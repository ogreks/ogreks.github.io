---
title: mysql 关联查询 查询字段存在null赋值0
date: 2019-06-14
categories: 
 - MYSQL
tags: 
 - SQL
 - MYSQL
 - SELECT
cover: https://s2.ax1x.com/2019/10/19/Kn0pVJ.jpg
---
# mysql 5.6 以上

#### 问题描述
> 在读取数据库中 我们会发现 有的字段我们虽然默认为0 但是在关联查询时 主表查询从表如果查询不到数据 那么数据就会为空 而在前端展示不好看 所以我们能不能直接赋值

#### 环境
~ mysql 版本 5.6以上
#### 查询条件
``` 
select 字段1,字段2,ifNull(字段3,0) as 字段3(别名) from 表名
```

#### 环境
~ sqlserver
#### 查询条件
```
select 字段1,字段2,isNull(字段3,0) as 字段3(别名) from 表名
```

#### 环境
~ oracle
#### 查询条件
```
select 字段1,字段2,nvl(字段3,0) from 表名
```
