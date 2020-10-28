---
title: 关于java Calendar Date Math 类的使用
date: 2019-12-29 19:00:00
categories:
 - java
 - java 基础
tags:
 - java
 - java Calendar
 - java Math
 - java Date
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---



## java Calendar、Date、Math 的使用

> 记录 Calendar 的使用方法

### Calendar

> 这是时间类

在早期的jdk版本中，Date类附有两大功能:

1. 允许用年、月、日、时、分、秒来解释日期
2. 允许对表示日期的字符串进行格式化和句法分析

在JDK1.1中提供了类Calendar来完成第一种功能，类DateFormat来完成第二项功能。DateFormat是java.text包中的一个

类。与Date类中有所不同的是，DateFormat类可以接受用各种语言和不同习惯表示的日期字符串。

但是Calendar 类是一个抽象类，他完成Date类与普通日期表示法之间的转换，而我们更多地是使用Calendar类的子类

GregorianCalendar类。它实现了世界上普遍使用的公历系统。当然我们也可以继承Calendar类，然后自己定义实现日历

方法。

先来看一看GregorianCalendar类的构造函数：

| 构造方法 | 说明 |
| ------------ | ------------ |
| GregorianCalendar() | 创建的对象中的相关值被设置成指定时区，缺省地点的当前时间，即程序运行所处的时区、地点的当前时间 |
| GregorianCalendar(TimeZone zone) | 创建的对象中的相关值被设置成指定时区zone，缺省地点的当前时间 |
| GregorianCalendar(Locale aLocale) | 创建的对象中的相关值被设置成缺省时区，指定地点aLocale的当前时间 |
| GregorianCalendar(TimeZone zone,Locale aLocale) | year - 创建的对象中的相关值被设置成指定时区，指定地点的当前时间 | 


TimeZone是java.util包中的一个类,其中封装了有关时区的信息。每一个时区对应一组ID。类TimeZone提供了一些方法完成时区与对应ID两者之间的转换。

例如：

```
// 太平洋时区的ID 为 PST
TimeZone tz0 = TimeZone.getTimeZone("PST");
// getDefault() 可以获取主机所在时区的对象
TimeZone tz1 = TimeZone.getDefault();
```

Locale只是一种机制，他用来标识一个特定的地理、政治或文化区域取一个Locale对象的构造方法:

```
// 调用Locale类的构造方法
Locale 10 = new Locale(String language)
Locale 11 = new Locale(String language,String country)
Locale 12 = new Locale(String language,String country,String variant)
// 调用Locale类中定义的常量
Locale 11 - Locale.CHINA
```


示例代码：

```
import java.text.DateFormat;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;

public class CalendarDemo {
    public static void main(String[] args) {
        System.out.println("完整显示日期时间：");
        // 字符串转换日期格式
        DateFormat fdate = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String str = fdate.format(new Date());
        System.out.println(str);

        // 创建 Calendar 对象
        Calendar calendar = Calendar.getInstance();
        // 初始化 Calendar 对象，但并不必要，除非需要重置时间
        calendar.setTime(new Date());

        // 显示年份
        System.out.println("年： " + calendar.get(Calendar.YEAR));

        // 显示月份 (从0开始, 实际显示要加一)
        System.out.println("月： " + calendar.get(Calendar.MONTH));


        // 当前分钟数
        System.out.println("分钟： " + calendar.get(Calendar.MINUTE));

        // 今年的第 N 天
        System.out.println("今年的第 " + calendar.get(Calendar.DAY_OF_YEAR) + "天");

        // 本月第 N 天
        System.out.println("本月的第 " + calendar.get(Calendar.DAY_OF_MONTH) + "天");

        // 3小时以后
        calendar.add(Calendar.HOUR_OF_DAY, 3);
        System.out.println("三小时以后的时间： " + calendar.getTime());
        // 格式化显示
        str = (new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SS")).format(calendar.getTime());
        System.out.println(str);

        // 重置 Calendar 显示当前时间
        calendar.setTime(new Date());
        str = (new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SS")).format(calendar.getTime());
        System.out.println(str);

        // 创建一个 Calendar 用于比较时间
        Calendar calendarNew = Calendar.getInstance();

        // 设定为 5 小时以前，后者大，显示 -1
        calendarNew.add(Calendar.HOUR, -5);
        System.out.println("时间比较：" + calendarNew.compareTo(calendar));

        // 设定7小时以后，前者大，显示 1
        calendarNew.add(Calendar.HOUR, +7);
        System.out.println("时间比较：" + calendarNew.compareTo(calendar));

        // 退回 2 小时，时间相同，显示0
        calendarNew.add(Calendar.HOUR, -2);
        System.out.println("时间比较：" + calendarNew.compareTo(calendar));

        // calendarNew创建时间点
        System.out.println((new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SS")).format(calendarNew.getTime

()));
        // calendar创建时间点
        System.out.println((new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SS")).format(calendar.getTime()));
        System.out.println("时间比较：" + calendarNew.compareTo(calendar));
    }
}
```

输出结果  

```
完整显示日期时间：
2019-12-12 06:17:57
年： 2019
月： 11
分钟： 17
今年的第 346天
本月的第 12天
三小时以后的时间： Thu Dec 12 09:17:57 UTC 2019
2019-12-12 09:17:57:108
2019-12-12 06:17:57:122
时间比较：-1
时间比较：1
时间比较：1
2019-12-12 06:17:57:123
2019-12-12 06:17:57:122
时间比较：1
```

大家运行上面的代码后，看见控制台上的输出结果会不会有所疑问呢？

其实 month 的含义与 Date 类相同，0 代表 1 月，11 代表 12 月。

有的人可能不明白最后一个的输出为什么有时是 0 ，有时是 1，在这里会涉及到 calendarNew 与 calendar 的创建时

间点， calendarNew 经过增加和减少时间后恢复到原来的时间点，也就是最终比较的是谁先创建好，时间点靠后的大一

些，而 calendarNew 创建的时间点只有可能是大于等于 calendar 的，需要根据实际的创建时间点进行比较。

### Date

> Date 类表示日期和时间，里面封装了操作日期和时间的方法.Date类经常用来获取系统当前时间

Date 中定义的未过时的构造方法:

| 构造方法 | 说明 |
| ------------ | ------------ |
| Date() | 构造一个Date对象并对其进行初始化以反映当前时间 |
| Date(long date) | 构造一个Date对象，并根据相对于 GMT 1970年1月1日 00:00:00的毫秒数以对其进行初始化 |


示例代码：

```
import java.text.SimpleDateFormat;
import java.util.Date;

public class DateDemo{
    public static void main(String[] args){
        String strDate, strTime;
        Date objDate = new Date();
        System.out.println("今天的日期是: " + objDate);
        long time = objDate.getTime();
        System.out.println("自1970年1月1日起以毫秒为单位的时间（GMT）" + time);
        strDate = objDate.toString();
        // 提取GMT时间
        strTime = strDate.substring(11, (strDate.length() - 4));
        // 按照小时、分钟和秒提取时间
        strTime = "时间：" + strTime.substring(0,8);
        System.out.println(strTime);
        // 格式化时间
        SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss:SS");
        System.out.println(formatter.format(objDate));
    }
}
```

运行结果:


```
$ javac DateDemo.java
$ java DateDemo     
今天的日期是: Thu Dec 12 06:34:45 UTC 2019
自1970年1月1日起以毫秒为单位的时间（GMT）1576132485389
时间：06:34:45
2019-12-12 06:34:45:389
```

> 注意 Date类的很多方法自 JDK 1.1 开始就已经过时了。


### Math

> Math 类在java.lang 包中，包含用于执行基本数学运算的方法,如初等指数、对数、平方根和三角函数

常见方法:

| 方法 | 返回值 | 功能描述 |  
| ------------ | ------------ | ------------ |
| sin(double numvalue) | double | 计算角numvalue的正弦值 |  
| cos(double numvalue) | double | 计算角 numvalue 的余弦值 |  
| acos(double numvalue) | double | 计算 numvalue 的反余弦 |  
| asin(double numvalue) | double | 计算 numvalue 的反正弦 |  
| atan(double numvalue) | double | 计算 numvalue 的反正切 |  
| pow(double a, double b) | double | 计算 a 的 b 次方 |  
| sqrt(double numvalue) | double | 计算给定值的正平方根 |  
| abs(int numvalue) | int | 计算 int 类型值 numvalue 的绝对值，也接收 long、float 和 double 类型的参数 |  
| ceil(double numvalue) | double | 返回大于等于 numvalue 的最小整数值 |  
| floor(double numvalue) | double | 返回小于等于 numvalue 的最大整数值 |  
| max(int a, int b) | int | 返回 int 型 a 和 b 中的较大值，也接收 long、float 和 double 类型的参数 |  
| min(int a, int b) | int | 返回 a 和 b 中的较小值，也可接受 long、float 和 double 类型的参数 |  
| rint(double numvalue) | double | 返回最接近 numvalue 的整数值 |  
| round(T arg) | arg 为 double 时返回long,为float 时返回int | 返回最接近arg的整数值 |  
| random() | double | 返回带正号的 double 值，该值大于等于 0.0 且小于 1.0 |  

> 上面都是一些常用的方法，如果同学们还会用到极坐标、对数等，就去查一查手册吧。


示例代码:

```
public class MathDemo {
    public static void main(String[] args) {
        System.out.println(Math.abs(-12.7));
        System.out.println(Math.ceil(12.7));
        System.out.println(Math.rint(12.4));
        System.out.println(Math.random());
        System.out.println("sin30 = " + Math.sin(Math.PI / 6));
        // 计算30°的正弦值，参数是用弧度表示的角，即π的六分之一
        System.out.println("cos30 = " + Math.cos(Math.PI / 6));
        // 计算30°的余弦值，这些计算三角函数的方法，其参数和返回值的类型都为double
        System.out.println("tan30 = " + Math.tan(Math.PI / 6));
        // 计算30°的正切值
    }
}
```

输出结果:

```
$ javac MathDemo.java
$ java MathDemo
12.7
13.0
12.0
0.4548881782067914
sin30 = 0.49999999999999994
cos30 = 0.8660254037844387
tan30 = 0.5773502691896257
```
