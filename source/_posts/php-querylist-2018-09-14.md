---
title: 基于 PHP 的数据爬取（QueryList）
date: 2018-09-14 22:06:08
categories: 
 - PHP
 - QueryList
tags:
 - php
 - querylist
 - 爬虫
 - 数据抓取
cover: https://s2.ax1x.com/2019/10/19/Kn0gZ4.png
---
## 基于PHP的数据爬取 ##

[官方网站站点][1]

> 简单、 灵活、强大的PHP采集工具，让采集更简单一点。

简介：
  QueryList使用jQuery选择器来做采集，让你告别复杂的正则表达式；QueryList具有jQuery一样的DOM操作能力、Http网络操作能力、乱码解决能力、内容过滤能力以及可扩展能力；可以轻松实现诸如：模拟登陆、伪造浏览器、HTTP代理等意复杂的网络请求；拥有丰富的插件，支持多线程采集以及使用PhantomJS采集JavaScript动态渲染的页面。

安装

通过Composer安装:

    composer require jaeger/querylist

使用教程：

直接上代码：

      <?php
    include './vendor/autoload.php';
    // 使用composer安装后引入目录
    use QL\QueryList;
    // 使用插件
    
    $html = file_get_contents('https://www.biqudu.com/14_14778/');
    // 手动获取页面
    $data = QueryList::html($html);
    // 得到页面内容
    $data = QueryList::setHtml('https://www.biqudu.com/14_14778/');
    // 等同于上面的html()
    $data->rules([
    	// 采集所有a标签的href属性
        'link' => ['a','href'],
        // 采集所有a标签的文本内容
        'text' => ['a','text']
    	]);
    // 此处$data = 上面已经获取到网页内容之后的对象
    // 设置采集规则 替代了传统正则
    $data->query();
    // 此处$data = 上面已经获取到网页内容之后的对象 
    // query 执行操作
    $data->getData();
    // 此处$data = 上面已经获取到网页内容之后的对象
    // 得到数据结果
    $data->all();
    // 此处$data = 上面已经获取到网页内容之后的对象
    // 将数据转换成二维数组
    print_r($data->all());
    // 打印结果


<!--more-->

上面的基本使用方法就是这样了 这样我们已经可以抓取到一定的数据了

如果你对爬取数据感兴趣 欢迎前往官网查看文档 [超级传送门（点我）][2]


  [1]: https://www.querylist.cc/
  [2]: https://doc.querylist.cc/