---
title: Spring 松耦合
date: 2020-04-02 17:00:00
categories:
 - java
 - Spring
tags:
 - java
 - Spring
cover: https://ae01.alicdn.com/kf/U0f72609504414433bbf24e0d11d81e43S.png
---

## Spring 松耦合

> 在电脑运算和系统设计中，一个松耦合的系统中的每一个组件对其他独立组件的定义所知甚少或一无所知。子范围包括类、接口、数据和服务之间的耦合。 松耦合是紧耦合的对立面。

### 优点和缺点

* 优点

松耦合系统中的组件能够被提供相同服务的替代实现所替换。松耦合系统中的组件不太受相同的平台、语言、操作系统或构建环境的约束。

* 缺点

如果系统在时间上是解耦的，那么也很难提供事务完整性；需要额外的协调协议。跨系统的数据复制提供了松耦合性（可用性），但是造成了维护一致性（数据同步）的问题。
  
  
### 松耦合的目的

上一部分，我们已经创建了 Maven 项目，打印出了 HelloShiyanlou。为了方便，我使用上面的工程，pom.xml 文件一致，不必修改。下面，我们实验证明 Spring 的松耦合。假设项目需要输出到 CSV 或者 JSON。

### 松耦合代码编写

#### IOutputGenerator.java

创建 com.study.demo.loosely_coupled 包，新建一个 IOutputGenerator 接口，接口内容如下：

```
package com.study.demo.loosely_coupled;

public interface IOutputGenerator{
  public void generateOutput();
}
```

#### CsvOutputGenerator.java

CSV 输出，实现了 IOutputGenerator 接口。同样的包路径，新建一个 CsvOutputGenerator.java

内容如下：

```
package com.study.demo.loosely_coupled;

public class CsvOutputGenerator implements IOutputGenerator{

  public void generateOutput(){
    System.out.println("Creating CsvOutputGenerator  Output......");
  }

}
```

#### JsonOutputGenerator.java

JSON 输出，实现了 IOutputGenerator 接口。同样的包路径，新建一个 JsonOutputGenerator.java。 

内容如下：

```
package com.study.demo.loosely_coupled;

public class JsonOutputGenerator implements IOutputGenerator{

  public void generateOutput(){
    System.out.println("Creating JsonOutputGenerator  Output......");
  }

}

```

### 用 Spring 依赖注入调用输出

用 Spring 的松耦合实现输出相应的格式。 首先在 com.shiyanlou.demo.loosely_coupled 包内创建一个需要用到输出的类 OutputHelper.java

内容如下：

```
package com.study.demo.loosely_coupled;

public class OutputHelper{
  IOutputGenerator outputGenerator;

  public void generateOutput(){
    this.outputGenerator.generateOutput();
  }

  public void setOutputGenerator(IOutputGenerator outputGenerator){
    this.outputGenerator = outputGenerator;
  }
}
```

### 创建一个 spring 配置文件

此文件用于依赖管理 src/main/resources 下创建配置文件 Spring-Output.xml。

```
<?xml version = "1.0" encoding = "UTF-8"?>
<beans xmlns = "http://www.springframework.org/schema/beans"
       xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation = "http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id = "OutputHelper" class = "com.study.demo.loosely_coupled.OutputHelper">
        <property name = "outputGenerator" ref = "CsvOutputGenerator" />
    </bean>
    <bean id = "CsvOutputGenerator" class = "com.study.demo.loosely_coupled.CsvOutputGenerator" />
    <bean id = "JsonOutputGenerator" class = "com.study.demo.loosely_coupled.JsonOutputGenerator" />
</beans>
```

### App.java

修改此文件用于通过 Spring 调用相应的 output

内容如下：

```
package com.study.demo;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.study.demo.loosely_coupled.OutputHelper;

public class App{

  private static ApplicationContext context;

  public static void main( String[] args ){
    context = new ClassPathXmlApplicationContext(new String[] {"Spring-Output.xml"});

    OutputHelper output = (OutputHelper)context.getBean("OutputHelper");
    output.generateOutput();
  }
}
```

现在已经实现了松耦合，当需要输出改变时，不必修改任何代码 .java 文件，只要修改 **Spring-Output.xml** 文件 ***<property name = "outputGenerator" ref = "CsvOutputGenerator" />*** 中的 ref 值，就可以实现输出不同的内容，不修改代码就减少了出错的可能性。

### 运行结果

输入命令：

```
mvn compile
mvn exec:java -Dexec.mainClass = "com.study.demo.App"
```

**注意：修改文件后都要使用 mvn compile 重新编译，然后再运行。**

当 Spring-Output 如下时：

```
 <bean id = "OutputHelper" class = "com.study.demo.loosely_coupled.OutputHelper">
        <property name = "outputGenerator" ref = "JsonOutputGenerator" />
 </bean>
 ```
 
输出 结果 又如何？？