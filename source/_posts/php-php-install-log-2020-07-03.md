---
title: 关于php 编译安装 遇到的坑总结
date: 2020-07-04 02:16:04
categories: 
 - PHP
tags:
 - php
 - php 安装
 - 编译安装
 - 错误排坑
 - php 编译错误解决  
cover: https://s2.ax1x.com/2019/10/19/Kn0gZ4.png
---

## 关于php 编译安装 遇到的坑总结

> 每一次记录都是成长的过程

因为公司服务器问题，我们临时打算将测试环境的php 环境 先升级到 7.0 左右 ，然后我就接下了这个坑。

我的系统环境：
```
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=14.04
DISTRIB_CODENAME=trusty
DISTRIB_DESCRIPTION="Ubuntu 14.04.6 LTS"

Linux iZbp13psr6fb9tfovzicnmZ 3.2.0-126-generic #169-Ubuntu SMP Fri Mar 31 14:15:21 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```


话不多说，首先贴出我这次遇到的问题

* 问题一：

```
configure: error: xslt-config not found. Please reinstall the libxslt >= 1.1.0 distribution
```

* 问题二：
```
E: Unable to locate package libxslt1-devel
```
* 问题三：
```
php 7.0 make: *** [ext/gd/libgd/gd_png.lo] error 1
```
* 问题四：
```
collect2: error: ld returned 1 exit status
make: *** [sapi/cli/php] Error 1
```

具体问题 我就不过多阐述了，上述问题也不是最终的问题指向，其他因为扩展的问题，我也不多说了。总之一句话，直接升级除了坑就是麻烦

### 老系统建议直接升级

* 问题一、问题二


这个问题其实很简单，但是对于 一个 浅度的 Ubuntu 用户来说，其实这是致命的一个问题

看到这个问题 我们可以直接去 用系统的命令去安装，可是 ubuntu 使用 apt-get install 的时候 就会出现第二个问题。

发生这种情况的时候，我们有两个解决方案 

1. 直接去 google 上面 搜索对应的扩展包，执行编译 安装到系统
2. 搜索大佬的解决方案

本着能偷懒就偷懒的性格，我选择了第二个方案

然后 我就看到各种 关于 ubuntu 的系统 search package 的解决方案

有 提示 让我直接 apt-get update 的。（公司服务器 阿里云）
当我直接 更新后，第二个问题基本没有解决，而且，还更严重了，直接 就是 对应的 libgd 的扩展 全部都找不到了

这让我如何是好啊。

```
sudo apt-get install libbz2-dev libcurl4-gnutls-dev libssl-dev libc-client2007e libc-client2007e-dev libcurl4-openssl-dev libpq-dev libxslt1-dev libxpm-dev build-essential
```

然后我发现 在 ubuntu 上 需要这样去找 你敢信 ***libbz2-dev*** ***libxslt1-dev*** 还有先后顺序

不过好在 问题 顺利解决了

### 魔鬼出在细节里

为了保证 有一个 良好的周六日 不会被公司的小伙伴们 用远程电话叫醒 这次安装环境 原先的环境 我并没有删除

然后 ***问题三*** 出现了 （原报错非常多，没有一一记录）

大致的问题就是 我使用的 ***gd_png*** 出现了不可知的意外错误

看到这里 我已经开始怀疑 php 关于gd 库的bug 是不是没有解决的问题了。熟练的上 [php.net](php.net) 搜索了一会儿 版本记录。发现这个问题已经被解决了。那就很奇怪，我这里的问题又是怎么发生的呢？

上面的 php 环境没有删除 划重点 <\\-_-/>

仔细 看了看错误信息 因为 ./configure 已经通过了 现在是 make 失败 说明 php 不存在 bug 那么多半出在 扩展上

> ext/gd/libgd/gd_png.c

我根据提示错误信息 找到了 对应文件 然后 搜索了自己的系统 发现存在多个 png.h 的文件 

难道是 png.h 引用错误？

![NvMe0J.png](https://s1.ax1x.com/2020/07/04/NvMe0J.png)

上面图片 请根据 自己系统中的文件 更换 绝对路径

### 发生了问题，多半都是基础知识的复习

当解决掉一大堆问题之后，我们终于来到了最后一步，看着终端不停的输出 我甚至在 make 后面 加上了 j4

然后就发生了第四个问题

> 这次的报错还是 gd 库的问题

无奈，我们已经排除了所有的答案，但是 这个错误很迷惑 因为它涉及到了 sapi 的问题 说明 已经进入了编译的后半段阶段，按道理来说 gd 库已经编译成功了，应该已经进入 其他阶段了，请看下面的示意图：

![NvM2As.png](https://s1.ax1x.com/2020/07/04/NvM2As.png)

在安裝 PHP 到系统中时要是发生「undefined reference to libiconv_open'」之类的错误信息，那表示在「./configure 」沒抓好一些环境变数值。错误发生点在建立「-o sapi/cli/php」是出错，没給到要 link 的 iconv 函式库参数。 解决方法：编辑Makefile 大约77 行左右的地方: EXTRA_LIBS = ..... -lcrypt 在最后加上 -liconv，例如: EXTRA_LIBS = ..... -lcrypt -liconv 然后重新再次 make 即可。


或者用另一种办法

make ZEND_EXTRA_LIBS='-liconv'

ln -s /usr/local/lib/libiconv.so.2 /usr/lib64/

记得先make clean一下


### 成功 

```
root@iZbp13psr6fb9tfovzicnmZ:/alidata/server/php-7.0.23# make install
Installing shared extensions:     /alidata/server/php70/lib/php/extensions/no-debug-non-zts-20151012/
Installing PHP CLI binary:        /alidata/server/php70/bin/
Installing PHP CLI man page:      /alidata/server/php70/php/man/man1/
Installing PHP FPM binary:        /alidata/server/php70/sbin/
Installing PHP FPM defconfig:     /alidata/server/php70/etc/
Installing PHP FPM man page:      /alidata/server/php70/php/man/man8/
Installing PHP FPM status page:   /alidata/server/php70/php/php/fpm/
Installing phpdbg binary:         /alidata/server/php70/bin/
Installing phpdbg man page:       /alidata/server/php70/php/man/man1/
Installing PHP CGI binary:        /alidata/server/php70/bin/
Installing PHP CGI man page:      /alidata/server/php70/php/man/man1/
Installing build environment:     /alidata/server/php70/lib/php/build/
Installing header files:          /alidata/server/php70/include/php/
Installing helper programs:       /alidata/server/php70/bin/
  program: phpize
  program: php-config
Installing man pages:             /alidata/server/php70/php/man/man1/
  page: phpize.1
  page: php-config.1
Installing PEAR environment:      /alidata/server/php70/lib/php/
[PEAR] Archive_Tar    - installed: 1.4.3
[PEAR] Console_Getopt - installed: 1.4.1
[PEAR] Structures_Graph- installed: 1.1.1
[PEAR] XML_Util       - installed: 1.4.2
[PEAR] PEAR           - installed: 1.10.5
Wrote PEAR system config file at: /alidata/server/php70/etc/pear.conf
You may want to add: /alidata/server/php70/lib/php to your php.ini include_path
/alidata/server/php-7.0.23/build/shtool install -c ext/phar/phar.phar /alidata/server/php70/bin
ln -s -f phar.phar /alidata/server/php70/bin/phar
Installing PDO headers:           /alidata/server/php70/include/php/ext/pdo/
```

### 附属链接-帮助记录

软件包: libxslt1-dev (1.1.28-2.1ubuntu0.3 以及其他的) [security]

[https://packages.ubuntu.com/xenial/libxslt1-dev](https://packages.ubuntu.com/xenial/libxslt1-dev)

Ubuntu 16.04 编译安装Nginx-1.10.3、 PHP7.0.9、Redis3.0 扩展、Phalcon3.1 扩展、Swoole1.9.8 扩展、ssh2扩展（全程编译安装）
[https://www.cnblogs.com/tinywan/p/6607395.html](https://www.cnblogs.com/tinywan/p/6607395.html)

#### 下载必要软件包

1. libxml2-2.6.30.tar.gz （一个xml c语言版的解析器）
ftp://ftp.gnome.org/pub/GNOME/sources/libxml2/2.6/libxml2-2.6.30.tar.gz
2. libmcrypt-2.5.8.tar.gz （php中mcrypt扩展,libmcrypt是加密算法扩展库）
http://prdownloads.sourceforge.net/mcrypt/libmcrypt-2.5.8.tar.gz?use_mirror=peterhost
3. zlib-1.2.7.tar.gz （提供数据压缩用的函式库，为安装gd库做准备）
http://zlib.net/fossils/zlib-1.2.7.tar.gz
4. libpng-1.2.31.tar.gz （软件包包含 libpng 库.这些库被其他程式用于读写png文件，为安装gd库做准备）
ftp://194.54.81.27/services/customapache/libpng-1.2.31.tar.gz
5. jpegsrc.v6b.tar.gz （为安装gd库做准备）
http://www.ijg.org/files/jpegsrc.v6b.tar.gz
6. freetype-2.3.5.tar.gz （字体库文件，为GD库做准备） http://download.savannah.gnu.org/releases/freetype/freetype-2.3.5.tar.gz
7. autoconf-2.61.tar.gz （是用来产生configure文件的。）
ftp://ftp.gnu.org/gnu/autoconf/autoconf-2.61.tar.gz
8. libgd-2.1.0.tar.gz（是php处理图形的扩展库）
https://bitbucket.org/libgd/gd-libgd/downloads/libgd-2.1.0.tar.gz
9. ngnix
http://nginx.org/download/nginx-1.0.15.tar.gz
10. pcre （一个正则表达式库，nginx伪静态可以用到）
http://ftp.exim.llorien.org/pcre/pcre-8.36.tar.gz
11. mysql
http://mysql.mirror.kangaroot.net/Downloads/MySQL-5.6/mysql-5.6.27.tar.gz
12. php
http://cn2.php.net/get/php-5.6.9.tar.gz/from/a/mirror
13. cmake （编译mysql）
http://www.cmake.org/files/v3.2/cmake-3.2.2.tar.gz
14. zend guard （php脚本加密）
http://downloads.zend.com/guard/5.5.0/ZendGuardLoader-php-5.3-linux-glibc23-i386.tar.gz
下载apache
http://www.apache.org/dist/httpd/httpd-2.4.17.tar.gz
下载apr（Apache库文件）
http://mirror.bit.edu.cn/apache/apr/apr-1.5.2.tar.gz
下载apr-util（Apache库文件）
http://mirror.bit.edu.cn/apache/apr/apr-util-1.5.4.tar.gz
php7
https://github.com/php/php-src/archive/php-7.0.0.tar.gz

#### 一些排错的坑

```
make && makeinstall
```

**错误处理：**

```
make[1]: *** [nanohttp.lo] Error 1打开目录下的nanohttp.c，第1588行由
fd = open(filename, O_CREAT | O_WRONLY);更换为 fd = open(filename, O_CREAT | O_WRONLY,0777);
```

* cd /usr/local/src/libmcrypt-2.5.8
./configure --prefix=/usr/local/libmcrypt
出现错误：
checking for C++ compiler default output file name... configure: error: C++ compiler cannot create executablesSee \`config.log' for more details.
解决办法：
sudo apt-get install g++
make && make install

##### cd /usr/local/src/zlib-1.2.3
```
./configure --prefix=/usr/local/zlib
make && make install
```

##### cd /usr/local/src/libpng-1.2.3
```
./configure --prefix=/usr/local/libpng
make && make install
```

##### cd /usr/local/src/zlib-1.2.3
```
./configure --prefix=/usr/local/zlib
make && make install
```
**在安装过程中会出现如下错误：**
```
error: zlib not installed 
```
**解决的办法是：**
```
cd /usr/local/src/zlib-1.2.3 
make clean 清除上一次make产生的文件，然后 ./configure make && make install 
cd /usr/local/src/libpng-1.2.3
./configure --prefix=/usr/local/libpng
make && make install
```

##### 安装jpeg6库文件
```
mkdir /usr/local/jpeg6
mkdir /usr/local/jpeg6/bin
mkdir /usr/local/jpeg6/lib
mkdir /usr/local/jpeg6/include
mkdir  -p /usr/local/jpeg6/man/man1
cd /usr/local/src/jpeg6/jpeg-6b
./configure --prefix=/usr/local/jpeg6 
--enable-shared    建立共享库使用的GNU libtool
--enable-static      建立静态库使用的libtool
make && make insall 
```

##### cd  /usr/local/src/freetype-2.3.5
```
./configure --prefix=/usr/local/freetype
make && make install 
```

##### cd /usr/local/src/autoconf-2.61
```
./configure make && makeinstall
```


##### 安装GD库
```
cd /usr/local/src/libgd-2.1.0
./configure \
 --prefix=/usr/local/gd2/  \
 --with-zlib=/usr/local/zlib/ \
 --with-jpeg=/usr/local/jpeg6/ \
 --with-png=/usr/local/libpng/ \
 --with-freetype=/usr/local/freetype/ 
make && make install 
```
**以上子make过程中会出现如下错误：**
```
“gd_png.c:16:53: error: png.h: No such file or directory
gd_png.c:47: error: expected specifier-qualifier-list before 'jmp_buf'
gd_png.c:54: error: expected ')' before 'png_ptr'
gd_png.c:82: error: expected ')' before 'png_ptr'
gd_png.c:92: error: expected ')' before 'png_ptr'
gd_png.c:98: error: expected ')' before 'png_ptr'”
```

处理方法：
vi gd_png.c
将#include “png.h”             
替换成：#include “/usr/local/libpng/include/png.h”             
然后再make就可以了
注：include“”双引号里包含的是libpng安装的路径里的include文件夹里的png.h文件


##### cmake
```
cd /usr/local/src
tar zxvf cmake-2.8.9.tar.gz
cd cmake-2.8.9
./configure
make && make install
```

##### pcre
```
cd /usr/local/src
mkdir /usr/local/pcre #创建安装目录
tar zxvf pcre-8.30.tar.gz
cd pcre-8.30
./configure --prefix=/usr/local/pcre #配置
make
make install
```

##### 安装apr
```
cd /usr/local/src
tar zxvf  apr-1.4.6.tar.gzcd apr-1.4.6
./configure --prefix=/usr/local/apr makemake install
```
##### 安装apr-util
```
tar zxvf apr-util-1.4.1.tar.gz
cd apr-util-1.4.1./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr/bin/apr-util
./configure make && make install 
```

##### mysql
* groupadd mysql #添加mysql组
* useradd -g mysql mysql -s /bin/false #创建用户mysql并加入到mysql组，不允许mysql用户直接登录系统
* mkdir -p /data/mysql #创建MySQL数据库存放目录
* chown -R mysql:mysql /data/mysql #设置MySQL数据库目录权限
* mkdir -p /usr/local/mysql #创建MySQL安装目录
``` 
cd /usr/local/src
tar zxvf mysql-5.5.27.tar.gz
cd mysql-5.5.27
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/data/mysql -DSYSCONFDIR=/etc #配置 
```
**出现如下错误：**
```
CMake Error at cmake/readline.cmake:83 (MESSAGE):
  ...
  cmake/readline.cmake:127 (FIND_CURSES)
  cmake/readline.cmake:217 (MYSQL_USE_BUNDLED_LIBEDIT)
  CMakeLists.txt:268 (MYSQL_CHECK_READLINE)
  Configuring incomplete, errors occurred!
 如回显所示，ubuntu下安装libncurses5-dev；redhat下安装ncurses-devel，并删除当前目录CMakeCache.txt(必须删除，否则报错依旧)并重新运行：$ cmake .
```
**解决：**
1. remove CMakeCache.txt；
2. yum install ncurses-devel  或 sudo apt-get install libncurses5-dev
3. 重新运行cmake  .

make && make install
```
* 配置mysql: cd /usr/local/mysql
cp ./support-files/my-huge.cnf /etc/my.cnf 

#拷贝配置文件（注意：如果/etc目录下面默认有一个my.cnf，直接覆盖即可）

vi /etc/my.cnf 
#编辑配置文件,在 [mysqld] 部分增加 datadir = /data/mysql 
#添加MySQL数据库路径
./scripts/mysql_install_db --user=mysql 
#生成mysql系统数据库
cp ./support-files/mysql.server /etc/init.d/mysqld 
#把Mysql加入系统启动
chmod 755 /etc/init.d/mysqld 
#增加执行权限
chkconfig mysqld on 
#加入开机启动
vi /etc/init.d/mysqld 
#编辑
basedir = /usr/local/mysql 
#MySQL程序安装路径
datadir = /data/mysql 
#MySQl数据库存放目录
service mysqld start 
#启动
vi /etc/profile 
#把mysql服务加入系统环境变量：在最后添加下面这一行 export PATH=$PATH:/usr/local/mysql/bin

下面这两行把myslq的库文件链接到系统默认的位置，这样你在编译类似PHP等软件时可以不用指定mysql的库文件地址。
ln -s /usr/local/mysql/lib/mysql /usr/lib/mysql
ln -s /usr/local/mysql/include/mysql /usr/include/mysql

shutdown -r now 
#需要重启系统，等待系统重新启动之后继续在终端命令行下面操作

mysql_secure_installation 
#设置Mysql密码

根据提示按Y 回车输入2次密码

或者直接修改密码/usr/local/mysql/bin/mysqladmin -u root -p password "123456" 
#修改密码
service mysqld restart 

#重启

到此，mysql安装完成！
```

##### 安装apache2
```
cd /usr/local/srctar -zvxf httpd-2.4.1.tar.gzcd  httpd-2.4.1mkdir -p /usr/local/apache2  
#创建安装目录1 
```

 * 配置一：
```
./configure --prefix=/usr/local/apache2 --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util --with-ssl --enable-ssl --enable-module=so --enable-rewrite --enable-cgid --enable-cgi --with-pcre=/usr/local/pcre
```

* 配置二：
``` 
./configure --prefix=/usr/local/apache2 --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util --with-zlib=/usr/local/zlib --with-pcre=/usr/local/pcre --enable-so --enable-rewrite=shared --enable-deflate=shared --enable-expires=shared --enable-static-support 
make   && make install
```
* 配置apache
```
/usr/local/apache2/bin/apachectl -k start  #启动
vi /usr/local/apache2/conf/httpd.conf   #编辑配置文件
找到：#ServerName www.example.com:80
修改为：ServerName www.osyunwei.com:80
找到：DirectoryIndex index.html
修改为：DirectoryIndex index.html index.php
找到：Options Indexes FollowSymLinks
修改为：Options FollowSymLinks    #不显示目录结构
找到AllowOverride None 
修改为：AllowOverride All   #开启apache支持伪静态，有两处都做修改
LoadModule rewrite_module modules/mod_rewrite.so   #取消前面的注释，开启apache支持伪静态
vi /etc/profile  #添加apache服务系统环境变量
在最后添加下面这一行
export PATH=$PATH:/usr/local/apache2/bin
cp /usr/local/apache2/bin/apachectl /etc/rc.d/init.d/httpd      #把apache加入到系统启动
vi /etc/init.d/httpd   #编辑文件
在#!/bin/sh下面添加以下两行
chkconfig:2345 10 90
descrption:Activates/Deactivates Apache Web Server
chown  daemon.daemon  -R /usr/local/apache2/htdocs  #更改目录所有者
chmod   700 /usr/local/apache2/htdocs  -R #更改apache网站目录权限
chkconfig httpd on    #设置开机启动
/etc/init.d/httpd start
service httpd restart
```

##### nginx
* groupadd www
* useradd -g www -s /bin/false www
* cd /usr/local/src/nginx-1.2.6

```
./configure \
--prefix=/usr/local/nginx \ 
--with-pcre=/usr/local/src/pcre-8.21\   //pcre的源代码路径，而不是安装路径
--conf-path=/usr/local/nginx/nginx.conf \ 
--pid-path=/usr/local/nginx/nginx.pid \ 
--with-zlib=/usr/local/src/zlib-1.2.7 \  //zlib的源代码路径
--with-http_stub_status_module --user=www --group=www
make && make install
```

##### php7 
```
cd /usr/local/src
tar -zvxf php-7.0.0.tar.gz
cd php-7.0.0
mkdir -p /usr/local/php7 #建立php安装目录 
编译安装php配合apache和nginx
 sudo ./configure --prefix=/usr/local/php --with-apxs2=/usr/local/apache2/bin/apxs --with-config-file-path=/usr/local/php  --with-mysql=/usr/local/mysql --with-mysqli=/usr/local/mysql/bin/mysql_config  --with-libxml-dir=/usr/local/libxml2 --with-gd=/usr/local/libgd2 --with-jpeg-dir=/usr/local/jpeg-9a  --with-png-dir=/usr/local/libpng --with-bz2 --with-freetype-dir=/usr/local/freetype-2.4  --with-iconv-dir --with-pcre-dir=/usr/local/pcre --with-zlib-dir=/usr/local/zlib --with-libzip=/usr/local/libzip --enable-zip --with-openssl --with-mcrypt=/usr/local/libmcrypt --with-xmlrpc --with-curl --with-gettext --enable-xml --enable-mbregex  --enable-soap --enable-gd-native-ttf --enable-ftp --enable-mbstring --enable-exif --enable-safe-mode --enable-sockets --enable-session --enable-magic-quotes --enable-bcmath --disable-rpath --disable-debug --disable-ipv6 --enable-fastcgi --enable-fpm --without-pear --enable-opcache=no  --with-xpm-dir=/usr/lib/i386-linux-gnu/
**选项--with-apxs2=/usr/local/apache2/bin/apxs支持apache
选项 --enable-fastcgi 和 --enable-fpm 支持nginx**
make && make install
```

> make 过程中提示：
In file included from /usr/local/packages/php-5.6.9/ext/zip/lib/zip_add.c:37:/usr/local/packages/php-5.6.9/ext/zip/lib/zipint.h:118:2: error: #error unsupported size of off_tmake: *** [ext/zip/lib/zip_add.lo] Error 1
是因为　--with-zlib-dir=DIR    --with-pcre-dir=DIR    --with-libzip=DIR  , zip都基于这三个模块，后来瞎试试发现装好后在./configure的参数加上这三个，然后成了……哎，php5.6以上才会发生这样的问题，以下貌似随便搞。


* 配置php 
```
cp php.ini-production /usr/local/php5/etc/php.ini #复制php配置文件到安装目录**
rm -rf /etc/php.ini #删除系统自带配置文件
ln -s /usr/local/php5/etc/php.ini /etc/php.ini #添加软链接
cp /usr/local/php5/etc/php-fpm.conf.default /usr/local/php5/etc/php-fpm.conf #拷贝模板文件为php-fpm配置文件
vi /usr/local/php5/etc/php-fpm.conf #编辑
user = www #设置php-fpm运行账号为www
group = www #设置php-fpm运行组为www
pid = run/php-fpm.pid #取消前面的分号
设置 php-fpm开机启动
cp /usr/local/src/php-5.3.16/sapi/fpm/init.d.php-fpm /etc/rc.d/init.d/php-fpm #拷贝php-fpm到启动目录
chmod +x /etc/rc.d/init.d/php-fpm #添加执行权限
chkconfig php-fpm on #设置开机启动
vi /usr/local/php5/etc/php.ini #编辑配置文件
找到：;open_basedir =
修改为：open_basedir = .:/tmp/ #防止php木马跨站，重要！！
找到：disable_functions =
修改为：disable_functions = passthru,exec,system,chroot,scandir,chgrp,chown,shell_exec,proc_open,proc_get_status,ini_alter,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server,escapeshellcmd,dll,popen,disk_free_space,checkdnsrr,checkdnsrr,getservbyname,getservbyport,disk_total_space,posix_ctermid,posix_get_last_error,posix_getcwd, posix_getegid,posix_geteuid,posix_getgid, posix_getgrgid,posix_getgrnam,posix_getgroups,posix_getlogin,posix_getpgid,posix_getpgrp,posix_getpid, posix_getppid,posix_getpwnam,posix_getpwuid, posix_getrlimit, posix_getsid,posix_getuid,posix_isatty, posix_kill,posix_mkfifo,posix_setegid,posix_seteuid,posix_setgid, posix_setpgid,posix_setsid,posix_setuid,posix_strerror,posix_times,posix_ttyname,posix_uname
列出PHP可以禁用的函数，如果某些程序需要用到这个函数，可以删除，取消禁用。
找到：;date.timezone =
修改为：date.timezone = PRC #设置时区
找到：expose_php = On
修改为：expose_php = OFF #禁止显示php版本的信息
找到：display_errors = On
修改为：display_errors = OFF #关闭错误提示
```

##### 配置apache支持php
* vi /usr/local/apache2/conf/httpd.conf  #编辑apache配置文件
* 在LoadModule php5_module        modules/libphp5.so这一行下面添加、
* AddType application/x-httpd-php .php  （注意：php .php这个点前面有一个空格）
* service httpd restart    #重启apache
* service mysqld restart   #重启mysql
* 测试篇

```
cd  /usr/local/apache2/htdocsvi index.php   #输入下面内容
<?php
phpinfo();
?>
```

##### 配置php支持Zend Guard

* 安装Zend Guard

```
cd /usr/local/src
mkdir /usr/local/zend #建立Zend安装目录
tar xvfz ZendGuardLoader-php-5.3-linux-glibc23-i386.tar.gz #解压安装文件
cp ZendGuardLoader-php-5.3-linux-glibc23-i386/php-5.3.x/ZendGuardLoader.so /usr/local/zend/ #拷贝文件到安装目录
vi /usr/local/php5/etc/php.ini #编辑文件
在最后位置添加以下内容
[Zend Guard]
zend_extension=/usr/local/zend/ZendGuardLoader.so
zend_loader.enable=1
zend_loader.disable_licensing=0
zend_loader.obfuscation_level_support=3
zend_loader.license_path=
```

ok！

安装Zend Guard
cd /usr/local/src
mkdir /usr/local/zend #建立Zend安装目录
tar xvfz ZendGuardLoader-php-5.3-linux-glibc23-i386.tar.gz #解压安装文件
cp ZendGuardLoader-php-5.3-linux-glibc23-i386/php-5.3.x/ZendGuardLoader.so /usr/local/zend/ #拷贝文件到安装目录
vi /usr/local/php5/etc/php.ini #编辑文件
在最后位置添加以下内容
[Zend Guard]
zend_extension=/usr/local/zend/ZendGuardLoader.so
zend_loader.enable=1
zend_loader.disable_licensing=0
zend_loader.obfuscation_level_support=3
zend_loader.license_path=
ok！
Vimium has been updated to 1.41.x


