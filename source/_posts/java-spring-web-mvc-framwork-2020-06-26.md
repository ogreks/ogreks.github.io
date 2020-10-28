---
title: Spring Web MVC 框架
date: 2020-06-26 02:08:09
categories:
 - java
 - Spring
tags:
 - java
 - Spring
 - 事务管理
cover: https://ae01.alicdn.com/kf/U0f72609504414433bbf24e0d11d81e43S.png
---

## Spring Web MVC 框架

Spring MVC 框架，这个框架适用于 Web 开发的，它代替了 servlet 的页面跳转。

本篇是对 Spring MVC 框架的一个简单讲解，不会讲理论只是，如果想进一步学习的话，可以看 [Spring MVC 简易教程]()

### 知识点

* Spring Web Hello World 例子
* Spring MVC 表单处理例子
* Spring MVC 页面重定向例子
* Spring MVC 异常处理例子

### Spring Web Hello World 例子

首先创建一个新的项目

```
mvn archetype:generate -DgroupId=com.learn-DartifactId=springMVCTest -DarchetypeArtifactId=maven-archetype-webapp
```

修改 pom.xml 文件，添加 Spring 的依赖：

```
<project xmlns = "http://maven.apache.org/POM/4.0.0" xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.learn</groupId>
    <artifactId>springMVCTest</artifactId>
    <packaging>war</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>springMVCTest Maven Webapp</name>
    <url>http://maven.apache.org</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <spring.version>5.1.1.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.2</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <!--jetty maven 插件，为 maven 提供运行 web 程序的能力-->
                <groupId>org.eclipse.jetty</groupId>
                <artifactId>jetty-maven-plugin</artifactId>
                <version>9.4.12.v20180830</version>
                <configuration>
                    <scanIntervalSeconds>10</scanIntervalSeconds>
                    <webApp>
                        <contextPath>/</contextPath>
                    </webApp>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

修改 web.xml 文件内容如下：
```
<?xml version = "1.0" encoding = "UTF-8"?>
<web-app xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xmlns = "http://java.sun.com/xml/ns/javaee" xmlns:web = "http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
    xsi:schemaLocation = "http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
    id = "WebApp_ID" version = "3.0">
    <display-name>springMVCTest</display-name>

    <!-- 配置 Spring MVC DispatchcerServlet 前端控制器 -->
    <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <!-- contextConfigLocation 是参数名称，该参数的值包含 Spring MVC 的配置文件路径 -->
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/springmvc-config.xml</param-value>
        </init-param>
        <!-- 在 Web 应用启动时立即加载 Servlet -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <!-- Servlet 映射声明 -->
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <!-- 监听当前域的所有请求 -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
        <!-- 添加 register.jsp 为首页 -->
    <welcome-file-list>
        <welcome-file>register.jsp</welcome-file>
    </welcome-file-list>
</web-app>
```
在 web.xml 中配置了 DispatchcerServlet，DispatchcerServlet 加载时需要一个 Spring MVC 的配置文件，默认会去 WEB-INF 下查找对应的 servlet-name-servlet.xml 文件，如本例中默认查找的是 springmvc-servlet.xml。

Spring MVC 的配置文件可以放在任何地方，用 servlet 的子元素 init-param 描述即可，见上述示例代码，这时 DispatchcerServlet 就会去查找 /WEB-INF/springmvc-config.xml。

在 webapp/WEB-INF/ 目录下新建 Spring MVC 配置文件 springmvc-config.xml，配置 Spring MVC 的 Controller，添加如下代码：

```
<?xml version = "1.0" encoding = "UTF-8"?>
<beans xmlns = "http://www.springframework.org/schema/beans"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance" xmlns:context = "http://www.springframework.org/schema/context"
    xmlns:mvc = "http://www.springframework.org/schema/mvc"
    xsi:schemaLocation = "http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd">

    <!-- 开启注解 -->
    <mvc:annotation-driven />
    <!-- 配置自动扫描的包，完成 Bean 的创建和自动依赖注入的功能 -->
    <context:component-scan base-package = "com.learn.controller" />
    <!-- 默认静态资源处理 -->
    <mvc:default-servlet-handler/>
    <!-- 配置视图解析器 -->
    <bean id = "viewResolver" class = "org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name = "prefix" value = "/WEB-INF/views/"></property>
        <property name = "suffix" value = ".jsp"></property>
    </bean>
</beans>
```
在包 com.learn.controller 下新建 HelloController 类 HelloController.java，具体解释注释已经给出，代码如下：

```
package com.learn.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * Controller 是一个基于注解的控制器
 * 可以同时处理多个请求动作
 */
@Controller
public class HelloController {
    /**
     * RequestMapping 用来映射一个请求和请求的方法
     * value = "/hello" 表示请求由 hello 方法进行处理
     */
    @RequestMapping(value = "/hello")
    public String hello() {
        // 返回一个字符串 "success" 作为视图名称
        return "hello";
    }
}
```
在 webapp/WEB-INF 目录下新建文件夹 views，并在该路径下新建一个 JSP 页面命名为 hello.jsp，代码如下：
```
<html>
  <body>
    <h2>Hello World!</h2>
  </body>
</html>
```
输入 mvn jetty:run 运行程序，访问 spring mvc。

> 地址 ***127.0.0.1:8080/hello***

效果：
```
springMVCTest/ $ curl 127.0.0.1:8080/hello
<html>
    <body>
        <h2>Hello World!</h2>
    </body>
</html>%     
```

### Spring MVC 表单处理

在项目目录 src/main/java 的包 com.learn.entity 下新建类 User.java，包含 id、username、password 和 age 属性，代码如下：

```
package com.learn.entity;

import java.io.Serializable;

public class User implements Serializable {

    private static final long serialVersionUID = 1L;
    private Integer id;
    private String username;
    private String password;
    private Integer age;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

}
```

在包 com.learn.controller 下新建 Controller 类 UserController.java，具体解释注释已经给出，代码如下：

```
package com.learn.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import com.learn.entity.User;

/**
 * UserController 是一个基于注解的控制器
 * 可以同时处理多个请求动作
 */
@Controller
public class UserController {
    /**
     * RequestMapping 用来映射一个请求和请求的方法
     * value = "/register" 表示请求由 register 方法进行处理
     */
    @RequestMapping(value = "/register")
    public String Register(User user, Model model) {  // user:视图层传给控制层的表单对象；model：控制层返回给视图层的对象
        // 在 model 中添加一个名为 "user" 的 user 对象
        model.addAttribute("user", user);
        // 返回一个字符串 "success" 作为视图名称
        return "success";
    }
}
```
在 webapp 目录下新建一个 JSP 页面命名为 register.jsp，代码如下：
```
<%@ page language = "java" contentType = "text/html; charset = UTF-8"
pageEncoding = "UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset = UTF-8" />
    <title>register page</title>
  </head>
  <body>
    <form action="register" method="post">
      <h5>User registration</h5>
      <p>
        <label>name </label>
        <input type="text" id="username" name="username" tabindex="1" />
      </p>

      <p>
        <label>password </label>
        <input type="text" id="password" name="password" tabindex="2" />
      </p>

      <p>
        <label>age </label>
        <input type="text" id="age" name="age" tabindex="3" />
      </p>
      <p id="buttons">
        <input id="submit" type="submit" tabindex="4" value="register" />
        <input id="reset" type="reset" tabindex="5" value="reset" />
      </p>
    </form>
  </body>
</html>
```
在 webapp/WEB-INF/views 新建一个 JSP 页面命名为 success.jsp，代码如下：
```
<%@ page language = "java" contentType = "text/html; charset = UTF-8"
pageEncoding = "UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset = UTF-8" />
    <title>success page</title>
  </head>
  <body>
    <h5>login was successful</h5>
    <p>
      name：${requestScope.user.username}<br />
      password：${requestScope.user.password}<br />
      age：${requestScope.user.age}<br />
    </p>
  </body>
</html>
```

输入 mvn jetty:run 运行程序

然后在 浏览器直接访问 之前的地址即可

### Spring 页面重定向

修改 HelloController.java 代码如下
```
package com.learn.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloController {

    @RequestMapping(value = "/hello")
    public String hello() {
        return "hello";
    }

    @RequestMapping(value = "/redirect")
    public String redirect(){
        return "redirect:finalPage";
    }

    @RequestMapping(value = "/finalPage")
    public String finalPage(){
        return "final";
    }
}
```
修改 hello.jsp 代码如下：
```
<%@ page contentType = "text/html; charset = UTF-8" language = "java" %>
<%@taglib uri = "http://www.springframework.org/tags/form" prefix = "form"%>
<html>
<head>
    <title>Spring MVC页面重定向</title>
</head>
<body>
<h2>Spring MVC页面重定向</h2>
<p>点击下面的按钮将结果重定向到新页面</p>
<form:form method = "GET" action = "redirect">
    <table>
        <tr>
            <td><input type = "submit" value = "页面重定向" /></td>
        </tr>
    </table>
</form:form>
</body>
</html>
```
在同目录下新建 final.jsp，代码如下：
```
<%@ page contentType = "text/html; charset = UTF-8" language = "java" %>
<%@taglib uri = "http://www.springframework.org/tags/form" prefix = "form"%>
<html>
<head>
    <title>Spring重定向页面</title>
</head>
<body>
<h2>重定向页面...</h2>
</body>
</html>
```
输入 mvn jetty:run 运行程序

然后访问 127.0.0.1:8080/hello 地址即可

### Spring MVC 异常处理

在 com.learn.controller 包下新建 ExceptionIntegratedHandleController.java，代码如下:

```

package com.learn.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class ExceptionIntegratedHandleController {

    @RequestMapping("/exceptionIntegratedHandleController/{id}")
    @ResponseBody
    public Object hello(@PathVariable String id) {

        if (id.equals("1")) {
            throw new RuntimeException("我这里是手动抛出的异常,期望被SpringMVC集中处理");
        } else if (id.equals("2")) {
            int value = 1 / 0;
            return "手动运算错误";
        } else {
            return "no exception";
        }
    }
}
```
然后在 com.learn.exception 包下新建 MySpringExceptionIntegratedHandler.java，代码如下：

```
package com.learn.exception;

import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ModelAndView;

// 自己手动编写 Java 代码来实现定制异常信息处理
public class MySpringExceptionIntegratedHandler implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(javax.servlet.http.HttpServletRequest request, javax.servlet.http.HttpServletResponse response, Object handler, Exception ex) {
        // 这里可以根据异常的类型来决定什么样的对策
        System.out.println("异常已经被处理了");
        System.out.println("异常的类型是:" + ex.getClass().getName());
        request.setAttribute("ex", ex);
        return new ModelAndView("runtimeExceptionPage");
    }
}
```
然后在 springmvc-config.xml 中加入：
```
<bean id = "mySpringExceptionIntegratedHandler" class = "com.learn.exception.MySpringExceptionIntegratedHandler"/>
```
最后在 views 目录下新建一个 runtimeExceptionPage.jsp，代码如下：
```
<%@ page contentType = "text/html; charset = UTF-8"%>
<html>
    <head><title>Exception!</title></head>
<body>
    <% Exception e = (Exception)request.getAttribute("ex"); %>
    <H2>业务错误: <%= e.getClass().getSimpleName()%></H2>
    <hr />
    <P>错误描述：</P>
    <%= e.getMessage()%>
    <P>错误信息：</P>
    <% e.printStackTrace(new java.io.PrintWriter(out)); %>
</body>
</html>
```
输入 mvn jetty:run 运行程序

[![ND2HUg.md.png](https://s1.ax1x.com/2020/06/26/ND2HUg.md.png)](https://imgchr.com/i/ND2HUg)

