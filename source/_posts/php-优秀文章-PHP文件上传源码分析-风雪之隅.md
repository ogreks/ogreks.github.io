---
title: PHP文件上传源码分析(RFC1867)
date: 2020-04-09 21:17:12
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

## PHP文件上传源码分析

> @author 风雪之隅 

* 本文地址: [https://www.laruence.com/2009/09/26/1103.html](https://www.laruence.com/2009/09/26/1103.html)
* 转载请注明出处

文件上传,一般分为俩种方式FTP和HTTP, 对于我们的互联网应用来说: FTP上传虽然传输稳定, 但是易用性和安全性都是个问题. 
你总不至于在用户要上传头像的时候告诉用户"请打开FTP客户端,上传文件到http://www.laruence.com/uploads/中, 并以2dk433423l.jpg命名"吧?
而基于HTTP的上传,相对来说易用性和安全性上就比FTP要增强了很多. 
可以应用的上传方式有PUT, WEBDAV, 和RFC1867三种, 
本文将分析在PHP中,是如何基于RFC1867实现文件上传的.

### RFC1867

RCF1867是Form-based File Upload in HTML标准协议, RFC1867标准对HTML做出了两处修改：

```
1 为input元素的type属性增加了一个file选项。
2 input标记可以具有accept属性，该属性能够指定可被上传的文件类型或文件格式列表。
```

另外，本标准还定义了一种新的mime类型：multipart/form-data，
以及当处理一个带有enctype="multipart/form-data" 
并且/或含有<input type="file">的标记的表单时所应该采取的行为。  
　　
举例来说，当HTML想让用户能够上传一个或更多的文件时，他可以这么写：

```
<form enctype="multipart/form-data" action="upload.php" method=post>
选择文件:
<input name="userfile" type="file">
文件描述:
<input name="description" type="text">
<input type="submit" value="上传">
</form>
```

这个表单, 大家一定不陌生, 而对于PHP来说, 它自己另外定义了一个默认表单元素MAX_FILE_SIZE, 
用户可以通过这个隐藏的表单元素来建议PHP最多只容许上传文件的大小, 
比如对于上面的例子, 我们希望用户上传的文件不能大于5000(5k)字节, 
那么可以如下写:  

``` 
<form enctype="multipart/form-data" action="upload.php" method=post>
<input type="hidden" value="5000" name="MAX_FILE_SIZE"> <!--文件大小-->
选择文件:
<input name="userfile" type="file">
文件描述:
<input name="description" type="text">
<input type="submit" value="上传">
</form>
```

姑且不说, 这个MAX_FILE_SIZE是多么的不可靠(所以基于浏览器的控制,都是不可靠的), 我们单纯从实现来介绍这个MAX_FILE_SIZE是如何起作用的.
当用户选择了一个文件(laruence.txt), 并填写好文件描述("laruence的个人介绍"), 点击上传后, 发生了什么呢?

### 表单提交

在用户确定提交以后, 浏览器会根据用户选择的输入, 读取要上传的文件, 连同表单中的其他元素, 
组织成一定格式(如下)的数据发送到form中action属性指定的页面(在本例中是upload.php):

``` 
//请求头
POST /upload.php HTTP/1.0\r\n
...
Host: www.laruence.com\r\n
...
Content-length: xxxxx\r\n
...
Content-type: multipart/form-data, boundary=7d51863950254\r\n
...\r\n\r\n
//开始POST数据内容
--7d51863950254
content-disposition: form-data; name="description"\r\n
laruence的个人介绍
--7d51863950254
content-disposition: form-data; name="userfile"; filename="laruence.txt"
Content-Type: text/plain\r\n
... laruence.txt 的内容...
--7d51863950254--
```

接下来, 就是服务器, 是如何处理这些数据了

### 接受上传

当Web服务器, 此处假设为Apache(另外假设PHP是以module方式安装在Apache上的), 接受到用户的数据时, 
首先它根据HTTP请求头, 通过确定MIME TYPE为PHP类型, 然后经过一些过程以后
(这部分,可以参看我之前的[PHP Life Cycle ppt](http://www.laruence.com/2008/08/15/283.html)), 最终会把控制权交给PHP模块.  

这个时候, PHP会调用sapi_activate来初始化一个请求, 
在这个过程中, 首先判断请求类型, 此时是POST, 从而去调用sapi_read_post_data, 通过Content-type, 找到rfc1867的处理函数rfc1867_post_handler, 
从而调用这个handler, 来分析POST来的数据.

关于rfc1867_post_handler这部分的源代码, 可以在mian/rfc1867.c找到, 
另外也可以参看我之前的[深入理解PHP之文件上传](/2020/04/10/php-优秀文章-深入理解PHP原理之文件上传-风雪之隅/), 其中也列出的源代码.

然后, PHP通过boundary, 对于每一个分段, 都通过检查, 是否同时定义了:

```
 name和filename属性(有名文件上传)
 没有定义name定义了filename(无名上传)
 定义了name没有定义filename(普通数据),
```

从而进行不同的处理.

```
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
                    //获取name字段
                    if (param) {
                         efree(param);
                    }
                    param = php_ap_getword_conf(&pair TSRMLS_CC);
               } else if (!strcasecmp(key, "filename")) {
                    //获取filename字段
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
}
```

在这个过程中, PHP会去检查普通数据中,是否有MAX_FILE_SIZE.

``` 
/* Normal form variable, safe to read all data into memory */
if (!filename && param) {
     unsigned int value_len;
     char *value = multipart_buffer_read_body(mbuff, &value_len TSRMLS_CC);
     unsigned int new_val_len; /* Dummy variable */
     ......
     if (!strcasecmp(param, "MAX_FILE_SIZE")) {
                  max_file_size = atol(value);
    }
     efree(param);
     efree(value);
     continue;
}
```

有的话, 就会按照它的值来检查文件大小是否超出.

```
if (PG(upload_max_filesize) > 0 && total_bytes > PG(upload_max_filesize)) {
     cancel_upload = UPLOAD_ERROR_A;
} else if (max_file_size && (total_bytes > max_file_size)) {
#if DEBUG_FILE_UPLOAD
     sapi_module.sapi_error(E_NOTICE,
          "MAX_FILE_SIZE of %ld bytes exceeded - file [%s=%s] not saved",
           max_file_size, param, filename);
#endif
     cancel_upload = UPLOAD_ERROR_B;
}
```

通过上面的代码,我们也可以看到, 判断分为俩部, 

* 第一部分是检查PHP默认的上传上限. 
* 第二部分才是检查用户自定义的MAX_FILE_SIZE, 

所以表单中定义的MAX_FILE_SIZE并不能超过PHP中设置的最大上传文件大小.

通过对name和filename的判断, 如果是文件上传, 会根据php的设置, 在文件上传目录中创建一个随机名字的临时文件:

```
if (!skip_upload) {
     /* Handle file */
     fd = php_open_temporary_fd_ex(PG(upload_tmp_dir),
                "php", &temp_filename, 1 TSRMLS_CC);
     if (fd==-1) {
          sapi_module.sapi_error(E_WARNING,
                "File upload error - unable to create a temporary file");
          cancel_upload = UPLOAD_ERROR_E;
     }
}
```

返回文件句柄, 和临时随机文件名.
之后, 还会有一些验证,比如文件名合法, name合法等.
如果这些验证都通过, 那么就把内容读入, 写入到这个临时文件中.

``` 
.....
else if (blen > 0) {
     wlen = write(fd, buff, blen); //写入临时文件.
     if (wlen == -1) {
     /* write failed */
#if DEBUG_FILE_UPLOAD
     sapi_module.sapi_error(E_NOTICE, "write() failed - %s", strerror(errno));
#endif
     cancel_upload = UPLOAD_ERROR_F;
     }
}
....
```

当循环读入完成后, 关闭临时文件句柄. 记录临时变量名:

```
zend_hash_add(SG(rfc1867_uploaded_files), temp_filename,
     strlen(temp_filename) + 1, &temp_filename, sizeof(char *), NULL);
```

并且生成FILE变量, 这个时候, 如果是有名上传, 那么就会设置:

```
$_FILES['userfile'] //name="userfile"
```

如果是无名上传, 则会使用tmp_name来设置:

```
$_FILES['tmp_name'] //无名上传
```

最终交给用户编写的upload.php处理.
这时在upload.php中, 用户就可以通过move_uploaded_file来操作刚才生成的文件了~