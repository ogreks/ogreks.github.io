---
title: 深入理解PHP原理之文件上传（风雪之隅）
date: 2020-04-09 20:52:02
categories: 
 - PHP
 - 优秀文章
tags:
 - php
 - 文件上传
 - 上传原理
cover: https://s2.ax1x.com/2019/10/19/Kn0gZ4.png
copyright: false
---

## 深入理解PHP原理之文件上传

> @author 风雪之隅

* 本文地址: [https://www.laruence.com/2008/11/07/586.html](https://www.laruence.com/2008/11/07/586.html)
* 转载请注明出处

今天研究PHP注册POST/GET大变量的时候，看到了关于这块的一些东西，跟踪了半天，先记录下来，免得以后再如此麻烦的跟踪

处理器注册:

```
[mod_php5.c, mod_php5模块初始化] php_init_handler(server_rec *s, pool *p)
     ->[main/SAPI.c]sapi_startup(&apache_sapi_module)
          ->[main/SAPI.c] sapi_globals_ctor(&sapi_globals)
               ->[main/php_content_types.c]php_setup_sapi_content_types(TSRMLS_C)
                    ->[main/php_content_types.c php_post_entries如下]sapi_register_post_entries(php_post_entries TSRMLS_CC)
                         ->[main/SAPI.c]sapi_register_post_entry(p TSRMLS_CC)
```

如下面的代码，共注册了俩个处理器，分别处理post数据和文件上传。

> 注1:G(http_globals)\[TRACK_VARS_COOKIE]这部分,可以参看我较早前的 [在PHP Module中获取$_GET/$_POST/$_COOKIE的方法研究](http://www.laruence.com/2008/04/04/17.html)


``` 
[main/rfc1867.h]
      #define MULTIPART_CONTENT_TYPE "multipart/form-data"
  [main/php_content_types.h]
     #define DEFAULT_POST_CONTENT_TYPE "application/x-www-form-urlencoded"
  [main/SAPI.c]
     struct _sapi_post_entry {
         char *content_type;
         uint content_type_len;
         void (*post_reader)(TSRMLS_D);
         void (*post_handler)(char *content_type_dup, void *arg TSRMLS_DC);
     };
  [main/php_content_types.c]
     static sapi_post_entry php_post_entries[] = {
         { DEFAULT_POST_CONTENT_TYPE, sizeof(DEFAULT_POST_CONTENT_TYPE)-1, sapi_read_standard_form_data, php_std_post_handler },
         { MULTIPART_CONTENT_TYPE,    sizeof(MULTIPART_CONTENT_TYPE)-1,    NULL,                         rfc1867_post_handler },
         { NULL, 0, NULL, NULL }
};
```

那么对于rfc1867_post_handler函数来说,我罗列出源码如下, 加了我的注释.

```
SAPI_API SAPI_POST_HANDLER_FUNC(rfc1867_post_handler)
{
        char *boundary, *s=NULL, *boundary_end = NULL, *start_arr=NULL, *array_index=NULL;
        char *temp_filename=NULL, *lbuf=NULL, *abuf=NULL;
        int boundary_len=0, total_bytes=0, cancel_upload=0, is_arr_upload=0, array_len=0;
        int max_file_size=0, skip_upload=0, anonindex=0, is_anonymous;
        zval *http_post_files=NULL; HashTable *uploaded_files=NULL;
#if HAVE_MBSTRING && !defined(COMPILE_DL_MBSTRING)
        int str_len = 0, num_vars = 0, num_vars_max = 2*10, *len_list = NULL;
        char **val_list = NULL;
#endif
        zend_bool magic_quotes_gpc;
        multipart_buffer *mbuff;
        zval *array_ptr = (zval *) arg;
        int fd=-1;
        zend_llist header;
        void *event_extra_data = NULL;
        int llen = 0;
          //检查是否超出最大上传文件大小
        if (SG(request_info).content_length > SG(post_max_size)) {
                sapi_module.sapi_error(E_WARNING, "POST Content-Length of %ld bytes exceeds the limit of %ld bytes", SG(request_info).content_length, SG(post_max_size));
                return;
        }
        //取得上传文件的分隔符
        boundary = strstr(content_type_dup, "boundary");
        if (!boundary || !(boundary=strchr(boundary, '='))) {
                sapi_module.sapi_error(E_WARNING, "Missing boundary in multipart/form-data POST data");
                return;
        }
        boundary++;
        boundary_len = strlen(boundary);
        if (boundary[0] == '"') {
                boundary++;
                boundary_end = strchr(boundary, '"');
                if (!boundary_end) {
                        sapi_module.sapi_error(E_WARNING, "Invalid boundary in multipart/form-data POST data");
                        return;
                }
        } else {
                /* search for the end of the boundary */
                boundary_end = strchr(boundary, ',');
        }
        if (boundary_end) {
                boundary_end[0] = '';
                boundary_len = boundary_end-boundary;
        }
        /* Initialize the buffer */
        if (!(mbuff = multipart_buffer_new(boundary, boundary_len))) {
                sapi_module.sapi_error(E_WARNING, "Unable to initialize the input buffer");
                return;
        }
        //初始化$_FILE变量
        zend_hash_init(&PG(rfc1867_protected_variables), 5, NULL, NULL, 0);
        ALLOC_HASHTABLE(uploaded_files);
        zend_hash_init(uploaded_files, 5, NULL, (dtor_func_t) free_estring, 0);
        SG(rfc1867_uploaded_files) = uploaded_files;
        ALLOC_ZVAL(http_post_files);
        array_init(http_post_files);
        INIT_PZVAL(http_post_files);
        PG(http_globals)[TRACK_VARS_FILES] = http_post_files; //TRACK_VARS_FILE正是_FILE在php_core_globals.http_globals中的index (注1)
#if HAVE_MBSTRING && !defined(COMPILE_DL_MBSTRING)
        if (php_mb_encoding_translation(TSRMLS_C)) {
                val_list = (char **)ecalloc(num_vars_max+2, sizeof(char *));
                len_list = (int *)ecalloc(num_vars_max+2, sizeof(int));
        }
#endif
        zend_llist_init(&header, sizeof(mime_header_entry), (llist_dtor_func_t) php_free_hdr_entry, 0);
        if (php_rfc1867_callback != NULL) {
                multipart_event_start event_start;
                event_start.content_length = SG(request_info).content_length;
                if (php_rfc1867_callback(MULTIPART_EVENT_START, &event_start, &event_extra_data TSRMLS_CC) == FAILURE) {
                        goto fileupload_done;
                }
        }
        while (!multipart_buffer_eof(mbuff TSRMLS_CC))
        {
                char buff[FILLUNIT];
                char *cd=NULL,*param=NULL,*filename=NULL, *tmp=NULL;
                size_t blen=0, wlen=0;
                off_t offset;
                zend_llist_clean(&header);
                if (!multipart_buffer_headers(mbuff, &header TSRMLS_CC)) {
                        goto fileupload_done;
                }
                if ((cd = php_mime_get_hdr_value(header, "Content-Disposition"))) {
                        char *pair=NULL;
                        int end=0;
                        while (isspace(*cd)) {
                                ++cd;
                        }
                        while (*cd && (pair = php_ap_getword(&cd, ';')))
                        {
                                char *key=NULL, *word = pair;
                                while (isspace(*cd)) {
                                        ++cd;
                                }
                                if (strchr(pair, '=')) {
                                        key = php_ap_getword(&pair, '=');
                                        if (!strcasecmp(key, "name")) {
                                                if (param) {
                                                        efree(param);
                                                }
                                                param = php_ap_getword_conf(&pair TSRMLS_CC);
                                        } else if (!strcasecmp(key, "filename")) {
                                                if (filename) {
                                                        efree(filename);
                                                }
                                                filename = php_ap_getword_conf(&pair TSRMLS_CC);
                                        }
                                }
                                if (key) {
                                        efree(key);
                                }
                                efree(word);
                        }
                        /* Normal form variable, safe to read all data into memory */
                        if (!filename && param) {
                                unsigned int value_len;
                                char *value = multipart_buffer_read_body(mbuff, &value_len TSRMLS_CC);
                                unsigned int new_val_len; /* Dummy variable */
                                if (!value) {
                                        value = estrdup("");
                                }
                                if (sapi_module.input_filter(PARSE_POST, param, &value, value_len, &new_val_len TSRMLS_CC)) {
                                        if (php_rfc1867_callback != NULL) {
                                                multipart_event_formdata event_formdata;
                                                size_t newlength = 0;
                                                event_formdata.post_bytes_processed = SG(read_post_bytes);
                                                event_formdata.name = param;
                                                event_formdata.value = &value;
                                                event_formdata.length = new_val_len;
                                                event_formdata.newlength = &newlength;
                                                if (php_rfc1867_callback(MULTIPART_EVENT_FORMDATA, &event_formdata, &event_extra_data TSRMLS_CC) == FAILURE) {
                                                        efree(param);
                                                        efree(value);
                                                        continue;
                                                }
                                                new_val_len = newlength;
                                        }
                                        #if HAVE_MBSTRING && !defined(COMPILE_DL_MBSTRING)
                                        if (php_mb_encoding_translation(TSRMLS_C)) {
                                                php_mb_gpc_stack_variable(param, value, &val_list, &len_list,
                                                                                                  &num_vars, &num_vars_max TSRMLS_CC);
                                        } else {
                                                safe_php_register_variable(param, value, new_val_len, array_ptr, 0 TSRMLS_CC);
                                        }
                                        #else
                                        safe_php_register_variable(param, value, new_val_len, array_ptr, 0 TSRMLS_CC);
                                #endif
                                } else if (php_rfc1867_callback != NULL) {
                                        multipart_event_formdata event_formdata;
                                        event_formdata.post_bytes_processed = SG(read_post_bytes);
                                        event_formdata.name = param;
                                        event_formdata.value = &value;
                                        event_formdata.length = value_len;
                                        event_formdata.newlength = NULL;
                                        php_rfc1867_callback(MULTIPART_EVENT_FORMDATA, &event_formdata, &event_extra_data TSRMLS_CC);
                                }
                                if (!strcasecmp(param, "MAX_FILE_SIZE")) {
                                        max_file_size = atol(value);
                                }
                                efree(param);
                                efree(value);
                                continue;
                        }
                        /* If file_uploads=off, skip the file part */
                        if (!PG(file_uploads)) {
                                skip_upload = 1;
                        }
                        /* Return with an error if the posted data is garbled */
                        if (!param && !filename) {
                                sapi_module.sapi_error(E_WARNING, "File Upload Mime headers garbled");
                                goto fileupload_done;
                        }
                        if (!param) {
                                is_anonymous = 1;
                                param = emalloc(MAX_SIZE_ANONNAME);
                                snprintf(param, MAX_SIZE_ANONNAME, "%u", anonindex++);
                        } else {
                                is_anonymous = 0;
                        }
                        /* New Rule: never repair potential malicious user input */
                        if (!skip_upload) {
                                char *tmp = param;
                                long c = 0;
                                while (*tmp) {
                                        if (*tmp == '[') {
                                                c++;
                                        } else if (*tmp == ']') {
                                                c--;
                                                if (tmp[1] && tmp[1] != '[') {
                                                        skip_upload = 1;
                                                        break;
                                                }
                                        }
                                        if (c < 0) {
                                                skip_upload = 1;
                                                break;
                                        }
                                        tmp++;
                                }
                        }
                        total_bytes = cancel_upload = 0;
                        if (!skip_upload) {
                                /* Handle file */
                                fd = php_open_temporary_fd_ex(PG(upload_tmp_dir), "php", &temp_filename, 1 TSRMLS_CC);
                                if (fd==-1) {
                                        sapi_module.sapi_error(E_WARNING, "File upload error - unable to create a temporary file");
                                        cancel_upload = UPLOAD_ERROR_E;
                                }
                        }
                        if (!skip_upload && php_rfc1867_callback != NULL) {
                                multipart_event_file_start event_file_start;
                                event_file_start.post_bytes_processed = SG(read_post_bytes);
                                event_file_start.name = param;
                                event_file_start.filename = &filename;
                                if (php_rfc1867_callback(MULTIPART_EVENT_FILE_START, &event_file_start, &event_extra_data TSRMLS_CC) == FAILURE) {
                                        if (temp_filename) {
                                                if (cancel_upload != UPLOAD_ERROR_E) { /* file creation failed */
                                                        close(fd);
                                                        unlink(temp_filename);
                                                }
                                                efree(temp_filename);
                                        }
                                        temp_filename="";
                                        efree(param);
                                        efree(filename);
                                        continue;
                                }
                        }
                        if (skip_upload) {
                                efree(param);
                                efree(filename);
                                continue;
                        }
                        if(strlen(filename) == 0) {
                                #if DEBUG_FILE_UPLOAD
                                sapi_module.sapi_error(E_NOTICE, "No file uploaded");
                                #endif
                                cancel_upload = UPLOAD_ERROR_D;
                        }
                        offset = 0;
                        end = 0;
                        while (!cancel_upload && (blen = multipart_buffer_read(mbuff, buff, sizeof(buff), &end TSRMLS_CC)))
                        {
                                if (php_rfc1867_callback != NULL) {
                                        multipart_event_file_data event_file_data;
                                        event_file_data.post_bytes_processed = SG(read_post_bytes);
                                        event_file_data.offset = offset;
                                        event_file_data.data = buff;
                                        event_file_data.length = blen;
                                        event_file_data.newlength = &blen;
                                        if (php_rfc1867_callback(MULTIPART_EVENT_FILE_DATA, &event_file_data, &event_extra_data TSRMLS_CC) == FAILURE) {
                                                cancel_upload = UPLOAD_ERROR_X;
                                                continue;
                                        }
                                }
                                if (PG(upload_max_filesize) > 0 && total_bytes > PG(upload_max_filesize)) {
                                        #if DEBUG_FILE_UPLOAD
                                        sapi_module.sapi_error(E_NOTICE, "upload_max_filesize of %ld bytes exceeded - file [%s=%s] not saved", PG(upload_max_filesize), param, filename);
                                        #endif
                                        cancel_upload = UPLOAD_ERROR_A;
                                } else if (max_file_size && (total_bytes > max_file_size)) {
                                        #if DEBUG_FILE_UPLOAD
                                        sapi_module.sapi_error(E_NOTICE, "MAX_FILE_SIZE of %ld bytes exceeded - file [%s=%s] not saved", max_file_size, param, filename);
                                        #endif
                                        cancel_upload = UPLOAD_ERROR_B;
                                } else if (blen > 0) {
                                        wlen = write(fd, buff, blen);
                                        if (wlen == -1) {
                                                /* write failed */
                                                #if DEBUG_FILE_UPLOAD
                                                sapi_module.sapi_error(E_NOTICE, "write() failed - %s", strerror(errno));
                                                #endif
                                                cancel_upload = UPLOAD_ERROR_F;
                                        } else if (wlen < blen) {
                                                #if DEBUG_FILE_UPLOAD
                                                sapi_module.sapi_error(E_NOTICE, "Only %d bytes were written, expected to write %d", wlen, blen);
                                                #endif
                                                cancel_upload = UPLOAD_ERROR_F;
                                        } else {
                                                total_bytes += wlen;
                                        }
                                        offset += wlen;
                                }
                        }
                        if (fd!=-1) { /* may not be initialized if file could not be created */
                                close(fd);
                        }
                        if (!cancel_upload && !end) {
                                #if DEBUG_FILE_UPLOAD
                                sapi_module.sapi_error(E_NOTICE, "Missing mime boundary at the end of the data for file %s", strlen(filename) > 0 ? filename : "");
                                #endif
                                cancel_upload = UPLOAD_ERROR_C;
                        }
                        #if DEBUG_FILE_UPLOAD
                        if(strlen(filename) > 0 && total_bytes == 0 && !cancel_upload) {
                                sapi_module.sapi_error(E_WARNING, "Uploaded file size 0 - file [%s=%s] not saved", param, filename);
                                cancel_upload = 5;
                        }
                        #endif
                        if (php_rfc1867_callback != NULL) {
                                multipart_event_file_end event_file_end;
                                event_file_end.post_bytes_processed = SG(read_post_bytes);
                                event_file_end.temp_filename = temp_filename;
                                event_file_end.cancel_upload = cancel_upload;
                                if (php_rfc1867_callback(MULTIPART_EVENT_FILE_END, &event_file_end, &event_extra_data TSRMLS_CC) == FAILURE) {
                                        cancel_upload = UPLOAD_ERROR_X;
                                }
                        }
                        if (cancel_upload) {
                                if (temp_filename) {
                                        if (cancel_upload != UPLOAD_ERROR_E) { /* file creation failed */
                                                unlink(temp_filename);
                                        }
                                        efree(temp_filename);
                                }
                                temp_filename="";
                        } else {
                                zend_hash_add(SG(rfc1867_uploaded_files), temp_filename, strlen(temp_filename) + 1, &temp_filename, sizeof(char *), NULL);
                        }
                        /* is_arr_upload is true when name of file upload field
                         * ends in [.*]
                         * start_arr is set to point to 1st [
                         */
                        is_arr_upload = (start_arr = strchr(param,'[')) && (param[strlen(param)-1] == ']');
                        if (is_arr_upload) {
                                array_len = strlen(start_arr);
                                if (array_index) {
                                        efree(array_index);
                                }
                                array_index = estrndup(start_arr+1, array_len-2);
                        }
                        /* Add $foo_name */
                        if (llen < strlen(param) + MAX_SIZE_OF_INDEX + 1) {
                                llen = strlen(param);
                                lbuf = (char *) safe_erealloc(lbuf, llen, 1, MAX_SIZE_OF_INDEX + 1);
                                llen += MAX_SIZE_OF_INDEX + 1;
                        }
                        if (is_arr_upload) {
                                if (abuf) efree(abuf);
                                abuf = estrndup(param, strlen(param)-array_len);
                                snprintf(lbuf, llen, "%s_name[%s]", abuf, array_index);
                        } else {
                                snprintf(lbuf, llen, "%s_name", param);
                        }
                        #if HAVE_MBSTRING && !defined(COMPILE_DL_MBSTRING)
                        if (php_mb_encoding_translation(TSRMLS_C)) {
                                if (num_vars>=num_vars_max){
                                        php_mb_gpc_realloc_buffer(&val_list, &len_list, &num_vars_max,
                                                                                          1 TSRMLS_CC);
                                }
                                val_list[num_vars] = filename;
                                len_list[num_vars] = strlen(filename);
                                num_vars++;
                                if(php_mb_gpc_encoding_detector(val_list, len_list, num_vars, NULL TSRMLS_CC) == SUCCESS) {
                                        str_len = strlen(filename);
                                        php_mb_gpc_encoding_converter(&filename, &str_len, 1, NULL, NULL TSRMLS_CC);
                                }
                                s = php_mb_strrchr(filename, '\' TSRMLS_CC);
                                if ((tmp = php_mb_strrchr(filename, '/' TSRMLS_CC)) > s) {
                                        s = tmp;
                                }
                                num_vars--;
                                goto filedone;
                        }
                        #endif
                        /* The  check should technically be needed for win32 systems only where
                         * it is a valid path separator. However, IE in all it's wisdom always sends
                         * the full path of the file on the user's filesystem, which means that unless
                         * the user does basename() they get a bogus file name. Until IE's user base drops
                         * to nill or problem is fixed this code must remain enabled for all systems.
                         */
                        s = strrchr(filename, '\');
                        if ((tmp = strrchr(filename, '/')) > s) {
                                s = tmp;
                        }
                        #ifdef PHP_WIN32
                        if (PG(magic_quotes_gpc)) {
                                s = s ? s : filename;
                                tmp = strrchr(s, ''');
                                s = tmp > s ? tmp : s;
                                tmp = strrchr(s, '"');
                                s = tmp > s ? tmp : s;
                        }
                        #endif
                        #if HAVE_MBSTRING && !defined(COMPILE_DL_MBSTRING)
                        filedone:
                        #endif
                        if (!is_anonymous) {
                                if (s && s > filename) {
                                        safe_php_register_variable(lbuf, s+1, strlen(s+1), NULL, 0 TSRMLS_CC);
                                } else {
                                        safe_php_register_variable(lbuf, filename, strlen(filename), NULL, 0 TSRMLS_CC);
                                }
                        }
                        /* Add $foo[name] */
                        if (is_arr_upload) {
                                snprintf(lbuf, llen, "%s[name][%s]", abuf, array_index);
                        } else {
                                snprintf(lbuf, llen, "%s[name]", param);
                        }
                        if (s && s > filename) {
                                register_http_post_files_variable(lbuf, s+1, http_post_files, 0 TSRMLS_CC);
                        } else {
                                register_http_post_files_variable(lbuf, filename, http_post_files, 0 TSRMLS_CC);
                        }
                        efree(filename);
                        s = NULL;
                        /* Possible Content-Type: */
                        if (cancel_upload || !(cd = php_mime_get_hdr_value(header, "Content-Type"))) {
                                cd = "";
                        } else {
                                /* fix for Opera 6.01 */
                                s = strchr(cd, ';');
                                if (s != NULL) {
                                        *s = '';
                                }
                        }
                        /* Add $foo_type */
                        if (is_arr_upload) {
                                snprintf(lbuf, llen, "%s_type[%s]", abuf, array_index);
                        } else {
                                snprintf(lbuf, llen, "%s_type", param);
                        }
                        if (!is_anonymous) {
                                safe_php_register_variable(lbuf, cd, strlen(cd), NULL, 0 TSRMLS_CC);
                        }
                        /* Add $foo[type] */
                        if (is_arr_upload) {
                                snprintf(lbuf, llen, "%s[type][%s]", abuf, array_index);
                        } else {
                                snprintf(lbuf, llen, "%s[type]", param);
                        }
                        register_http_post_files_variable(lbuf, cd, http_post_files, 0 TSRMLS_CC);
                        /* Restore Content-Type Header */
                        if (s != NULL) {
                                *s = ';';
                        }
                        s = "";
                        /* Initialize variables */
                        add_protected_variable(param TSRMLS_CC);
                        magic_quotes_gpc = PG(magic_quotes_gpc);
                        PG(magic_quotes_gpc) = 0;
                        /* if param is of form xxx[.*] this will cut it to xxx */
                        if (!is_anonymous) {
                                safe_php_register_variable(param, temp_filename, strlen(temp_filename), NULL, 1 TSRMLS_CC);
                        }
                        /* Add $foo[tmp_name] */
                        if (is_arr_upload) {
                                snprintf(lbuf, llen, "%s[tmp_name][%s]", abuf, array_index);
                        } else {
                                snprintf(lbuf, llen, "%s[tmp_name]", param);
                        }
                        add_protected_variable(lbuf TSRMLS_CC);
                        register_http_post_files_variable(lbuf, temp_filename, http_post_files, 1 TSRMLS_CC);
                        PG(magic_quotes_gpc) = magic_quotes_gpc;
                        {
                                zval file_size, error_type;
                                error_type.value.lval = cancel_upload;
                                error_type.type = IS_LONG;
                                /* Add $foo[error] */
                                if (cancel_upload) {
                                        file_size.value.lval = 0;
                                        file_size.type = IS_LONG;
                                } else {
                                        file_size.value.lval = total_bytes;
                                        file_size.type = IS_LONG;
                                }
                                if (is_arr_upload) {
                                        snprintf(lbuf, llen, "%s[error][%s]", abuf, array_index);
                                } else {
                                        snprintf(lbuf, llen, "%s[error]", param);
                                }
                                register_http_post_files_variable_ex(lbuf, &error_type, http_post_files, 0 TSRMLS_CC);
                                /* Add $foo_size */
                                if (is_arr_upload) {
                                        snprintf(lbuf, llen, "%s_size[%s]", abuf, array_index);
                                } else {
                                        snprintf(lbuf, llen, "%s_size", param);
                                }
                                if (!is_anonymous) {
                                        safe_php_register_variable_ex(lbuf, &file_size, NULL, 0 TSRMLS_CC);
                                }
                                /* Add $foo[size] */
                                if (is_arr_upload) {
                                        snprintf(lbuf, llen, "%s[size][%s]", abuf, array_index);
                                } else {
                                        snprintf(lbuf, llen, "%s[size]", param);
                                }
                                register_http_post_files_variable_ex(lbuf, &file_size, http_post_files, 0 TSRMLS_CC);
                        }
                        efree(param);
                }
        }
        fileupload_done:
        if (php_rfc1867_callback != NULL) {
                multipart_event_end event_end;
                event_end.post_bytes_processed = SG(read_post_bytes);
                php_rfc1867_callback(MULTIPART_EVENT_END, &event_end, &event_extra_data TSRMLS_CC);
        }
        SAFE_RETURN;
}
```