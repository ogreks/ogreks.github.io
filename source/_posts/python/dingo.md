---
title: lumen + dingo 搭建api服务器
date: 2019-06-27 20:26:39
categories: 
 - PHP
 - lumen
tags: 
 - laravel
 - dingo
 - lumen
 - api server
cover: https://s2.ax1x.com/2019/10/19/Kn0gZ4.png
---
# lumen + dingo 搭建api服务器

<u>网上现在关于lumen的教程还是蛮少的，不过很少有初级PHP人员直接接触lumen，估计这也是一个问题吧！作为一个菜鸟，因为公司项目重构，所以我走上了lumen的流行</u>


----------


> lumen [lumen git地址](https://github.com/laravel/lumen) 全部关于lumen的介绍以及说明都有


----------


<u>至于 dingo/api 是一个基于larveral和lumen的api工具 可以帮助我们快速构建api服务</u>


----------


> dingo/api [dingo/api git地址](https://github.com/dingo/api) 全部关于dingo组件


----------


## 安装

首先 ，通过使用composer安装laravel安装器：

```
composer global require "laravel/lumen-installer"
```

然后我们通过安装器进行安装

```
lumen new blog
```

其次我们也可以通过composer创建项目

```
composer create-project --prefer-dist laravel/lumen blog
```

### 本地开发环境

如果你本地安装了PHP，并且你想使用PHP内置的服务器来为你提供应用服务，```php -S localhost:8000 -t public```命令。该命令会在  ```http://localhost:8000```上启动开发服务器

> lumen 和 laravel 虽然是同一个框架系列但是不属于同样的结构 lumen更像是一个laravel的儿子，一个专属于api开发的框架 所以在文件结构上的处理和方式也有很多不同


Lumen 框架所有的配置信息都是存在 .env 文件中。一旦 Lumen 成功安装，你同时也要 配置本地环境。

### dingo install

首先去dingo的git上面查看当前版本 我这里安装的是2.2以上的版本

至于dingo的版本安装失败 请参考[https://learnku.com/courses/laravel-package/api-development-kit-dingoapi/](https://learnku.com/courses/laravel-package/api-development-kit-dingoapi/)

安装dingo 有两种方法 这两种方法基本上百度都能找到

一种是在composer.json文件中进行修改 然后composer update：
```
"require": {
    "dingo/api": "^2.2"
    }
```
另一种就是直接使用 composer 直接将包引入
```
composer require dingo/api
```

当我们安装 dingo 之后我们需要对dingo进行注册 我们使用 laravel 需要将配置文件引入进来，当然我们当前使用的是lumen

```
$app->register(Dingo\Api\Provider\LumenServiceProvider::class);
```

#### 关于Facades

API 自带了两个  **Facade** ，你可以自己选择使用

`Dingo\Api\Facade\API`

这个调度器的 **Facade** ，并提供了一些好用的辅助方法

`Dingo\Api\Facade\Route`

你可以使用这个 **Facade** 来获取API的当前路由、请求、检查当前的路由名称等

#### Dingo 配置


----------


大部分的配置信息都是预先配置好了的，为的是让你能快速上手你的 API 项目。你可以通过 `·env` 文件来自定义大部分配置。但是，有一些配置微调需要你发布配置文件（Laravel）或者在 `bootstrap/app.php` 文件中配置 （Lumen）。你也可以使用 `AppServiceProvider` 中的 `boot` 方法来做设置。


##### Standards Tree 标准树


----------


这有三个不同的树`x`,`prs`,和,`vnd`.你使用的标准树需要取决于你开发的项目

关于版本描述，请自行参考官网配置

##### 配置.env


----------


```
API_STANDARDS_TREE=vnd
```
**子类型通常是应用程序或项目的短名称，都是小写的。**
```
API_SUBTYPE=myapp
```
**前缀和子域**
```
API_PREFIX=api
API_DOMAIN=api.myapp.com
```
> 当然域名和前缀只能用一个

###### 版本号


----------


```
API_VERSION=v1
```

###### 最后配置格式
```
# dingo 
API_STANDARDS_TREE=prs 
API_SUBTYPE=package 
API_PREFIX=api 
API_VERSION=v1 
API_DEBUG=true
```

这几个配置非常的重要， API 版本的切换会利用到这些配置，不配置是会报错的。

接下来就可以进行接口测试了。具体测试 基本上官网文档都讲解的非常明白。具体请自行参考官网配置