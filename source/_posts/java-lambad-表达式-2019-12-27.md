---
title: Lambad 表达式的含义及使用方法、引用
date: 2019-12-27 16:00:00
categories:
 - java
 - java 函数式编程
tags:
 - java
 - java Lambad
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---


## Lambad 表达式的含义及使用方法、引用

### 什么是函数式编程

函数式编程（英语：functional programming）或称函数程序设计，又称泛函编程，是一种编程典范，它将计算机运算视为数学上的函数计算，并且避免使用程序状态以及易变对象。
函数编程语言最重要的基础是λ演算（lambda calculus）。而且λ演算的函数可以接受函数当作输入（引数）和输出（传出值）。

### Lambad 表达式

Lambad 表达式是在Java 8 中引入的。并且成为了Java 8最大的特点。  
它使得功能性编程变得非常便利，极大地简化了开发工作。  

#### 语法

一个Lambad表达式具有下面这样的语法特征。它由三个部分组成；  
第一部分为一个括号内用都好分割的参数列表，参数即函数式接口里面方法的参数；第二部分为一个箭头符号：->; 第三部分为方法体，可以使表达式和代码块。语法如下  

```
parameter -> expression body
```

下面列举了Lambad表达式的几个最重要的特征；  

* 可选的类型声明：你不用去声明参数的类型。编译器可以从参数的值来推断它是什么类型。  
* 可选的参数周围的括号：你可以不用在括号内声明单个参数。但是对于很多参数的情况，括号是必需的  
* 可选的大括号：如果表达式体里面只有一个语句，那么你不必用大括号括起来。  
* 可选的返回关键字：如果表达式体只有单个表达式用于值的返回，那么编译器会自动完成这一步。若要指示表达式来返回某个值，则需要使用大括号。  

语言的设计者们思考了很多如何让现有的功能和lambda表达式友好兼容。于是就有了函数接口这个概念。函数接口是一种只有一个方法的接口，函数接口可以隐式地转换成 Lambda 表达式。  

> 函数式接口的重要属性是：我们能够使用 Lambda 实例化它们，Lambda 表达式让你能够将函数作为方法参数，或者将代码作为数据对待。Lambda 表达式的引入给开发者带来了不少优点：在 Java 8 之前，匿名内部类，监听器和事件处理器的使用都显得很冗长，代码可读性很差，Lambda 表达式的应用则使代码变得更加紧凑，可读性增强；Lambda 表达式使并行操作大集合变得很方便，可以充分发挥多核 CPU 的优势，更易于为多核处理器编写代码。引用自[IBM - Java 8 新特性概述。](http://www.ibm.com/developerworks/cn/java/j-lo-jdk8newfeature/index.html)  

#### 一个Lambad 表达式的例子

下面尝试写一些代码来理解Lambda表达式。新建一个类NewFeaturesTester.java，请在NewFeaturesTester.java中输入下面这些代码，对于它们的解释在注释中给出。  

```
public class NewFeaturesTester {
    public static void main(String args[]){
        NewFeaturesTester tester = new NewFeaturesTester();

          // 带有类型声明的表达式
          MathOperation addition = (int a, int b) -> a + b;

          // 没有类型声明的表达式
          MathOperation subtraction = (a, b) -> a - b;

          // 带有大括号、带有返回语句的表达式
          MathOperation multiplication = (int a, int b) -> { return a * b; };

          // 没有大括号和return语句的表达式
          MathOperation division = (int a, int b) -> a / b;

          // 输出结果
          System.out.println("10 + 5 = " + tester.operate(10, 5, addition));
          System.out.println("10 - 5 = " + tester.operate(10, 5, subtraction));
          System.out.println("10 x 5 = " + tester.operate(10, 5, multiplication));
          System.out.println("10 / 5 = " + tester.operate(10, 5, division));

          // 没有括号的表达式            
          GreetingService greetService1 = message ->
          System.out.println("Hello " + message);

          // 有括号的表达式            
          GreetingService greetService2 = (message) ->
          System.out.println("Hello " + message);

          // 调用sayMessage方法输出结果
          greetService1.sayMessage("Java");
          greetService2.sayMessage("Classmate");
       }

       // 下面是定义的一些接口和方法

       interface MathOperation {
          int operation(int a, int b);
       }

       interface GreetingService {
          void sayMessage(String message);
       }

       private int operate(int a, int b, MathOperation mathOperation){
          return mathOperation.operation(a, b);
       }
}
```

需要注意的是：  

* Lambda表达式优先用于定义功能接口在行内的实现，即单个方法只有一个接口。在上面的例子中，我们用了多个类型的Lambda表达式来定义MathOperation接口的操作方法。然后我们定义了GreetingService的sayMessage的实现  
* Lambda表达式让匿名类不再需要，这为Java增添了简洁但实用的函数式编程能力。  

#### 作用域

我们可以通过下面这段代码来学习Lambda的作用域。请将代码修改至如下这些：  

```
public class NewFeaturesTester {
        final static String salutation = "Hello "; //正确，不可再次赋值
        //static String salutation = "Hello "; //正确，可再次赋值
        //String salutation = "Hello "; //报错
        //final String salutation = "Hello "; //报错

    public static void main(String args[]){
        //final String salutation = "Hello "; //正确，不可再次赋值
        //String salutation = "Hello "; //正确，隐性为 final , 不可再次赋值

        // salution = "welcome to "  
        GreetingService greetService1 = message -> 
        System.out.println(salutation + message);
        greetService1.sayMessage("Java");
    }

    interface GreetingService {
       void sayMessage(String message);
    }
}
```

可以得到以下结论：  

* 可访问static修饰的成员变量,如果是final static 修饰，不可再次赋值，只有static修饰可再次赋值；  
* 可访问表达式外层的final局部变量（不用声明为final，隐性具有final语义），不可再次赋值  

### 方法引用

> 方法也是一种对象，可以通过名字来引用，不过方法引用的唯一用途是支持Lambad的简写，使用方法名称来表示Lambad。不能通过方法引用来获得诸如方法签名的相关信息。引用自[永无止境，上下求索](http://blog.csdn.net/kimylrong/article/details/47255123) 的博客  


方法引用提供了一个很有用的语义来直接访问类或者实例的已经存在的方法或者构造方法。  

方法引用可以通过方法的名字来引用其本身。方法引用是通过::符号（双冒号）来描述的。  

它可以用来引用下列类型的方法：  

* 构造器引用。语法是Class::new,或者更一般的Class<T>::new,要求构造器方法是没有参数；  
* 静态方法引用。语法是Class::static_method,要求接受一个Class类型的参数;  
* 特定类的任意对象方法引用，它的语法是instance::method。要求方法接受一个参数，与3不同的地方在于，3是在列表元素上分别调用方法，而4是在某个对象上调用方法，将列表元素作为参数传入；  

更多对于方法引用的介绍，可以参考这一篇博文--[java8 - 方法引用(method referrance)](http://blog.csdn.net/wwwsssaaaddd/article/details/37573517) 

例子：

```
import java.util.List;
import java.util.ArrayList;

public class NewFeaturesTester {

    public static void main(String args[]){
        List<String> names = new ArrayList<>();

        names.add("Peter");
        names.add("Linda");
        names.add("Smith");
        names.add("Zack");
        names.add("Bob");

        //     通过System.out::println引用了输出的方法
        names.forEach(System.out::println);
    }
}
```

### 参考链接

* [The Java™ Tutorials - Lambda Expressions](http://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)
* [The Java™ Tutorials - Method References](http://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html)
* [java8 - 方法引用(method referrance)](http://blog.csdn.net/wwwsssaaaddd/article/details/37573517)
* [Java 8 特性 – 终极手册](http://www.importnew.com/19345.html)
* [JAVA8 十大新特性详解](http://www.jb51.net/article/48304.htm)
* [Java9都快发布了，Java8的十大新特性你了解多少呢？](http://www.cnblogs.com/pkufork/p/java_8.html)

