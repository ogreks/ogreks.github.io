---
title: 如何在 Centos7 中安装 gcc
date: 2018-12-27
categories: 
 - centos
 - gcc
tags:
 - gcc
 - centos
 - linux
cover: https://s2.ax1x.com/2019/10/19/KndPeJ.png
---
如何在 Centos7 中安装 gcc
===================


系统环境：**Centos7.4**
今天在安装 **Nodejs8.7** 的时候，报了一个警告：
**WARNING: C++ Compiler too old, need g++ 4.9.4 or clang++ 3.4.2 (CXX=g++)**



然后，查了一下自己系统上安装的版本：4.8.5
好吧，不能用 yum 升级了，那就手动安装了吧



 ## 一、选择需要升级的版本

gcc ftp 下载地址列表
(我选择了5.4.0)
 ## 二、获取安装包并解压

    cd /usr/local/src
    wget https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2
    tar -jxvf gcc-5.4.0.tar.bz2

注：bz2是一种压缩文件格式，若无法解压，安装 bzip2 即可：yum -y install bzip2
 ## 三、进入解压后的gcc文件夹，下载供编译需求的依赖项

    cd gcc-build-5.4.0
    ./contrib/download_prerequisites

 ## 四、建立一个文件夹存放编译文件

    mkdir gcc-build-5.4.0
    cd gcc-build-5.4.0

 ## 五、生成 Makefile 文件

    make

注：这个过程非常耗时，我的1核1G内存大约花了一个来小时



 ## 六、安装

    sudo make install

 ## 七、重启服务器，验证版本

    gcc -v

 


等了那么久，总算是成功了，很激动对不对？
但是！
我执行到上边以后，屁颠屁颠的跑去编译 nodejs 了，耍出了一个错误：

    /usr/local/src/node-v8.7.0/out/Release/mksnapshot: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.21' not found (required by /usr/local/src/node-v8.7.0/out/Release/mksnapshot)

编译失败~ 劳资等了30分钟



好吧，出了问题终究是要解决的
问题原因：升级gcc时，生成的动态库没有替换老版本 gcc 动态库导致的
解决方案：将gcc最新版本的动态库替换系统中老版本的动态库。
(1). 查找编译gcc时生成的最新动态库

    find / -name "libstdc++.so*"



(2) 将找到的动态库`libstdc++.so.6.0.21`复制到`/usr/lib64`

    cp /usr/local/src/gcc-5.4.0/gcc-build-5.4.0/stage1-x86_64-unknown-linux-gnu/libstdc++-v3/src/.libs/libstdc++.so.6.0.21 /usr/lib64

(3). 切换工作目录至/usr/lib64，删除原来的软连接， 将默认库的软连接指向最新动态库。

    cd /usr/lib64
    rm -rf libstdc++.so.6
    ln -s libstdc++.so.6.0.21 libstdc++.so.6

到这里才算是收工了。
