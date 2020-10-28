---
title: 如何在 CentOS7 中安装 Nodejs
date: 2018-12-28
categories: 
 - centos
 - Nodejs
tags:
 - linux
 - nodejs
 - centos
 - 前端
cover: https://s2.ax1x.com/2019/10/19/KndPeJ.png
---
安装版本：10.13.0

 

## 一、安装必要的编译软件包

sudo yum install gcc gcc-c++

## 二、从源码下载Nodejs

    cd /usr/local/src
    wget https://npm.taobao.org/mirrors/node/v10.13.0/node-v10.13.0.tar.gz

 


 

## 三、解压 nodejs 安装包

    tar xvf node-v10.13.0.tar.gz

 

## 四、进入解压的 node 文件夹，开始编译

    cd node-v10.13.0/
    ./configure
    make

> 注：make过程较为耗时，可能需要30分钟以上

 

 如果编译过程中报 C++ Compiler too old, need g++ 4.9.4 or clang++ 3.4.2 (CXX=g++)，那需要先升级一下 gcc，编译正常的话请忽略直接跳至第五步开始安装

检查 gcc 的版本

    gcc -v

如果版本号低于4.9.4，请先升级gcc

 
## 五、安装Nodejs

    sudo make install

 

## 六、到此就已经安装完毕了