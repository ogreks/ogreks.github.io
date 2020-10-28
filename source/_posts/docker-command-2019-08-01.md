---
title: docker 命令笔记
date: 2020-06-26 23:45:17
tags:
---

## docker简介(docker 命令笔记)

### 为什么要使用docker
简单粗暴的一句话，docker已经实现了在linux 容器化技术上，达到‘任何时间、任何地点’可以一键部署等完美操作，当然，这只是docker的 一部分骚操作

*  更快速的交付和部署
*  更高效的资源利用
*  更轻松的迁移和扩展
*  更简单的更新和管理

#### docker 核心概念
docker 大部分的操作都围绕这它的三大核心概念：镜像、容器和仓库。因此、这三大概念也尤为重要

#### 安装docker
> [官方教程:https://docs.docker.com/install/linux/docker-ce/centos/](https://docs.docker.com/install/linux/docker-ce/centos/)

### 使用docker镜像
镜像是docker 三大核心概念中最重要的

docker 运行容器前需要本地存在对应的镜像，如果镜像不存在，docker 会尝试先从默认镜像仓库下载（默认使用docker hub 公共注册服务器中的仓库）

如果你的服务器在国外（香港也算 台湾也算，非大陆）推荐docker hub

如果在国内 推荐阿里云的镜像仓库 地址:请自行百度

#### 命令格式

##### docker [image] pull
```
docker [image] pull
```

例如：
```
docker pull ubuntu:18.04
```
对于docker 镜像而言 如果不显式指定tag 则默认会选择latest标签，这回下载仓库中最新版本的镜像 

如果从非官方的仓库进行下载，则需要在仓库名称钱指定完整的仓库地址。例如从网易蜂巢的镜像源来下载ubuntu:18.04:
```
docker pull hub.c.163.com/public/ubuntu:18.04
```
pull 子命令支持的选型主要包括：
* -a , --all-tags=true|false:是否获取仓库中的所有镜像，默认为否；
* --disable-content-trust:取消镜像的内容校验，默认为真。

另外，有时需要使用镜像代理服务来加速 **docker** 镜像获取过程，可以在docker服务启动配置中增加--registry-mirror=proxy_URL来指定镜像代理服务地址（如https://registry.docker-cn.com）

##### docker images | docker image ls

使用docker images 或者 docker image ls 命令可以列出本地主机上已有镜像的基本信息。

##### docker tag [image] [new image]

使用 tag 命令为镜像添加标签
为了方便在后续的工作中使用特定镜像，还可以使用 docker tag 命令来为本地镜像任意的添加新的名称或者标签。

##### docker [image] inspect

使用该命令可以获取镜像的详细信息，包括制作者、适应框架、各层的数字摘要等

##### docker history [image]

使用该命令则可以查看历史镜像

##### docker search [option] keyword

命令选项支持
* -f --filter filter:过滤输出内容
* --format string:格式化输出内容；
* --limit int: 限制输出结果个数,默认25个
* --no-trunc: 不截断输出结果

##### docker rm | rmi | prune

###### 通过标签删除镜像

```
# 删除镜像（注意：镜像）
docker rmi 
docker image rm 
```
命令选项支持
* -f ,-force: 强制删除镜像，即使存在依赖容器
* -no-prune: 不要清理未带标签的父镜像

###### 通过镜像id 删除镜像

当使用docker rmi 命令，并且后面跟上镜像的ID(也可以是能够区分的部分ID串前缀）时，会先尝试删除所有指向该镜像的标签，然后删除该镜像文件本身

###### 清理镜像

当镜像使用超过一段时间之后，系统中可能会遗留一些临时的镜像文件，以及一些没有被使用的镜像，可以通过*docker image prune*来进行清理

命令的选项支持
* -a , -all 删除所有无用镜像，不光是临时镜像
* -filter filter 只清理符合给定过滤器的镜像
* -f , -force 强制删除镜像，而不进行提示确认



##### docker ps -a

该命令可以让你看到本机上面存在的所有容器

