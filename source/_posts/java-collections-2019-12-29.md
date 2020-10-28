---
title: 关于java Collections 类的使用
date: 2019-12-29 18:00:00
categories:
 - java
 - java 基础
tags:
 - java
 - java Collections
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---

## Java Collections

> java.util.Collections 是一个工具类,他包含了大量对集合进行操作的静态方法.

| 方法名 | 描述 |
| -----  | ----- |
| void sort(List list) | 按自然升序排序 |
| void sort(List list,Comparator c) | 自定义排序规则排序 |
| void shuffle(List list) | 随机排序,用于打乱顺序 |
| void reverse(List list) | 反转,将列表元素顺序反转 |
| void swap(List list,int i,int j) | 交换处于索引 i 和 j 位置的元素 |
| int binarySearch(List list,Object key) | 二分查找,列表必须有序,返回找到的元素索引位置 |
| int max(Collection coll) | 查找最大值 |
| int min(Collection coll) | 查找最小值 |
| void fill(List list,Object obj) | 使用obj填充list所有元素 |
| boolean replaceAll(List list,Object oldVal,Object newVal) | 使用用newVal替换所有的oldVal |
| <K,V>Map<K,V> synchronizedList(List<T> list) | 将list包装为线程安全的List |
| <T>List<T> synchronizedList(List<T> list) | 将list包装为线程安全的List |

``` 
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class CollectionsDemo {
    public static void main(String[] args) {
//        创建一个空List
        List<Integer> list = new ArrayList<>();
        //赋值
        list.add(3);
        list.add(5);
        list.add(7);
        list.add(9);
        list.add(12);
        System.out.print("初始顺序：");
        list.forEach(v -> System.out.print(v + "\t"));


        //打乱顺序
        Collections.shuffle(list);
        System.out.print("\n打乱顺序：");
        list.forEach(v -> System.out.print(v + "\t"));

        //反转
        Collections.reverse(list);
        System.out.print("\n反转集合：");
        list.forEach(v -> System.out.print(v + "\t"));

        //第一个位和最后一位交换
        Collections.swap(list,0,list.size()-1);
        System.out.print("\n交换第一位和最后一位：");
        list.forEach(v -> System.out.print(v + "\t"));

        //按自然升序排序
        Collections.sort(list);
        System.out.print("\nSort排序后：");
        list.forEach(v -> System.out.print(v + "\t"));

        //二分查找 必须排序后
        System.out.print("\n二分查找数值7的位置："+Collections.binarySearch(list, 7));

        //返回线程安全的list
        List<Integer> synchronizedList = Collections.synchronizedList(list);
    }
}
```

运算结果:

```
$ javac CollectionsDemo.java
$ java CollectionsDemo
初始顺序：3    5    7    9    12    
打乱顺序：5    7    3    12    9    
反转集合：9    12    3    7    5    
交换第一位和最后一位：5    12    3    7    9    
Sort排序后：3    5    7    9    12    
二分查找数值7的位置：2
```
