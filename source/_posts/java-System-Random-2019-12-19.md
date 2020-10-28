---
title: 关于 Java System、Random 的使用
date: 2019-12-19 16:00:00
categories:
 - java
 - java 基础
tags:
 - java
 - java System
 - java Random
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---

## java 关于 System、Random  的使用

> 记录java的System、Random  类的使用

### System

System 类提供一下的功能:

 * 标准输入，标准输出和错误输出流
 * 访问外部定义的属性和环境变量
 * 加载文件和库的方法
 * 以及用于快速复制数组的实用方法。

System 不可以被实例化，只能使用其静态方法

```
//从指定的源数组中复制一个数组，从源数组指定的位置开始，到目标数组指定的位置
public static void arraycopy(Object src,int srcPos, Object dest,int desPos,int length) 
//返回以毫秒为单位的当前时间(从1970年到现在的毫秒数)
public static long currentTimeMillis()  
//终止当前正在运行的Java虚拟机，status为 0时退出
public static void exit(int status)  
//  运行垃圾收集器
public static void gc() 
// 取得当前系统的全部属性
public static Properties getProperties()
//获取指定键的系统属性
public static String  getProperty(String key) 
```


示例代码：

```
import java.util.Arrays;

public class SystemDemo {
    public static void main(String[] args) {
        int[] a = {7, 8, 9, 10, 11};
        int[] b = {1, 2, 3, 4, 5, 6};
        //从数组a的第二个元素开始，复制到b数组的第三个位置 复制的元素长度为3
        System.arraycopy(a, 1, b, 2, 3);
        //输出结果
        System.out.println(Arrays.toString(b));
        System.out.println("当前时间：" + System.currentTimeMillis());
        System.out.println("java版本信息：" + System.getProperty("java.version"));
        //运行垃圾收集器
        System.gc();
        //退出
        System.exit(0);
    }
}
```

输出结果

```
[1, 2, 8, 9, 10, 6]
当前时间：1576139050573
java版本信息：1.8.0_131
```


### Random 

> Random 类用于生产伪随机数流，在java.util包下。

示例代码:

```
import java.util.Random;

public class RandomDemo {
    public static void main(String[] args) {
        Random random = new Random();
        //随机生成一个整数 int范围
        System.out.println(random.nextInt());
        //生成 [0,n] 范围的整数  设n=100
        System.out.println(random.nextInt(100 + 1));
        //生成 [0,n) 范围的整数  设n=100
        System.out.println(random.nextInt(100));
        //生成 [m,n] 范围的整数  设n=100 m=40
        System.out.println((random.nextInt(100 - 40 + 1) + 40));
        //随机生成一个整数 long范围
        System.out.print(random.nextLong());
        //生成[0,1.0)范围的float型小数
        System.out.println(random.nextFloat());
        //生成[0,1.0)范围的double型小数
        System.out.println(random.nextDouble());
    }
}
```

运行结果：


```
$ javac RandomDemo.java
$ java RandomDemo
272128541
67
93
66
-23177167376469717070.93104035
0.20044632645967309
```

> ps 多关注 (new Random).nextInt() 这个生产随机数的 规则 [m,n] 例如 m=x n>m :x可以是任何数

