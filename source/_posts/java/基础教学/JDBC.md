---
title: 关于 Java JDBC 的使用
date: 2019-12-20 10:00:00
categories:
 - java
 - java 基础
tags:
 - java
 - java JDBC
cover: https://s2.ax1x.com/2019/12/11/QsYkmn.png
---

## Java JDBC

> JDBC 是连接数据库和Java程序的桥梁，通过JDBC API 可以方便地实现对各种主流数据库的操作. 本节将重点讲解JDBC的内容  

本章概括:  

* SQL 简介  
* JDBC  
* 创建数据库  
* 数据库操作  
* JDBC 结果集
* 插入数据

> 小提示 在已经掌握了 关系数据库 和 非关系数据库的小伙伴 可以进行适当跳章

### 数据库简介

数据库，简而言之可以理解成电子化的文件柜--存储点子文件的处所，用户可以对文件中的数据运行新增、截取、更新、删除等操作  

所谓"数据库"系以一定方式存储在一起、能予多个用户共享、具有尽可能小的冗余度、与应用程序彼此独立的数据集合。一个数据库由多个表空间（Tablespace）构成.  

常见的关系型数据库有以下几种:  

* Mysql  
  * MariaDB  
  * Percona Server  
* PostgreSQL
* Microsoft Access
* Microsoft SQL Server
* Google Fusion Tables
* FileMaker  
* Oracle    
* Sybase
* dBASE
* Clipper  
* FoxPro  
* foshub  

常见的非关系型数据库  

* Redis  
* BigTable  
* Cassandra  
* MongoDB  
* CouchDB  

### SQL 简介

> 结构化查询语言(Structured Query Language)简称 SQL(发音：/ˈes kjuː ˈel/ "S-Q-L")，是一种特殊目的的编程语言，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统；同时也是数据库脚本文件的扩展名。 结构化查询语言是高级的非过程化编程语言，允许用户在高层数据结构上工作。它不要求用户指定对数据的存放方法，也不需要用户了解具体的数据存放方式，所以具有完全不同底层结构的不同数据库系统, 可以使用相同的结构化查询语言作为数据输入与管理的接口。结构化查询语言语句可以嵌套，这使它具有极大的灵活性和强大的功能。

SQL语句不是本章节主要内容 想要了解 [请戳这里](https://www.runoob.com/sql/sql-tutorial.html)  

### JDBC 

> JDBC 的全称是 Java Database Connectivity，叫做 Java 数据库连接。它包括了一组与数据库交互的api，还有与数据库进行通信的驱动程序。  

我们要写设计到数据库的程序，是通过C语言或者C++语言直接访问数据库的接口  

对于不同的数据库，我们需要知道不同数据库对外提供的系统API，这就影响了我们程序扩展和跨平台的实现.  

那么有没有一种方法来对不同的数据库接口进行统一呢？ 当然有. 我们只需要和最上层接口进行交互，剩下的部分就交给其它层去处理,我们的任务就变得轻松简单许多 .  

JDBC 为数据库开发人员提供了一个标准的API，据此可以构建更高级的工具和接口使数据库开发人员能够用纯Java API 编写数据库应用程序  

#### JDBC 连接数据库 

设计到建立一个JDBC连接的编程主要有四个步骤:  

1. 导入JDBC驱动：只有拥有了驱动程序我们才可以注册驱动程序完成连接的其他步骤.  
2. 注册 JDBC 驱动程序：这一步会导致 JVM 加载所需的驱动类实现到内存中，然后才可以实现 JDBC 请求。
3. 数据库 URL 指定：创建具有正确格式的地址，指向到要连接的数据库。
4. 创建连接对象：最后，代码调用 DriverManager 对象的 getConnection() 方法来建立实际的数据库连接。

##### 导入JDBC 驱动程序

需要下载对应的数据库的JDBC驱动，将其导入到项目中,具体的导入方式根据个人的IDE确定，当前我们直接通过命令行导入 ***javac -cp*** 

##### 注册JDBC驱动程序

我们在使用驱动程序之前，必须注册你的驱动程序。注册驱动程序的本质就是将我们将要使用的数据库的驱动类文件动态的加载到内存中，然后才能进行数据库。比如我们使用的 Mysql 数据库。我们可以通过以下两种方式来注册我们的驱动程序。

1. 方法一 ---- Class.forName();  

动态加载一个类最常用的方法时使用Java的Class.forName()方法,通过使用这个方法来将数据库的驱动类动态加载到内存中,然后我们就可以使用.  

使用Class.forName()来注册Mysql驱动程序;  

```
try {
    Class.forName("com.mysql.jdbc.Driver");
} catch(ClassNotFoundException ex){
    System.out.println("Error: unable to load driver class!");
    System.exit(1);
}
```

2. 方法2 ---- DriverManager.registerDriver();  

```
Driver driver = new com.mysql.jdbc.Driver();
DriverManager.registerDriver(driver);
```

指定数据库连接 URL  

当加载了驱动程序,便可以使用 ***DriverManager.getConnection()*** 方法连接到数据库了  
这里给出 ***DriverManager.getConnection()*** 三个重载方法：  

```
getConnection(String url)

getConnection(String url, Properties prop)

getConnection(String url, String user, String password)

```

数据库的URL是指定数据库地址.下表列出了下来流行的JDBC驱动程序名和数据库的URL。  

| RDBMS | JDBC 驱动程序的名称 | URL | 
| ----- | -------------- | ---- |
| Mysql | com.mysql.jdbc.Driver | jdbc:mysql://hostname/databaseName |
| Oracle | oracle.jdbc.driver.OracleDriver | jdbc:oracle:thin:@hostname:port Number:databaseName | 
| DB2 | COM.ibm.db2.jdbc.net.DB2Driver | jdbc:db2:hostname:port Number/databaseName |
| Sybase | com.sybase.jdbc.SybDriver | jdbc:sybase:Tds:hostname: port Number/databaseName |


##### 创建连接对象

下面三种形式 DriverManager.getConnection() 方法来创建一个连接对象，以 Mysql 为例。getConnection()最常用形式要求传递一个数据库 URL，用户名 username 和密码 password。

1. 使用数据库 ***URL*** 的 ***用户名*** 和 ***密码***

```
String URL = "jdbc:mysql://localhost/EXAMPLE";
String USER = "username";
String PASS = "password";
Connection conn = DriverManager.gegtConnection(URL, USER, PASS);
```

2. 只使用一个数据库 ***URL*** 

然而在这种情况下，数据库的URL，包括用户名和密码。  

```
String URL = "jdbc:mysql://localhost/EXAMPLE?user=root&password=0909";// 密码为示例
// Mysql URL 的参数设置详细可以查阅相关资料
Connection conn = DriverManager.getConnection(URL);
```

3. 使用数据库的 ***URL*** 和 一个 ***Properties*** 对象

```
import java.util.*;

String URL = "jdbc:mysql://localhost/EXAMPLE";
Properties pro = new Properties();

// Properties 对象 保存一组关键词-值对
pro.put("user", "root");
pro.put("password", "");

Connection conn = DriverManager.getConnection(URL, pro);
```

4. 关闭JDBC连接

```
conn.close();
```

##### 创建数据库

> 在使用数据库之前第一件事情就是创建数据库，这里我们将使用JDBC来创建数据库

示例代码：

```
import java.sql.*;

public class CreateDatabase {
    public static void main(String[] args){
        Connection connection = null;
        try {
            //加载数据库驱动
            Class.forName("com.mysql.jdbc.Driver");
            //打开数据库连接 第一个参数为数据库地址  后面2个参数分别为数据库用户名和密码
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/","root","");
            //创建Statement
            Statement statement = connection.createStatement();
            //执行sql
            statement.execute("create database EXAMPLE");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }finally {
            try {
                //关闭连接
                if (connection != null) {
                    connection.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

启动mysql 服务

单文件编译运行需要使用mysql的 jar 包  

运行示例：  

```
$ wget http://labfile.oss.aliyuncs.com/courses/1230/mysql-connector-java-5.1.47.jar
$ javac -cp mysql-connector-java-5.1.47.jar CreateDatabase.java
$ java -cp .:mysql-connector-java-5.1.47.jar CreateDatabase
```

##### 数据库操作

当连接上了数据库后，就需要通过SQL语句对数据库进行操作. 随着java语言应用面的逐步扩宽，Sun公司开发了一个标准的SQL数据库访问接口----JDBC API. 它可以使Java编程人员通过一个一致的接口，访问多种关系数据库.而今天我们简单示例一遍，如何利用JDBC的一些核心API与数据库进行交互  

通过使用JDBC Statement, CallableStatement 和 PreparedStatement 接口定义的方法和属性,使可以使用SQL或者PL/SQL 命令和从数据库接收数据. 他们还定义了许多方法,帮助消除Java和数据库之间数据类型的差异  

| 接口 | 应用场景 |  
| ---- | ----- |  
| Statement | 当在运行时使用静态SQL语句时（Statement接口不能接收参数) |  
| CallableStatement | 当要访问数据库中的存储过程时（CallableStatement对象的接口还可以接收运行时输入参数） |  
| PreparedStatement | 当计划多次使用SQL语句时（PreparedStatement接口接收在运行时输入参数） |  


##### Statement  

我们要使用 ***Statement*** 接口，第一步肯定是创建一个 ***Statement*** 对象了。我们需要使用 ***Connection*** 对象的 ***createStatement()*** 方法进行创建.  

```
Statement stmt = null;
try {
    stmt = conn.createStatement();
    ……
} catch(SQLException e){
    ……
} finally{
    ……
}
```

一旦创建了一个 ***Statement*** 对象，我们就可以用它来执行SQL 语句了， 首先我们先来看看Statement 里面有哪些方法吧!  

| 方法 | 说明 |  
| ---- | ---- |  
| boolean execute(String SQL) | 如果 ResultSet 对象可以被检索返回布尔值true，否则返回false。使用这个方法来执行SQL DDL 语句，或当需要使用真正的动态SQL |  
| int executeUpdate(String SQL) | 用于执行 INSERT、UPDATE或DELETE语句以及SQLDDL（数据定义语言）语句。返回值是一个整数, 指示受影响的行数（即更新计数） |  
| ResultSet executeQuery(String SQL) | 返回ResultSet对象。用于产生单个结果集的语句,例如SELECT 语句 |  

正如关闭一个Connection 对象来释放数据库连接资源，出于同样的原因，也应该关闭Statement对象。

```
Statement stmt = null;
try{
    stmt = conn.createStatement();
    ……
} catch (SQLException e){
    ……
} finally {
    stmt.close();
}
```

> 如果关闭了 ***Connection*** 对象首先它会关闭 ***Statement*** 对象，然而应该始终明确关闭Statement对象，以确保正确的清除。  

##### PreparedStatement

PreparedStatement 接口扩展了 Statement 接口，有利于高效地执行多次使用的 SQL 语句。我们先来创建一个 PreparedStatement 对象。 Statement 为一条 SQL 语句生成执行计划。如果要执行两条 SQL 语句，会生成两个执行计划。一万个查询就生成一万个执行计划！ 　


PreparedStatement 用于使用绑定变量重用执行计划  

通过 set 不同数据，只需要生成一次执行计划，并且可以重用  

```
PreparedStatement pstmt = null;
try {
    /**
     * 在JDBC中所有的参数都被代表？符号，这是已知的参数标记。在执行SQL语句之前 必须提供值的每一个参数
     */
    String SQL = "Update Students SET age = ? WHERE id = ?";
    pstmt = conn.prepareStatement(SQL);
    . . .
} catch (SQLException e){
    pstmt.close();
}
```

##### CallableStatement

CallableStatement 对象为所有的 DBMS 提供了一种以标准形式调用存储过程的方法。存储过程储存在数据库中。对储存过程的调用是 CallableStatement 对象所含的内容。三种类型的参数有：IN，OUT 和 INOUT。PreparedStatement 对象只使用IN参数。 CallableStatement 对象可以使用所有三个  

| 参数 | 描述 |  
| ----- | ------ |  
| IN | 它的值实在创建SQL语句时未知的参数，将IN参数传给CallableStatement对象是通过 setXXX() 方法完成的 |  
| OUT | 其值由它返回的SQL语句提供的参数。从OUT参数的getXXX() 方法检索值 |  
| INOUT | 同时提供输入和输出值的参数，绑定的setXXX() 方法的变量，并使用getXXX() 方法检索值 |  

在JDBC中调用存储过程的语法如下所示. 注意，方括号标识其间的内容是可选项; 方括号本身并不是语法的组成部分.  

```
{call 存储过程名[(?,?,……)]}
```

返回结果参数的过程的语法为：  

```
{? = call 存储过程名[(?,?,……)]}
```

不带参数的存储过程的语法类似：  

```
{call 存储过程名}
```

CallableStatement 对象是用Connection方法prepareCall 创建的。  

```
CallableStatement cstmt = null;
try {
   String SQL = "{call getEXAMPLEName (?, ?)}";
   cstmt = conn.prepareCall (SQL);
   . . .
}
catch (SQLException e) {
   . . .
}
finally {
   cstmt.close();
}
```

> 关于CallableStatement接口更多的可以查询java JDBC api

##### JDBC 结果集

> 结果集通常是通过执行查询数据库的语句生成，表示数据库查询结果的数据表  

##### ResultSet 介绍

ResultSet 对象具有指向其当前数据行的光标。最初，光标被置于第一行之前。next 方法将光标移动到下一行；因为该方法在 ResultSet 对象没有下一行时返回 false，所以可以在 while 循环中使用它来迭代结果集。光标可以方便我们对结果集进行遍历。默认的 ResultSet 对象不可更新，仅有一个向前移动的光标。因此，只能迭代它一次，并且只能按从第一行到最后一行的顺序进行。    

ResultSet 接口的方法可分为三类:  

* 导航方法: 用于移动光标  
* 获取方法：用于查看当前行的光标所指向的列中的数据  
* 更新方法：用于更新当前行的列中的数据  

JDBC 提供下列连接方法来创建所需的 ResultSet 语句：

```
createStatement(int RSType, int RSConcurrency);

prepareStatement(String SQL, int RSType, int RSConcurrency);

prepareCall(String sql, int RSType, int RSConcurrency);
```

Rstype 表示 ResultSet 对象的类型，RsConcurrency 是 ResultSet 常量，用于指定一个结果集是否为只读或可更新.  

ResultSet 的类型，如果不指定ResultSet类型 将自动获取一个是 TYPE_FORWARD_ONLY；  

| 类型 | 描述 |  
| ---- | ---- |  
| ResultSet.TYPE_FORWARD_ONLY | 游标只能向前移动的结果集 |  
| ResultSet.TYPE_SCROLL_INSENSITIVE | 游标可以向前和向后滚动，但不及时更新，就是如果数据库里的数据修改过，并不在ResultSet中反应出来 |  
| ResultSet.TYPE_SCROLL_SENSITIVE | 游标可以向前和向后滚动，并及时跟踪数据库的更新，以便更改ResultSet中的数据 |  

并发性的ResultSet，如果不指定任何并发类型，将自动获得一个为CONCUR_READ_ONLY  

| 并发 | 描述 |  
| ---- | ---- | 
| ResultSet.CONCUR_READ_ONLY | 创建结果集只读。这是默认的 |  
| ResultSet.CONCUR_UPDATABLE | 创建一个可更新的结果集 |  


示例创建了一个双向、可更新的ResultSet对象；  

```
try {
    Statement stmt = conn.createStatement(
        ResultSet.TYPE_SCROLL_INSENSITIVE,
        ResultSet.CONCUR_UPDATABLE
    );
} catch(Exception ex){
    ……
} finally {
    ……
}
```

###### 导航

我们在上面已经知道了，导航方法时用于移动光标.我们先来看一看，在ResultSet接口中有哪些方法会涉及光标的移动.  

| 方法 | 说明 |  
| ---- | ---- |  
| public void beforeFirst() throws SQLException | 将光标移动到正好位于第一行之前 |  
| public void afterLast() throws SQLException | 将光标移动到刚刚结束的最后一行 |  
| public boolean first() throws SQLException | 将光标移动到第一行 |  
| public void last() throws SQLException | 将光标移动到最后一行 |  
| public boolean absolute(int row) throws SQLException | 将光标移动到指定的行 |  
| public boolean relative(int row) throws SQLException | 从它目前所指向向前或向后移动光标行的给定数量 |  
| public boolean previous() throws SQLException | 将光标移动到上一行。上一行关闭的结果集此方法返回false |  
| public boolean next() throws SQLException | 将光标移动到下一行。如果没有更多的行结果集中的此方法返回false |  
| public int getRow() throws SQLException | 返回的行号，改光标指向的行 |  
| public void moveToInsertRow() throws SQLException | 将光标移动到一个特殊的行，可以用来插入新行插入到数据库中的结果集。当前光标位置被记住 |  
| public void moveToCurrentRow() throws SQLException | 移动光标返回到当前行，如果光标在当前插入行，否则，这个方法不执行任何操作 |  

###### 获取

ResultSet 接口中 我们经常使用get方法来查看结果集  

| 方法 | 说明 |  
| ---- | ---- |  
| public int getInt(String columnName) throws SQLException | 当前行中名为ColumnName列的值 |  
| public int getInt(int columnIndex) throws SQLException | 当前行中指定列的索引的值。 列索引从1开始，意味着一个行的第一列是1，行的第二列是2，依次类推 |  

当然还有 getString() 等等

###### 更新 

更新的方法如下：  

| 方法 | 说明 |  
| ---- | ---- |  
| public void updateString(int columnIndex, String s) throws SQLException | 指定列中的字符串更改为s的值 |  
| public void updateString(String columnName, String s) throws SQLException | 类似于前面的方法，不同之处在于由它的名称。而不是它的索引指定的列 |  

类似的还有updateDouble() 等等  

我们在更新了结果集中的内容，当然需要更新一下数据库了。 我们可以调用下面的方法更新数据库  
| 方法 | 说明 |  
| ---- | ---- |  
| public void updateRow() | 通过更新数据库中响应的行更新当前行 |  
| public void deleteRow() | 从数据库中删除当前行 |  
| public void refreshRow() | 刷新在结果集的数据，以反映最新变化在数据库中 |  
| public void cancelRowUpdates() | 取消所做的当前行的任何更新 |  
| public void insertRow() | 插入一行到数据库中。当光标指向插入行此方法只能被调用 |  

我们这里对上面的方法做一个小小的举例  

```
Statement stmt = conn.createStatement(
                           ResultSet.TYPE_SCROLL_INSENSITIVE,
                           ResultSet.CONCUR_UPDATABLE
                           );

String sql = "SELECT id, name, age FROM Students";
ResultSet rs = stmt.executeQuery(sql);

//结果集中插入新行
rs.moveToInsertRow();
rs.updateInt("id",5);
rs.updateString("name","John");
rs.updateInt("age",21);
//更新数据库
rs.insertRow();
```

### JDBC 事务

我们在编写 java 程序的时候，在默认情况下，JDBC 连接是在自动提交模式下，即每个 SQL 语句都是在其完成时提交到数据库。但有时候我们为了提高程序运行的性能或者保持业务流程的完整性，以及使用了分布式事务管理方式，这个时候我们可能想关闭自动提交而自己管理和控制自己的事务。  
让多条 SQL 在一个事务中执行，并且保证这些语句是在同一时间共同执行的时候，我们就应该为这多条语句定义一个事务。一个事务是把单个 SQL 语句或一组 SQL 语句作为一个逻辑单元，并且如果事务中任何语句失败，则整个事务失败。  

如果我们要启动一个事务，而不是让 JDBC 驱动程序默认使用 auto-commit 模式支持。这个时候我们就要使用 Connection 对象的 setAutoCommit() 方法。我们传递一个布尔值 false 到 setAutoCommit() 中，就可以关闭自动提交。反之我们传入一个 true 便将其重新打开。  

例如:  

```
Connection conn = null;
conn = DriverManager.getConnection(URL);
// 关闭自动提交
conn.setAutoCommit(false);
```

我们关闭了自动提交后，如果我们要提交数据库更改怎么办呢？这时候就要用到我们的提交和回滚了。我们要提交更改，可以调用commit() 方法  

```
conn.commit();
```

尤其不要忘记，在catch块内添加回滚事务，表示操作出现异常，撤销事务;  

```
conn.rollback();
```
