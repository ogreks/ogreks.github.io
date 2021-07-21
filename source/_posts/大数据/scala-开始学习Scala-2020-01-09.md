---
title: 开始学习Scala
date: 2020-01-09 15:28:00
categories:
 - Scala
 - 基础开发
tags:
 - scala
cover: https://s2.ax1x.com/2020/01/14/lLkl8K.jpg
---


## 开始学习Scala

> 老规矩，请您先自行安装 Scala

开始使用 Scala 的最简单的方式是使用交互式 Scala 解释器，只要输入 Scala 表达式， Scala 解释器会立即解释执行该语句并输出结果。当然你也可以使用如 Scala IDE 或 IntelliJ IDEA 集成开发环境。不过本教程开始还是以这种交互式 Scala 解释器为主。  

开始使用 Scala 的最简单的方式是使用交互式 Scala 解释器，只要输入 Scala 表达式， Scala 解释器会立即解释执行该语句并输出结果。当然你也可以使用如 Scala IDE 或 IntelliJ IDEA 集成开发环境。不过本教程开始还是以这种交互式 Scala 解释器为主。  

你可以使用 ***:help*** 命令列出一些常用的 Scala 解释器命令  

退出Scala解释器，输入:  
```
:quit
```

### 使用表达式

在 ***scala >*** 提示符下，你可以输入任意的Scala表达式，比如输入 ***1+2*** ,解释器显示：  
```
1+2
```

输入上面的表达式 结果显示包括：

* 一个由Scala解释器自动生成的变量名或者由你指定的变量名用来指向计算出来的结果（比如 ***res0*** 代表 ***result0*** 变量）
* 一个冒号，后来紧跟个变量类型比如 ***Int***  
* 一个等于号 ***=***
* 计算结果，本例为 ***1+2***的结果 ***3***  

***resX*** 变量名会用在之后得到表达式中, 比如: 此时 ***res0=3***, 如果输入 ***res0 *3***, 则显示 ***res1: Int =9*** 。  

### 定义一些变量

Scala 定义了两种类型的变量 ***val*** 和 ***var***, ***val*** 类似于Java中的 ***final*** 变量，一旦初始化之后，不可以重新复制（我们可以称它为 ***常变量***）.而 ***var*** 类似于一般的非 ***final*** 变量。 可以任意重新赋值。  

比如定义一个字符串常变量：  

```
scala> val msg = "Hello,World"
msg：String = Hello,World
```

这个表达式定义了一个msg变量，为字符串常量。它的类型为 ***string***(***java.lang.string***). 可以看到我们在定义这个变量是并不需要像Java一样定义其类型，Scala可以根据赋值的内容推算出变量的类型。这在Scala语言中称为"type inference"(类型推断)。 当然如果你愿意，你也可以采用和Java一样的方法，明确指定变量的类型，如：  
```
scala> val msg2:String ="Hello again,world"
msg2: String = Hello again,world
```

不过这样写就显得不像 Scala 风格了。此外 Scala 语句也不需要以分号结尾。 如果在命令行中需要分多行输入， Scala 解释器在新行前面显示 |，表示该行接着上一行。比如：  

```
scala> val msg3=
     | "Hello world 3rd time"
msg3: String = Hello world 3rd time
```

### 定义一些函数
Scala 既是面向对象的编程语言，也是面向函数的编程语言，因此函数在 Scala 语言中的地位和类是同等的。下面的代码定义了一个简单的函数求两个值的最大值：  

```
scala> def max(x:Int,y:Int) : Int ={
     | if (x >y) x
     | else
     | y
     | }
max: (x: Int, y: Int)Int
```

Scala 函数以 def 定义，之后是函数的名称（如 max )，然后是以逗号分隔的参数。Scala 中变量类型是放在参数和变量的后面，以 ： 隔开。这样做的一个好处是便于“ type inference ”。刚开始有些不习惯（如果你是 Pascal 程序员可能会觉得很亲切）。同样如果函数需要返回值，它的类型也是定义在参数的后面（实际上每个Scala函数都有返回值，只是有些返回值类型为 Unit ，类似于 void 类型）。  

此外每个 Scala 表达式都有返回结果（这一点和 Java，C# 等语言不同），比如 Scala 的 if else 语句也是有返回值的，因此函数返回结果无需使用 return 语句。实际上在Scala代码中应当尽量避免使用 return 语句。函数的最后一个表达式的值就可以作为函数的结果进行返回。  

同样由于 Scala 的“ type inference ”特点，本例其实无需指定返回值的类型。对于大多数函数 Scala 都可以推测出函数返回值的类型，但目前来说回溯函数（函数调用自身）还是需要指明返回结果类型的。  

下面再定义个“没有”返回结果的函数（其它语言可能称这种无返回值的函数为程式）。  

```
scala> def greet() = println("hello,world")
greet: ()Unit
```

greet 函数的返回值类型为 Unit ，表示该函数不返回任何有意义的值，Unit 类似于 Java 中的 void 类型。这种类型的函数主要用来获得函数的“副作用”，比如本函数的副作用是打印招呼语。  

### 使用while 实现循环

下面的代码使用 ***while*** 实现一个循环：  

```
val args = Array("I","like","scala")
var i=0
while (i < args.length) {
  println (args(i))
  i+=1
}
```
这里要注意的是Scala不支持++i和i++的运算符，因此需要使用 ***i += 1*** 来加一。这段代码看起来和Java代码差不多，实际上 ***while*** 也是一个函数，你自然可以利用Scala语言的扩展性，实现while语句，使它看起来和Scala语言自带的关键字一样调用。   

Scala 访问数组的语法是使用 ***()*** 来实现循环，但这种实现循环的方法并不是最好的 Scala 风格，在下一步介绍使用一种更好的方法来避免通过索引来枚举数组元素。  

### 使用 foreach 和 for 来实现迭代

在实验3.4中使用 ***while*** 来实现循环， 和使用Java实现无太大差异，而 Scala 是面向函数的语言，更好的方法时采用"函数式" 风格来编写代码。比如上面的循环，使用foreach方法如下：  

```
args.foreach(arg => println(arg))
```

该表达式，调用 ***args*** 的 ***foreach*** 方法，传入一个参数，这个参数类型也是一个函数( ***lambad*** 表达式，和C#中的概念类似)。这段代码可以再写的精简些，你可以利用Scala支持的缩写形式，如果一个函数只有一个参数并且只包含一个表达式，那么你无需明确指明参数。因此上面的代码可以写成：  

```
args.foreach(println)
```

Scala 中也提供了一个称为" for comprehension" 的功能，它比Java中的 ***for*** 功能更强大。 "for comprehension" (可称之为 for 表达式) 将在后面介绍，这里先试用 for 来实现前面的例子：  

```
for(arg <-args)
    println(arg)
```

### 使用类型参数化数组

在Scala 中，你可以使用new 来实例化一个类。当你创建一个对象的实例时，你可以使用数值或类型参数。如果使用类型参数，它的作用类似Java或 .net 的 ***Generic*** 类型。所不同的是，Scala使用方括号来指明数据类型参数，而非尖括号。比如：  

```
val greetStrings = new Array[String](3);
greetStrings(0) = "hello"
greetStrings(1) = ","
greetStrings(2) = "world!\n"
for(i <- 0 to 2)
    print(greetStrings(i))
```

可以看到 Scala 使用 [] 来为数组指明类型化参数，本例使用 String 类型，数组使用 () 而非 [] 来指明数组的索引。其中的 for 表达式中使用到 0 to 2 ，这个表达式演示了 Scala 的一个基本规则，如果一个方法只有一个参数，你可以不用括号和 . 来调用这个方法。  

因此这里的 0 to 2， 其实为 (0).to(2) 调用的为整数类型的 to 方法，to 方法使用一个参数。Scala 中所有的基本数据类型也是对象（和 Java 不同），因此 0 可以有方法（实际上调用的是 RichInt 的方法），这种只有一个参数的方法可以使用操作符的写法（不用 . 和括号），实际上 Scala 中表达式 1+2 ，最终解释为 (1).+(2)+，这也是 Int 的一个方法，和 Java 不同的是，Scala 对方法的名称没有太多的限制，你可以使用符号作为方法的名称。  

这里也说明为什么 Scala 中使用 () 来访问数组元素，在 Scala 中，数组和其它普遍的类定义一样，没有什么特别之处，当你在某个值后面使用 () 时，Scala 将其翻译成对应对象的 apply 方法。因此本例中 greetStrings(1) 其实是调用 greetString.apply(1) 方法。这种表达方法不仅仅只限于数组，对于任何对象，如果在其后面使用 () ，都将调用该对象的 apply 方法。同样的如果对某个使用 () 的对象赋值，比如：  

```
greetStrings(0) = "Hello"
```

Scala 将这种赋值转换为该对象的 update 方法， 也就是 greetStrings.update(0,”hello”) 。因此上面的例子，使用传统的方法调用可以写成：

```
val greetStrings =new Array[String](3)
greetStrings.update(0,"Hello")
greetStrings.update(1,",")
greetStrings.update(2,"world!\n")
for(i <- 0 to 2)
  print(greetStrings.apply(i))
```

从这点来说，数组在 Scala 中并不是某种特殊的数据类型，和普通的类没有什么不同。  

不过 Scala 还是提供了初始化数组的简单方法，比如上面的例子数组可以使用如下代码：  

```
val greetStrings =Array("Hello",",","World\n")
```

这里使用 () 其实还是调用 Array 类的关联对象 Array 的 apply 方法，也就是：  

```
val greetStrings =Array.apply("Hello",",","World\n")
```

### 使用Lists

Scala 也是一个面向函数的编程语言，面向函数的编程语言的一个特点是，调用某个方法不应该有任何副作用，参数一定，调用该方法后，返回一定的结果，而不会去修改程序的其他状态（副作用）。这样做的一个好处是方法和方法之间关联性较小，从而方法变得更加可靠和重用性高。使用这个原则也就意味着需要把变量设为不可修改的，这也就避免访问的互锁问题。  

前面介绍的数组，它的元素是可以被修改的。如果需要使用不可以修改的序列，Scala中提供了 ***Lists*** 类。和Java的 ***List*** 不同，Scala的 ***Lists*** 对象是不可修改的。它被设计用来满足函数编程风格的代码。它有点像Java 的 ***String***，***String*** 也是不可以修改的，如果需要可以修改的 ***String*** 对象，可以使用 ***StringBUlider*** 类  

比如下面的代码：  
```
val oneTwo = List(1,2)
val threeFour = List(3,4)
val oneTwoThreeFour = oneTwo ::: threeFour
println (oneTwo + " and " + threeFour + " were not mutated. ")
println ("Thus," + oneTwoThreeFour + " is a new list")
```

定义了两个 ***List*** 对象 ***oneTwo*** 和 ***threeFour***，然后通过 ***:::*** 操作符 （其实为 ***:::*** 方法） 将两个列表连接起来。实际上由于 ***List*** 的不可修改特性，Scala创建了一个新的 ***List*** 对象 ***oneTwoThreeFour*** 来保存两个列表连接后的值。  

***List*** 也提供了一个 ***::*** 方法用来像 ***list*** 中添加一个元素， ***::*** 方法（操作符）是右操作符，也就是使用 ***::*** 右边的对象来调用它的 ***::*** 方法，Scala 中的规定所有以 ***:*** 开头的操作符都是右操作符，因此如果你自己定义以 ***:*** 开头的方法（操作符）也是右操作符。   

如下面使用常量创建一个列表：  

```
val oneTwoThree = 1 :: 2 :: 3 :: Nil
println(oneTwoThree)
```

调用空列表兑现 ***Nil*** 的 ***::*** 方法也就是：  

```
val oneTwoThree = Nil.::(3).::(2).::(1)
```

Scala 的 ***List*** 类还定义了其他很多很有用的方法，比如 ***head***、***last***、***length***、***reverse***、***tail***等。这里就不一一说明了，具体可以参考 ***List*** 文档。  

### 使用元组(Tuples)

Scala 中另外一个很有用的容器类为 ***Tuples*** ，和 ***List*** 不同的是， ***Tuples*** 可以包含不同类型的数据，而 ***List*** 只能包含同类型的数据， ***Tuples*** 在方法需要返回多个结果时非常有用。（ ***Tuple*** 对应到数字的 ***矢量*** 的概念）。  

一旦定义了一个元组，可以使用 ***._*** 和 ***索引*** 来访问元组的元素（矢量的分量，主义和数组不听的是，元组的索引从1开始）。  

```
val pair = (99,"Luftballons")
println(pair._1)
println(pair._2)
```

元组的实际类型取决于它的分量的类型，比如上面的 ***pair*** 的类型实际为 ***Tuple2[Int,String]*** , 而 ***('u','r','the',1,4,'me')*** 的类型为 ***Tuple6[Char,Char,String,Int,Int,String]***   

目前Scala支持的元组的最大长度为22。如果有需要，你可以自己扩展更长的元组  

### 使用Sets 和 Maps

Scala Set(集合)是没有重复对象的集合，所有的元素都是唯一的。  

Scala 语言的一个设计目标是让程序员可以同时利用面向对象和面向函数的方法编写代码，因此它提供的集合类分成了可以修改的集合类和不可以修改的集合类两大类型。比如 ***Array*** 总是可以修改内容的，而 ***List*** 总是不可以修改内容的。类似的情况，***Scala*** 也提供了两种 ***Sets*** 和 ***Maps*** 集合类。  

比如 Scala API 定义了 ***set*** 的 ***基Trait*** 类型 ***Set*** ( ***Trait*** 的概念类似于Java中的 ***Interface*** ), 所不同的是Scala中的 ***Trait*** 可以有方法的实现，分两个包定义 ***Mutable*** (可变) 和 ***Immutable*** (不可变)， 使用同样名称的子 ***Trait***。   

使用 ***Set*** 的基本方法如下：  

```
var jetSet = Set("Boeing","Airbus")
jetSet += "Lear"
println(jetSet.contains("Cessna"))
```

缺省情况 ***Set*** 为 ***Immutable Set***, 如果你需要使用可修改的集合类（***Set*** 类型）,你可以使用全路径来指明 ***Set*** 比如 ***scala.collection.mutable.Set***。  

Scala 提供的另一个类型为 ***Map*** 类型，Map(映射)是一种可迭代的key/value结构，所有的值都可以通过键来获取。Scala也提供了 ***Mutable*** 和 ***Immutable*** 两种Map类型。  

Map的基本用法如下（Map类似其他语言中的关联数组，如PHP）  

```
val romanNumeral = Map( 1 -> "I", 2 -> "II", 3 -> "III", 4 -> "IV", 5 -> "V")
println(romanNumeral(4))
```

### 学习识别函数编程风格   

Scala语言的一个特点是支持面向函数编程，因此学习Scala的一个重要方面是改变之前的指令时编程思想（尤其是来自Java或C#背景的程序员），观念要向函数式编程转变。首先在看代码上要认识哪种是指令编程，哪种是函数式编程，实现这种思想上的转变，不仅仅会使你成为一个更好的Scala程序员，同时也会扩展你的视野，使你成为一个更好的程序员。  

一个简单的原则，如果代码中含有 ***var*** 类型的变量，这段代码就是传统的指令式编程，如果代码只有 ***val*** 变量，这段代码就很有可能是函数式代码，因此学会函数式编程关键是不适用 ***vars*** 来编写代码。  

来看一看简单的例子：  
```
def printArgs ( args: Array[String]) : Unit = {
    var i=0;
    while(i < args.length){
        println (args(i));
        i+=1;
    }
}
```

来自Java 背景的程序员开始写Scala代码很有可能写成上面的实现。我们试着去除vars变量，可以写成更符合函数式编程的代码：  

```
def printArgs(args: Array[String]) : Unit = {
    for(arg <- args)
        println(arg)
}
```

或者更简化为：  

```
def printArgs (args:Array[String]) : Unit = {
    args.foreach(println)
}
```

这个例子也说明了尽量少用 ***vars*** 的好处，代码更简洁明了，从而也可以减少错误的发生。因此Scala编程的一个基本原则是，能不用 ***vars*** ，尽量不用 ***vars***， 能不用 ***mutable***变量，尽量不用 ***mutable*** 变量，能避免函数的副作用，就尽量不产生副作用。  
### 读取文件

使用脚本实现某个人物，通常需要读取文件，本节介绍Scala读写文件的基本方法。比如下面的例子读取文件的每行，把该行字符长度添加到行首：  
```
import scala.io.Source

var args=Source.fromFile("/home/hadoop/test.text") # 记得在对应位置创建文件，并写入内容

for( line <- args.getLines)
    println(line.length + "" + line)
```

可以看到Scala引入包的方式和Java类似，也是通过 ***import*** 语句。文件相关的类定义在 ***scala.io*** 包中。如果需要引入多个类，Scala 使用 ***_***而非 ***\****