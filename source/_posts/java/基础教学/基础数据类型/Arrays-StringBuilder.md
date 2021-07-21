---
title: 关于java Arrays、StringBuilder 类的使用
date: 2019-12-11 18:00:13
categories:
 - java
 - java 基础
tags:
 - java
 - java Arrays
 - java StringBuilder
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---

## 关于java Arrays、StringBuilder 类的使用

> 记录 java Arrays、StringBuilder 两个类的使用方法

### Arrays
> Arrays 类包含用于操作数组的各种方法（例如排序和搜索）。还包含一个静态工厂，允许将数组转为list


| 方法 | 描述 |
| :--- | :--- |
| <T>List<T> asList(T...a) | 返回由指定数组构造的list |
| void sort(Object[] a) | 对数组进行排序 |
| void fill(Object[] a, Object val) | 对数组的所有元素都赋上相同的值 |
| boolean equals(Object[] a, Object[] a2) | 检查两个数组是否相等 |
| int binarySearch(Object[] a, Object key) | 对排序后的数组使用二分法查找数据 |

示例代码:  
```
import java.util.Arrays;
import java.util.Random;

public class ArraysDemo {
    public static void main(String[] args) {
        int[] arr = new int[10];
        //将数组元素都设为9
        Arrays.fill(arr, 9);
        System.out.println("fill:" + Arrays.toString(arr));
        Random random = new Random();
        for (int i = 0; i < arr.length; i++) {
            //使用100以内的随机数赋值数组
            arr[i] = random.nextInt(101);
        }
        //重新赋值后的数组
        System.out.println("重新赋值：" + Arrays.toString(arr));
        //将索引为5的元素设为50
        arr[5] = 50;
        //排序
        Arrays.sort(arr);
        //排序后的数组
        System.out.println("sort排序后：" + Arrays.toString(arr));
        //查找50的位置
        int i = Arrays.binarySearch(arr, 50);
        System.out.println("值为50的元素索引："+i);
        //复制一份新数组
        int[] newArr = Arrays.copyOf(arr, arr.length);
        //比较
        System.out.println("equals:"+Arrays.equals(arr, newArr));
    }
}
```
编译结果:  
```
$ javac ArraysDemo.java
$ java ArraysDemo
fill:[9, 9, 9, 9, 9, 9, 9, 9, 9, 9]
重新赋值：[69, 83, 40, 58, 94, 42, 2, 53, 43, 83]
sort排序后：[2, 40, 43, 50, 53, 58, 69, 83, 83, 94]
值为50的元素索引：3
equals:true
```

### StringBuilder

> StringBuilder 类是可变的。它是 String 的对等类，它可以增加和编写字符的可变序列，并且能够将字符插入到字符串中间或附加到字符串末尾（当然是不用创建其他对象的）  

StringBuilder 的构造方法：  

| 构造方法 | 说明 |
| :----- | :----- |
| StringBuider() | 构造一个其中不带字符的StringBuilder, 其容量为16个字符 |
| StringBuilder(CharSequenceseq) | 构造一个StringBuilder, 它包含与指定的CHarSequence相同的字符 |
| StringBuilder(int capacity) | 构造一个具有指定初始容量的StringBuilder |
| StringBuilder(String str) | 并将其内容初始化为指定的字符串内容 |

StringBuilder 类的常用方法:  

| 方法 | 返回值 | 功能描述 |
| :--- | :----  | :------  |
| insert(int offsetm, Object obj) | StringBuilder | 在offsetm的位置插入字符串obj |
| append(Object obj) | StringBuilder | 在字符串末尾追加字符串 obj |
| length() | int | 确定StringBuilder 对象的长度 |
| setCharAt(int index,char ch) | void | 使用ch指定的新值设置index指定的位置上的字符 |
| toString() | String | 转换为字符串形式 |
| reverse() | StringBuilder | 反转字符串 |
| delete(int start, int end) | StringBuilder | 删除调用对象中从start位置开始直到end指定的索引(end-1)位置的字符序列 |
| replace(int start, int end, String str) | StringBuilder | 使用一组字符替换另一组字符。将用替换字符串从start指定的位置开始替换，直到end直到的位置结束 |