---
title: mysql FIND_IN_SET使用方法
date: 2019-06-17
categories: 
 - MYSQL
tags: 
 - SQL
 - MYSQL
 - SELECT
cover: https://s2.ax1x.com/2019/10/19/Kn0pVJ.jpg
---
# mysql FIND_IN_SET使用方法

#### 使用场景

在最近的一次项目中，遇到一个场景，在一个审批中可能存在两个人同时进行审批，按照原先项目的思路，我决定对该项目的字段进行修改

1. 首先在原有字段上进行修改
> 原有字段存在单个人的id 我们在单个id时对该id进行存储 然后 用户登录时，我们获取用户的id 然后对该id 进行验证
2. 对此进行修改 我直接将原有的bigint类型修改成为varchar字段
> 如 原字段 存储用户1 = id = 1
> 现在字段 存储 用户1 = id = 1  多用户 用户1、用户2 = id = 1,2

#### 问题概述

问题出现在id如果是按照in去查询 那么我们永远只能查询到id在前的无法查询到第一个id 后面的id

百度了一下 在mysql的手册中找到了FIND_IN_SET() 方法

#### 使用方法
###### 语法

```
FIND_IN_SET(str,strlist)
```

###### 定义

1. 假如字符串str在由N子链组成的字符串列表strlist中，则返回值的范围在1到N之间。
2. 一个字符串列表就是一个由一些被‘,’符号分开的自链组成的字符串。
3. 如果第一个参数是一个常数字符串，而第二个是typeSET列，则FIND_IN_SET()函数被优化，使用比特计算。
4. 如果str不在strlist或strlist为空字符串，则返回值为0。
5. 如任意一个参数为NULL，则返回值为NULL。这个函数在第一个参数包含一个逗号(‘,’)时将无法正常运行。

***strlist***：一个由英文逗号“,”链接的字符串，例如："a,b,c,d"，该字符串形式上类似于SET类型的值被逗号给链接起来。

```
示例：SELECT FIND_IN_SET('b','a,b,c,d') // 返回2，即为第二个值
```


#### 关于mysql中的 IN和FIND_IN_SET的查询问题


原来以为mysql可以进行这样的查询
select id, list, name from table where 'daodao' IN (list);

> 注：table含有三个字段id:int,  list:varchar(255),  name:varchar(255)

实际上这样是不行的，这样只有当'daodao'是list中的第一个元素(我测试的时候貌似是第一个也是不行的，只有当list字段的值等于daodao时才是对的)时，查询才有效，否则都的不到结果，即使'daodao'真的在list中

> 结尾大家可以测试一下 我在网上也找到相同的文章 比较详细的那种[http://blog.sina.com.cn/s/blog_5b5460eb0100e5r9.html](http://blog.sina.com.cn/s/blog_5b5460eb0100e5r9.html)



总结：所以如果list是常量，则可以直接用IN， 否则要用FIND_IN_SET()函数