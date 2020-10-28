---
title: Lambad Streams （流）
date: 2020-01-01 12:00:00
categories:
 - java
 - java 函数式编程
tags:
 - java
 - java Streams
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---


## Streams （流）

Stream 是Java 8中的一个新的抽象层。通过使用Stream，你能以类似于SQL语句的声明式方式处理数据。  

例如一个典型的SQL语句能够自动地返回某些信息，而不用在开发者这一端做任何的计算工作。同样，通过使用Java的集合框架，开发者能够利用循环做重复的检查。另外一个关注点是效率，就像多核处理器能够提升效率一样，开发者也可以通过并行化编程来改进工作流程，但是这样很容易出错。  

因此，Stream的引入是为了解决上述痛点。开发者可以通行声明式数据处理，以及简单地利用多核处理体系而不用写特定的代码。  

说了这么久，Stream究竟是什么呢？Stream代表了来自某个源的对象的序列，这些序列支持聚集操作。下面是Stream的一些特性：  

* 元素序列：Stream以序列的形式提供了特定类型的元素的集合。根据需求，它可以获得和计算元素，但不会储存任何元素。
* 源：Stream可以将集合、数组和I/O资源作为输入源。
* 聚集操作：Stream支持诸如filter、map、limit、reduce等的聚集操作。
* 流水技术：许多Stream操作返回了流本身，故它们的返回值可以以流水的行式存在。这些操作称之为中间操作，并且它们的功能就是负责输入、处理和向目标输出。collect()方法是一个终结操作，通常存在于流水线操作的末端，来标记流的结束。
* 自动迭代：Stream的操作可以基于已提供的源元素进行内部的迭代，而集合则需要显式的迭代。

> 扩展阅读：[java 8 中的 Streams Api 详解](http://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/) - IBM  

### 产生流

集合的接口有两个方法来产生流  

* ***stream()*** :该方法返回一个将集合视为源的连续流
* ***parallelStream()*** :该方法返回一个将集合视为源的并行流。  

#### 相关的方法介绍

* ***forEach*** :该方法用于对Steam中的每个元素进行迭代操作。下面的代码段演示了如何使用forEach方法输出10个输出数。  

```
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

* ***map*** :该方法用于将每个元素映射到对应的结果上。下面代码段演示了怎样用map方法输出唯一的某个数的平方。  

```
List<Integer> numbers = Arrays.asList(2,3,3,2,5,2,7);
// get list of unique squares
List<Integer> squaresList = numbers.stream().map( i -> i*i ).distinct().collect(Collectiors.toList());
```

* ***filter*** :该方法用于过滤满足条件的元素。下面的代码段演示了怎样输出使用了过滤方法的空字符串数量。  

```
List<String>strings = Arrays.asList("efg","","abc","bc","ghij","lmn");
// get count of empty string
long count = strings.stream().filter(string -> string.isEmpty()).count();
```

* ***limit*** :该方法用于减少Stream的大小。下面的代码段演示了怎样有限制地输出10个随机数。  
```
Random random = new Random();
random.ints().limit(10).forEach(System.out::println);
```

* ***sorted*** :该方法用于对Stream排序。下面的代码段演示了怎样以有序的形式输出10个随机数  
```
Random random = new Random();
random.ints().limit(10).sorted().forEach(System.out::println);
```

#### 并行处理

ParallelStream 是 Stream 用于并行处理的一种替代方案。下面的代码段演示了如何使用它来输出字符串的数量  

```
List<String> strings = Arrays.asList("efg","","abc","bc","ghij","lmn");
long count = strings.parallelStream().filter(String::isEmpty).count();
```

当然，在连续的Stream与并行的Stream之间切换是很容易的。  

#### Collector

Collector 用于合并Stream的元素处理结果。它可以用于返回一个字符串列表  

示例代码：  
```
List<String> strings = Arrays.asList("efg","","abc","bc","ghij","lmn");
List<String> filtered = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.toList());
System.out.println("Filtered List:" + filtered);
String mergedString = strings.stream().filter(string -> !string.isEmpty()).collect(Collectors.joining(", "));
System.out.println("Merged String: " + mergedString);
```

#### 统计工具

Java 8引入了用于统计的Collector来计算Stream处理完成后的所有统计数据。  

下面的代码段演示了如何使用它  

```
List<Integer> numbers = Arrays.asList(2,3,3,2,5,2,7);

IntSummaryStatistics stats = numbers.stream().mapToInt((x) -> x).summaryStatistics();

System.out.println("Highest number in List : " + stats.getMax());
System.out.println("Lowest number in List : " + stats.getMin());
System.out.println("Sum of all numbers : " + stats.getSum());
System.out.println("Average of all numbers : " + stats.getAverage());
```

### 一个Stream的例子

下面我们通过一个例子来演示技巧  

新建一个类NewFeaturesTester.java，以下是NewFeaturesTester.java 类中应当输入的代码:  

```
import java.util.ArrayList;
import java.util.Arrays;
import java.util.IntSummaryStatistics;
import java.util.List;
import java.util.Random;
import java.util.stream.Collectors;
import java.util.Map;

public class NewFeaturesTester {
   public static void main(String args[]){
      System.out.println("Using Java 7: ");

      // 统计空字符串的数量
      List<String> strings = Arrays.asList("efg", "", "abc", "bc", "ghij","", "lmn");
      System.out.println("List: " +strings);
      long count = getCountEmptyStringUsingJava7(strings);

      System.out.println("Empty Strings: " + count);
      count = getCountLength3UsingJava7(strings);

      System.out.println("Strings of length 3: " + count);

      // 消除空字符串
      List<String> filtered = deleteEmptyStringsUsingJava7(strings);
      System.out.println("Filtered List: " + filtered);

      // 消除空字符串，同时使用逗号来连接
      String mergedString = getMergedStringUsingJava7(strings,", ");
      System.out.println("Merged String: " + mergedString);
      List<Integer> numbers = Arrays.asList(2, 3, 3, 2, 5, 2, 7);

      // 获得不同数字的平方的列表
      List<Integer> squaresList = getSquares(numbers);
      System.out.println("Squares List: " + squaresList);
      List<Integer> integers = Arrays.asList(1,2,13,4,15,6,17,8,19);

      System.out.println("List: " +integers);
      System.out.println("Highest number in List : " + getMax(integers));
      System.out.println("Lowest number in List : " + getMin(integers));
      System.out.println("Sum of all numbers : " + getSum(integers));
      System.out.println("Average of all numbers : " + getAverage(integers));


      // 输出10个随机数
      System.out.println("Random Numbers: ");
      Random random = new Random();

      for(int i=0; i < 10; i++){
         System.out.println(random.nextInt());
      }



      // 使用Java 8的新特性

      System.out.println("Using Java 8: ");
      System.out.println("List: " +strings);

      count = strings.stream().filter(string->string.isEmpty()).count();
      System.out.println("Empty Strings: " + count);

      count = strings.stream().filter(string -> string.length() == 3).count();
      System.out.println("Strings of length 3: " + count);

      filtered = strings.stream().filter(string ->!string.isEmpty()).collect(Collectors.toList());
      System.out.println("Filtered List: " + filtered);

      mergedString = strings.stream().filter(string ->!string.isEmpty()).collect(Collectors.joining(", "));
      System.out.println("Merged String: " + mergedString);

      squaresList = numbers.stream().map( i ->i*i).distinct().collect(Collectors.toList());
      System.out.println("Squares List: " + squaresList);
      System.out.println("List: " +integers);

      IntSummaryStatistics stats = integers.stream().mapToInt((x) ->x).summaryStatistics();

      // 输出结果
      System.out.println("Highest number in List : " + stats.getMax());
      System.out.println("Lowest number in List : " + stats.getMin());
      System.out.println("Sum of all numbers : " + stats.getSum());
      System.out.println("Average of all numbers : " + stats.getAverage());
      System.out.println("Random Numbers: ");

      random.ints().limit(10).sorted().forEach(System.out::println);

      // 并行处理
      count = strings.parallelStream().filter(string -> string.isEmpty()).count();
      System.out.println("Empty Strings: " + count);
   }

   // 使用Java 7版本就提供的API来计算空串数量    
   private static int getCountEmptyStringUsingJava7(List<String> strings){
      int count = 0;

      for(String string: strings){

         if(string.isEmpty()){
            count++;
         }
      }
      return count;
   }

   // 使用Java 7版本就提供的API来计算长度为3字符的字符串数量
   private static int getCountLength3UsingJava7(List<String> strings){
      int count = 0;

      for(String string: strings){

         if(string.length() == 3){
            count++;
         }
      }
      return count;
   }

   // 使用Java 7版本就提供的API来删除空串
   private static List<String> deleteEmptyStringsUsingJava7(List<String> strings){
      List<String> filteredList = new ArrayList<String>();

      for(String string: strings){

         if(!string.isEmpty()){
             filteredList.add(string);
         }
      }
      return filteredList;
   }

   // 使用Java 7版本就提供的API来获取合并后的字符串
   private static String getMergedStringUsingJava7(List<String> strings, String separator){
      StringBuilder stringBuilder = new StringBuilder();

      for(String string: strings){

         if(!string.isEmpty()){
            stringBuilder.append(string);
            stringBuilder.append(separator);
         }
      }
      String mergedString = stringBuilder.toString();
      return mergedString.substring(0, mergedString.length()-2);
   }


   // 自定义的用于计算数字的平方的方法
   private static List<Integer> getSquares(List<Integer> numbers){
      List<Integer> squaresList = new ArrayList<Integer>();

      for(Integer number: numbers){
         Integer square = new Integer(number.intValue() * number.intValue());

         if(!squaresList.contains(square)){
            squaresList.add(square);
         }
      }
      return squaresList;
   }

   // 自定义的用于获得List中最大值的方法
   private static int getMax(List<Integer> numbers){
      int max = numbers.get(0);

      for(int i=1;i < numbers.size();i++){

         Integer number = numbers.get(i);

         if(number.intValue() > max){
            max = number.intValue();
         }
      }
      return max;
   }

   // 自定义的用于获得List中最小值的方法
   private static int getMin(List<Integer> numbers){
      int min = numbers.get(0);

      for(int i=1;i < numbers.size();i++){
         Integer number = numbers.get(i);

         if(number.intValue() < min){
            min = number.intValue();
         }
      }
      return min;
   }

   // 自定义的用于获得List中各个数字的和的方法
   private static int getSum(List<Integer> numbers){
      int sum = (int)(numbers.get(0));

      for(int i=1;i < numbers.size();i++){
         sum += (int)numbers.get(i);
      }
      return sum;
   }

   // 自定义的用于获得List中各个数字的平均值的方法
   private static int getAverage(List<Integer> numbers){
      return getSum(numbers) / numbers.size();
   }
}
```
