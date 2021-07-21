---
title: Spring IOC 容器
date: 2020-06-25 19:31:03
categories:
 - java
 - Spring
tags:
 - java
 - Spring
 - IOC
cover: https://ae01.alicdn.com/kf/U0f72609504414433bbf24e0d11d81e43S.png
---

## Spring IOC 容器

### 理论基础

#### IoC 是什么

Ioc，Inversion of Control，即“控制反转”。它不是什么技术，而是一种设计思想。在 Java 开发中，Ioc 意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。如何理解好 Ioc 呢？理解好 Ioc 的关键是要明确“谁控制谁，控制什么，为何是反转（有反转就应该有正转了），哪些方面反转了”，那我们来深入分析一下：

* 谁控制谁，控制什么：传统 Java SE 程序设计，我们直接在对象内部通过 new 进行创建对象，是程序主动去创建依赖对象；而 IoC 是有专门一个容器来创建这些对象，即由 IoC 容器来控制对象的创建；谁控制谁？当然是 IoC 容器控制了对象；控制什么？那就是主要控制了外部资源获取（不只是对象，还包括文件等）。

* 为何是反转，哪些方面反转了：有反转就有正转，传统应用程序是由我们自己在对象中主动控制去直接获取依赖对象，也就是正转；而反转则是由容器来帮忙创建及注入依赖对象；为何是反转？因为由容器帮我们查找及注入依赖对象，对象只是被动的接受依赖对象，所以是反转；哪些方面反转了？依赖对象的获取被反转了。

用图例说明一下，传统程序设计都是主动去创建相关对象然后再组合起来：

![图一](https://ae01.alicdn.com/kf/Ub4bb3b981a2d4e509d813ab5af22631c0.png)

当有了 IoC/DI 的容器后，在客户端类中不再主动去创建这些对象了，如图：

![图二](https://ae01.alicdn.com/kf/U294e29fb17f54c5d952d5de7f705db22q.png)

#### IoC 能做什么


IoC 不是一种技术，只是一种思想，一个重要的面向对象编程的法则，它能指导我们如何设计出松耦合、更优良的程序。传统应用程序都是由我们在类内部主动创建依赖对象，从而导致类与类之间高耦合，难于测试；有了 IoC 容器后，把创建和查找依赖对象的控制权交给了容器，由容器进行注入组合对象，所以对象与对象之间是松散耦合，这样也方便测试，利于功能复用，更重要的是使得程序的整个体系结构变得非常灵活。

其实 IoC 对编程带来的最大改变不是从代码上，而是从思想上，发生了“主从换位”的变化。应用程序原本是老大，要获取什么资源都是主动出击，但是在 IoC/DI 思想中，应用程序就变成被动的了，被动的等待 IoC 容器来创建并注入它所需要的资源了。

IoC 很好的体现了面向对象设计法则之一的好莱坞法则：“别找我们，我们找你”。即由 IoC 容器帮对象找相应的依赖对象并注入，而不是由对象主动去找。

#### IoC 和 DI

DI，Dependency Injection，即“依赖注入”：是组件之间依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

理解 DI 的关键是：“谁依赖谁，为什么需要依赖，谁注入谁，注入了什么”。我们来深入分析一下：

* 谁依赖于谁：当然是某个容器管理对象依赖于 IoC 容器；“被注入对象的对象”依赖于“依赖对象”。
* 为什么需要依赖：容器管理对象需要 IoC 容器来提供对象需要的外部资源。
* 谁注入谁：很明显是 IoC 容器注入某个对象，也就是注入“依赖对象”。
* 注入了什么：就是注入某个对象所需要的外部资源，包括对象、资源、常量数据。

IoC 和 DI 有什么关系呢？其实它们是同一个概念的不同角度描述，由于控制反转概念比较含糊（可能只是理解为容器控制对象这一个层面，很难让人想到谁来维护对象关系），所以 2004 年大师级人物 Martin Fowler 又给出了一个新的名字：“依赖注入”，相对 IoC 而言，“依赖注入”明确描述了“**被注入对象依赖 IoC 容器配置依赖对象**”。

#### IoC 容器

IoC 容器就是具有依赖注入功能的容器，IoC 容器负责实例化、定位、配置应用程序中的对象及建立这些对象间的依赖。应用程序无需直接在代码中 new 相关的对象，应用程序由 IoC 容器进行组装。在 Spring 中 BeanFactory 是 IoC 容器的实际代表者。

Spring IoC 容器如何知道哪些是它管理的对象呢？

> 这就需要配置文件，Spring IoC 容器通过读取配置文件中的配置元数据，通过元数据对应用中的各个对象进行实例化及装配。一般使用基于 xml 配置文件进行配置元数据，而且 Spring 与配置文件完全解耦的，可以使用其他任何可能的方式进行配置元数据，比如注解、基于 java 文件的、基于属性文件的配置都可以。

> 在 Spring Ioc 容器的代表就是 org.springframework.beans 包中的 BeanFactory 接口，BeanFactory 接口提供了 IoC 容器最基本功能；而 org.springframework.context 包下的 ApplicationContext 接口扩展了 BeanFactory，还提供了与 Spring AOP 集成、国际化处理、事件传播及提供不同层次的 context 实现，如针对 web 应用的 WebApplicationContext。简单说，BeanFactory 提供了 IoC 容器最基本功能，而 ApplicationContext 则增加了更多支持企业级功能支持。ApplicationContext 完全继承 BeanFactory，因而 BeanFactory 所具有的语义也适用于 ApplicationContext。

* XmlBeanFactory：BeanFactory 实现，提供基本的 IoC 容器功能，可以从 classpat h 或文件系统等获取资源。
```
File file = new File("fileSystemConfig.xml");
Resource resource = new FileSystemResource(file);
BeanFactory beanFactory = new XmlBeanFactory(resource);
```

```
Resource resource = new ClassPathResource("classpath.xml");
BeanFactory beanFactory = new XmlBeanFactory(resource);
```

* ClassPathXmlApplicationContext：ApplicationContext 实现，从 classpath 获取配置文件。 
```
BeanFactory beanFactory = new ClassPathXmlApplicationContext("classpath.xml");
```

* FileSystemXmlApplicationContext：ApplicationContext 实现，从文件系统获取配置文件。

```
BeanFactory beanFactory = new FileSystemXmlApplicationContext("fileSystemConfig.xml");
```

### Spring 中 Bean 的定义及注入 Value

Spring 中，bean 的定义有三种方式：

1. 基于 XML 的配置
2. 基于注解的配置
3. 基于 Java 类的配置

Bean 的注入有两种方式：基于构造函数的依赖注入和基于设值函数的依赖注入。

这里我们先给大家介绍第一种，基于 XML 的配置方法，这种方法在 Spring 中是最常见的

#### 基于 XML 的配置方法

基于 XML 的配置方法又分为三种写法：一般方法，缩写方法，pschema 方法。先看下面的 Bean：FileNameGenerator.java，其中包含两个 properties，name 和 type，我们向两个 properties 注入 value。

新建一个 Maven 项目，步骤如下：

```
mvn archetype:generate -DgroupId=com.nobady.spring -DartifactId=bean -DarchetypeArtifact
Id=maven-archetype-quickstart
```

执行上述命令之后我们创建了一个 bean 的项目

然后切换工作区

修改 pom.xml 文件

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.nobady.spring</groupId>
  <artifactId>bean</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>bean</name>
  <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <spring.version>5.1.1.RELEASE</spring.version>
  </properties>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
  </dependencies>
</project>
```

在 src/main/java 路径下，新建类：FileNameGenerator.java，所属包为：com.shiyanlou.spring.bean，内容为：

```
package com.shiyanlou.spring.bean;

public class FileNameGenerator {
    private String name;
    private String type;

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public String getType() {
        return type;
    }
    public void setType(String type) {
       this.type = type;
    }
    /*
    *打印文件名和文件类型的方法
    */
    public void printFileName() {
        System.out.println("FileName & FileType  is  :  "+name+" & "+type);
    }
}
```

我们先在 src/main/ 下新建一个 Folder，命名为 resources，接着在 src/main/resources 路径下新建 SpringBeans.xml 文件：

```
<?xml version = "1.0" encoding = "UTF-8"?>
<beans xmlns = "http://www.springframework.org/schema/beans"
       xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation = "http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--一般方法-->
    <bean id = "FileNameGenerator" class = "com.nobady.spring.bean.FileNameGenerator">
        <property name = "name">
            <value>hehe</value>
        </property>
        <property name = "type">
            <value>txt</value>
        </property>
    </bean>
    <!-- 另一重配置方法 缩写方法-->
    <!--
        <bean id = "FileNameGenerator" class = "com.nobady.spring.bean.FileNameGenerator">
               <property name = "name" value = "ceshi" />
               <property name = "type" value = "txt" />
           </bean>
     -->
</beans>
```
第三种方法：pschema 方法。

```
<?xml version = "1.0" encoding = "UTF-8"?>
<beans xmlns = "http://www.springframework.org/schema/beans"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p = "http://www.springframework.org/schema/p"
    xsi:schemaLocation = "http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--<bean id = "FileNameGenerator" class = "com.nobady.spring.bean.FileNameGenerator">
        <property name = "name">
            <value>study</value>
        </property>
        <property name = "type">
            <value>txt</value>
        </property>
    </bean>-->
    <bean id = "FileNameGenerator" class = "com.nobady.spring.bean.FileNameGenerator"
             p:name = "study" p:type = "txt" />
</beans>
```

注意，这种方法需要在 bean 的配置文件 xml 中，加入声明。

```
xmlns:p = "http://www.springframework.org/schema/p"
```

然后直接修改 App.java

```
package com.nobady.spring;

import com.nobady.spring.bean.FileNameGenerator;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * Hello world!
 *
 */
public class App
{
    private static ApplicationContext context;

    public static void main( String[] args )
    {
        context = new ClassPathXmlApplicationContext("SpringBeans.xml");

        FileNameGenerator obj = (FileNameGenerator) context.getBean("FileNameGenerator");
        obj.printFileName();

    }
}

```

然后打开终端开始执行

```
mvn compile
mvn exec:java -Dexec.mainClass="com.nobady.spring.App"
```

![图3](https://ae01.alicdn.com/kf/U930b8694475a42dca7d31b59f4df407cK.png)

#### 基于注解的配置

注解是为 Spring 容器提供 Bean 定义的信息，把 XML 定义的信息通过类注解描述出来。众所周知，Spring 容器三大要素：Bean 定义、Bean 实现类以及 Spring 框架。如果采用 XML 配置，Bean 定义和 Bean 实现类本身分离，而采用注解配置，Bean 定义在 Bean 实现类上注解就可以实现。以下先简单列举几个注解方式。

##### @Component
被此注解标注的类将被 Spring 容器自动识别，自动生成 Bean 定义。即

```
packeage com.nobady.spring;

@Component("Study")
public class Study{

}
```

与在 XML 中配置以下效果相同：

```
<bean id = "shiyanlou" class = "com.shiyanlou.spring.shiyanlou">
```

除此之外，Spring 有三个与 @Component 等效的注解：

1. @Controller：对应表现层的 Bean，也就是 Action。
2. @Service：对应的是业务层 Bean。
3. @Repository：对应数据访问层 Bean。

##### @Autowired

@Autowired 可以用来装配 bean，都可以写在字段上，或者方法上。使用 @Autowired，首先要在在 applicationContext.xml 中加入 ***<bean class = "org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor"/>*** 。@Autowired 默认按类型装配，默认情况下要求依赖对象必须存在，如果要允许 null 值，可以设置它的 required 属性为 false。例如：

```
@Autowired()
@Qualifier("studentDao")
private StudentDao studentDao;
```

##### @Configuration

通过使用注释 @Configuration 告诉 Spring，这个 Class 是 Spring 的核心配置文件，并且通过使用注解 @Bean 定义 bean，举例说明

```
package com.nobady.spring.java_config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean(name = "animal")
    public IAnimal getAnimal(){
        return new Dog();
    }
}
```

App.java 内容：

```
package com.nobady.spring.java_config;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class App {

    private static ApplicationContext context;

    public static void main(String[] args) {
        context = new AnnotationConfigApplicationContext(AppConfig.class);
        IAnimal obj = (IAnimal) context.getBean("animal");
        obj.makeSound();

    }

}
```

通过上面的 @Configuration 注解，相当于在 ApplicationContext.xml 文件中添加如下配置，使用了 @Configuration + @Bean 就不需要添加了：

```
<bean id = "animal" class = "com.shiyanlou.spring.java_config.Dog">
```



