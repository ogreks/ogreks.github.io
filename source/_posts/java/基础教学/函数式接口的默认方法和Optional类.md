---
title: 函数式接口、默认方法和Optional类 
date: 2020-01-01 12:00:00
categories:
 - java
 - java 函数式编程
tags:
 - java
 - java 函数式接口
 - java Optional
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---


## 函数式接口、默认方法和Optional类  


### 函数式接口 

函数式接口通过一个单一的功能来表现。例如，带有***单个compareTo***方法的比较接口，被用于比较的场合。Java 8 开始定义了大量的函数式接口来广泛地用于lambda表达式。


> Java 8 引入的一个核心概念是函数式接口（Functional Interfaces）。通过在接口里面添加一个抽象方法，这些方法可以直接从接口中运行。如果一个接口定义唯一一个抽象方法，那么这个接口就成为函数式接口。同时，引入了一个新的注解：@FunctionalInterface。可以把他它放在一个接口前，表示这个接口是一个函数式接口。这个注解是非必须的，只要接口只包含一个方法的接口，虚拟机会自动判断，不过最好在接口上使用注解 @FunctionalInterface 进行声明。在接口中添加了 @FunctionalInterface 的接口，只允许有一个抽象方法，否则编译器也会报错。引用自[IBM - Java 8 新特性概述](http://www.ibm.com/developerworks/cn/java/j-lo-jdk8newfeature/index.html)

### 相关的接口及描述

下面是部分函数式接口的列表  

| 接口 | 描述 |  
| ---- | ---- |  
| BitConsumer<T,U> | 改接口代表了接收两个输入参数T、U，并且没有返回的操作 |  
| BiFunction<T,U,R> | 该接口代表提供接收两个参数T、U，并且产生一个结果R的方法 |  
| BinaryOperator<T> | 代表了基于两个相同类型的操作数, 产生任然是相同类型结果的操作 |  
| BiPredicate<T,U> | 代表了对两个参数的断言操作（基于Boolean值的方法） |  
| BooleanSupplier | 代表了一个给出Boolean值结果的方法 |  
| Consumer<T> | 代表了接受单一输入参数并且没有返回值的操作 |  
| DoubleBinaryOperator | 代表了基于两个Double类型操作数的操作，并且返回一个Double类型的返回值 |  
| DoubleConsumer | 代表了一个接受单个Double类型的参数并且没有返回的操作 |  
| DoubleFunction<R> | 代表了一个接受Double类型参数并且返回结果的方法 |  
| DoublePredicate | 代表了对一个Double类型的参数的断言操作 |  
| DoubleSupplier | 代表了一个给出Double类型值的方法 |  
| DoubleToIntFunction | 代表了接受单个Double类型参数但返回Int类型结果的方法 |  
| DoubleToLongFunction | 代表了接受单个Double类型参数但返回Long类型结果的方法 |  
| DoubleUnaryOperator | 代表了基于单个Double类型操作数且产生Double类型结果的操作 |  
| Function<T,R> | 代表了接受一个参数并且产生一个结果的方法 |  
| IntBinaryOperator | 代表了对两个Int类型操作数的操作，并且产生一个Int类型的结果 |  
| IntConsumer | 代表了接受单个Int类型参数的操作，没有返回结果 |  
| IntFunction<R> | 代表了接受Int类型参数并且给出返回值的方法 |  
| IntPredicate | 代表了对单个Int类型参数的断言操作 |  

更多的接口可以参考Java官方API手册：[java.lang.Annotation Type FunctionalInterface](https://docs.oracle.com/javase/8/docs/api/java/lang/FunctionalInterface.html)。在实际使用过程中，加有 ***@FunctionalInterface*** 注解的方法均是此类接口，位于[java.util.Funtion](http://docs.oracle.com/javase/8/docs/api/java/util/function/package-frame.html)包中。  

#### 一个函数式编程的例子

下面我们通过一个例子学习如何使用这些函数式编程的接口  

新建一个类 ***NewFeaturesTester.java***，以下是 ***NewFeaturesTester.java*** 类中应当输入的代码：  

```
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;

public class NewFeaturesTester {
   public static void main(String args[]){
      List<Integer> list = Arrays.asList(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);

      System.out.println("All of the numbers:");

      eval(list, n->true);

      System.out.println("Even numbers:");
      eval(list, n-> n%2 == 0 );

      System.out.println("Numbers that greater than  5:");
      eval(list, n -> n > 5 );
   }

   public static void eval(List<Integer> list, Predicate<Integer> predicate) {
      for(Integer n: list) {

         if(predicate.test(n)) {
            System.out.println(n);
         }
      }
   }
}
```

### 默认方法

Java 8在接口方面引入了一个关于默认方法实现的新概念。它也是作为一种向后兼容能力而出现，旧的接口也能用到Lambda表达式中。例如，***List***或 ***Collection*** 接口是没有forEach方法的声明的。但是，通过这些默认方法能够就能轻易地打破集合框架实现的限制。Java 8引入默认方式使得 ***List*** 和 ***Collection*** 接口能够拥有 ***forEach*** 方法的默认实现。实现了这些接口的类也不必再实现相同的功能了。  

语法如下所示:  

> 本段代码是示例代码,仅供理解使用。

```
public interface boy {
    default void print(){
        System.out.println("I am a boy");
    }
}
```

#### 多个默认值

接口中有了默认方法之后，在同一个类里面实现两个带有相同默认方法的接口就可行了。  

下面的代码演示了如何解决这种含糊不清的情况。  

首先是同一个类里面的两个接口  

> 本段是示例代码，仅供理解

```
public interface younger {
    default void print(){
        System.out.println("I am a younger.");
    }
}

public interface learner {
    default void print(){
        System.out.println("I am a learner.");
    }
}
```

第一个解决办法就是创建一个自有的办法，来重写默认的实现。就像这样：  

> 本段代码是示例代码，仅供理解使用。 

```
public class student implements younger, learner {
   public void print(){
      System.out.println("I am a younger and a learner, so I am  a student.");
   }
}
```

另一个解决办法就是使用超类 ***super*** 来调用特定接口的默认方法。  

> 本段代码是示例代码，仅供理解使用  

```
public class student implements younger, learner {
    public void print(){
        learner.super.print();
    }
}
```

#### 静态默认方法

你也可以为这个接口增加静态的辅助方法(helper), 就像下面这样：

> 本段代码是示例代码，仅供理解使用

```
public interface Younger {
    default void print(){
        System.out.println("I am a younger.");
    }

    static void sayHi(){
        System.out.println("Young is the capital.");
    }
}
```

#### 一个默认方法的例子

下面我们通过一个例子来掌握如何使用默认方法。请看下面的代码

```
public class NewFeaturesTester {
    public static void main(String args[]) {
        Younger younger = new Student();
        younger.print();
    }
}

interface Younger {
    default void print() {
        System.out.println("I am a younger.");
    }

    static void sayHi() {
        System.out.println("Young is the capital.");
    }
}

interface Learner {
    default void print() {
        System.out.println("I am a learner.");
    }
}

class Student implements Younger, Learner {
    public void print() {
        Younger.super.print();
        Learner.super.print();
        Younger.sayHi();
        System.out.println("I am a student!");
    }
}
```

### Optional 类

Optional是一个容器对象，用于容纳非空对象。Optional对象通过缺失值值代表null。这个类有许多实用的方法来促使代码能够处理一些像可用或者不可用的值，而不是检查那些空值（null）。Java 8中引入的这个特性有点像Google Guava里的Optional（Guava 是一个 Google 的基于Java 6的类库集合的扩展项目）。  

在Java官方文档的解释中，它是一个可以为null的容器对象。如果值存在则 isPresent() 方法会返回 true ，调用 get()方法会返回该对象。  

#### 类的声明及方法

下面是java.util.Optional<T>类的声明：  

```
public final class Optional<T>
extends Object
```

这个类继承了java.lang.Object类大多数方法。主要有：  

| 接口 | 描述 |  
| ---- | ---- |  
| static <T> Optional<T> empty() | 该方法返回一个空的Optional实例 |  
| boolean equals(Object obj) | 该方法可以指示某个对象是否与当前Optional对象相等 |  
| Optional<T> filter(Predicate<? super <T> predicate) | 如果一个值存在并且这个值满足某个给定的断言，那么该方法将返回一个描述该值的Optional对象；否则，将返回一个空的Optional对象 |  
| Optional flatMap(Function<? super T,Optional> mapper) | 如果一个值存在，该方法会把一个map方法应用于它，并且返回结果；否则，将返回空的Optional对象 |  
| T get() | 如果一个值存在于当前Optional中，则返回这个值；否则将抛出一个NoSuchElementException异常 |  
| int hashCode() | 返回当前值的hash编码值。若这个值不存在，则返回0 |  
| void ifPresent(Consumer<? super T> consumer) | 如果一个值存在，该方法会通过该值调用指定的consumer。如果不存在，则不调用 |  
| boolean isPresent() | 返回一个值是否存在 |  
| Optional map(Function<? super T,? extends U> mapper | 如果一个值存在，则将某个map方法应用于它。如果这个值是非空的，则返回一个描述结果的Optional对象 | 
| static <T> Optional<T> of(T value) | 返回某个存在的非空值的Optional对象 |  

更多的你可以访问官方API手册查看：[http://docs.oracle.com/javase/8/docs/api/java/util/Optional.html](http://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)  

#### 一个Optional类的例子

```
import java.util.Optional;

public class NewFeaturesTester {
   public static void main(String args[]){

      NewFeaturesTester tester = new NewFeaturesTester();
      Integer value1 = null;
      Integer value2 = new Integer(5);

      // ofNullable 允许传参时给出 null
      Optional<Integer> a = Optional.ofNullable(value1);

      // 如果传递的参数为null，那么 of 将抛出空指针异常（NullPointerException）
      Optional<Integer> b = Optional.of(value2);
      System.out.println(tester.sum(a,b));
   }

   public Integer sum(Optional<Integer> a, Optional<Integer> b){

      // isPresent 用于检查值是否存在

      System.out.println("First parameter is present: " + a.isPresent());
      System.out.println("Second parameter is present: " + b.isPresent());

      // 如果当前返回的是传入的默认值，orElse 将返回它
      Integer value1 = a.orElse(new Integer(0));

      // get 用于获得值，条件是这个值必须存在
      Integer value2 = b.get();
      return value1 + value2;
   }
}
```

#### 扩展阅读

[Java 8 Optional类深度解析](http://www.importnew.com/6675.html)

### 参考链接

* [Java SE 8: Lambda Quick Start](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/Lambda-QuickStart/index.html)
* [The Java™ Tutorials - Default Methods](https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html)


