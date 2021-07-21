---
title: 巧用CAS解决数据一致性问题
date: 2019-01-08
categories: 
 - 服务架构
tags: 
 - CAS
 - MYSQL
 - 服务架构
 - 架构师
 - CAS 算法
 - MYSQL 锁
copyright: false
cover: https://s2.ax1x.com/2019/10/19/KnBlTJ.png
---
## 巧用CAS解决数据一致性问题

58沈剑 架构师之路 _2015-11-05_

**缘起**：在高并发的分布式环境下，对于数据的查询与修改容易引发一致性问题，本文将分享一种非常简单但有效的优化方法。

## 一、业务场景 

业务场景为，购买商品的过程要对余额进行查询与修改，大致的业务流程如下：

（1）从数据库查询用户现有余额 `SELECT money FROM t_yue WHERE uid=$uid`，不妨设查询出来的`$old_money=100元`

![](http://mmbiz.qpic.cn/mmbiz/YrezxckhYOwqEGDX16FzribBMJ3okQE4I1sPicaUAxOQdJlauicU6gv8QTnjGws7T5Vy6F34vYicm1t04RJMIibhYXw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

（2）业务层实施业务逻辑，比如购买一个80元的商品，并且打九折

    if($old\_money> 80\*0.9) $new\_money=$old_money-80\*0.9=28

![](http://mmbiz.qpic.cn/mmbiz/YrezxckhYOwqEGDX16FzribBMJ3okQE4IyibMRTgtommicU61DeN1mia9BSguDFdo8gqoKwI4JbqBEiarwvibbv8NZCQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

（3）将数据库中的余额进行修改 `UPDAtE t\_yue SET money=$new\_money WHERE uid=$uid`

![](http://mmbiz.qpic.cn/mmbiz/YrezxckhYOwqEGDX16FzribBMJ3okQE4IHJ5mYyJyHSibGSKrt045nETHicY3ibkK2mcmVM96Gp5DksK3CmWQFRaxw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

在并发量低的情况下，这个流程没有任何问题，原有金额100元，购买了80元的九折商品（72元），剩余28元。

## 二、潜在的问题 

在分布式环境中，如果并发量很大，**这种“查询****+****修改”的业务很容易出现数据不一致**。极限情况下，可能出现这样的异常流程：

（1）业务1和业务2同时查询余额，是100元

![](http://mmbiz.qpic.cn/mmbiz/YrezxckhYOwqEGDX16FzribBMJ3okQE4IymrTyYQ14y38RQUysvfIGzN0xdeALkZTrcSib1HfTV74ZnoAdOUl6sw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

（2）业务1和业务2进行逻辑计算，算出各自业务的余额，假设业务1算出的余额是28元，业务2算出的余额是38元

![](http://mmbiz.qpic.cn/mmbiz/YrezxckhYOwqEGDX16FzribBMJ3okQE4I5B5FxMuV8ibD0NMVFynD8hLfuJKZF5FNhBIP7uMKibia7T9da6u5js0lg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

（3）业务1对数据库中的余额先进行修改，设置成28元。

业务2对数据库中的余额后进行修改，设置成38元。

![](http://mmbiz.qpic.cn/mmbiz/YrezxckhYOwqEGDX16FzribBMJ3okQE4IQY7wn9PTNr4ictHrFGndTzQgx1th35gNWRUoEpvtCR3b9gSlv3HIRyg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)  

此时异常出现了，原有金额100元，业务1扣除了72元，业务2扣除了62元，最后剩余38元。

## 三、问题原因 

> 高并发环境下，对同一个数据的**并发读**（两边都读出余额是100）与**并发写**（一个写回28，一个写回38）导致的数据一致性问题。

## 四、原因分析 

业务1的写回：原有金额100，这是一个初始状态，写回金额28，理论上只有在原有金额为100的时候才允许写回成功，这一步没问题。

业务2的写回：的原有金额100，这是一个初始状态，写回金额38，理论上只有在原有金额为100的时候才允许写回成功，可实际上，这个时候数据库中的金额已经变为28了，这一步的写操作不应该成功。

## 五、简易解决方案 

在set写回的时候，加上初始状态的条件compare，只有初始状态不变时，才允许set写回成功，这正是大家常说的“Compare And Set”（CAS），是一种常见的降低读写锁冲突，保证数据一致性的方法。

## 六、业务的升级 

业务线使用CAS解决高并发时数据一致性问题，只需要在进行set操作时，compare一下初始值，如果初始值变换，不允许set成功。

对于上文中的业务场景，只需要将“`UPDAtEt\_yue SET money=$new\_money WHERE uid=$uid`”升级为

    “UPDAtE t\_yue SETmoney=$new\_money WHERE uid=$uid **AND money=$old_money**”即可。

并发操作发生时：

业务1执行 =\> `UPDAtE t_yue **SET money=28** WHERE uid=$uid **AND money=100**`

业务2执行 =\> `UPDAtE t_yue **SET money=38** WHERE uid=$uid **AND money=100**`

~~【这两个操作同时进行时，只能有一个执行成功】~~。

## 七、怎么判断哪个执行成功，哪个执行失败 

set操作，其实无所谓成功或者失败，业务能通过affect rows得知哪个修改没有成功：

执行成功的业务，affect rows为1

执行失败的业务，affect rows为0

## 八、总结 

> 高并发“查询并修改”的场景，可以用CAS（Compare and Set）的方式解决数据一致性问题。对应到业务，即在set的时候，加上初始条件的比对。

