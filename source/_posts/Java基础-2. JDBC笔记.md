---
title: JDBC相关知识
date: 2021-06-30 23:48:29
tags: 
- JavaSE JDBC
category:
- Java基础
type: artitalk
cover: https://cdn.pixabay.com/photo/2020/12/15/06/28/pier-5832800_960_720.jpg
---

# JDBC

## JDBC概念

`Java DataBase Connectivity` ： Java 数据库连接， Java语言操作数据库。

## JDBC本质

其实是官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类。

![](https://gitee.com/hek97/picture/raw/master/img/60dc89fe5132923bf8dea0cb.jpg)

# 快速入门

这里只是简单快速入门，为避免释放资源的空指针异常，实际中需要使用异常处理。详见[进阶练习](#进阶练习)

## 数据库表信息

![](https://gitee.com/hek97/picture/raw/master/img/60ded5f45132923bf8d0f509-20211221092613707.jpg)

## 数据库连接jar包

首先先创建lib目录，然后把相关连接jar包加入进去，再把jar包添加到当前模块的库中。

![image-20211220230158908](https://gitee.com/hek97/picture/raw/master/img/image-20211220230158908.png)

## 详细代码

```java
package io.gitee.hek97.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.Statement;

public class JdbcDemo1 {
    public static void main(String[] args) throws Exception {
        //1. 导入驱动jar包
        //2. 注册驱动，mysql5之后的驱动jar包可以省略该步骤
        Class.forName("com.mysql.jdbc.Driver");
        //3. 获取数据库连接对象
        Connection coon = DriverManager.getConnection("jdbc:mysql:///db3", "root", "root");
        //4.1 定义第一条SQL语句
        String sql1 = "update account set balance = 4500 where id = 1";
        //4.2 定义第二条防注入SQL语句
        String sql2 = "update account set balance = ? where id = ?";
        //5.1 获取执行SQL对象 statement
        Statement stmt = coon.createStatement();
        //5.2 获取执行防注入SQL对象 prepareStatement
        PreparedStatement ps = coon.prepareStatement(sql2);//注意：这里放SQL语句
        ps.setObject(1,3500);
        ps.setObject(2,2);
        //6. 执行 sql，接受返回结果
        int count1 = stmt.executeUpdate(sql1);
        int count2 = ps.executeUpdate();//注意：这里直接执行，不用放SQL语句
        //7. 处理结果
        System.out.println("SQL1："+count1);
        System.out.println("SQL2："+count2);
        //8. 释放资源
        stmt.close();
        ps.close();
        coon.close();
    }
}
```

# 详解各个对象的功能

## DriverManager：驱动管理对象

### 主要作用

告诉程序该使用哪一个数据库驱动jar包和获取数据库的连接对象。

### 使用解析

#### 注册驱动

写代码使用`Class.forName("com.mysql.jdbc.Driver");`，将`com.mysql.jdbc.Driver`这个类加载进内存，
通过查看mysql驱动的源码找到`com.mysql.jdbc.Driver`类，发现其中的静态代码块的DriverManager类使用registerDriver方法注册了Driver驱动。

```java
 static {
        try {
            //static void registerDriver(Driver driver) :注册与给定的驱动程序 DriverManager 。 
            java.sql.DriverManager.registerDriver(new Driver());//DriverManager类使用registerDriver方法注册驱动
        } catch (SQLException E) {
            throw new RuntimeException("Can't register driver!");
}
	}
```
> mysql5之后的驱动jar包可以省略注册驱动的步骤，也就是快速入门的第1步可以省略。是因为mysql5之后的驱动jar包将该步骤给写到文件里了，使用时，会自动读取该文件去注册驱动。
>
> ![](https://gitee.com/hek97/picture/raw/master/img/60ddd7525132923bf83d1c29.jpg)

#### 获取数据库连接

`static Connection getConnection(String url, String user, String password) `

1. url：指定连接的路径。
	* 格式：`jdbc:mysql://ip地址(域名):端口号/数据库名称`；
	* 示例：`jdbc:mysql://localhost:3306/db3`；
	
	  > 如果连接的是本机mysql服务器，并且mysql服务默认端口是3306，则url可以简写为：`jdbc:mysql:///数据库名称`，示例：`jdbc:mysql:///db3`。
2. user：数据库的用户名，示例：`root`；
3. password：数据库的密码 ，示例：`root`。

## Connection：数据库连接对象

### 主要作用

获取执行SQL的对象和管理事务。

### 使用解析

#### 获取执行SQL的 对象

1. `Statement createStatement()`

   使用的SQL语句直接把参数写在字符串中或通过参数拼接字符串。示例：`String sql1 = "update account set balance = 3500 where id = 1";`。

2. `PreparedStatement prepareStatement(String sql)  `

   使用的SQL语句通过占位符`?`代替需要传入的参数，然后再通过`setObject`方法设置每个`?`的值。示例：`String sql2 = "update account set balance = ? where id = ?";`

  > 为了完全避免SQL注入，使用Java对数据库进行操作时，必须使用PreparedStatement，严禁任何通过参数拼字符串的代码！
  >
  > 具体使用查看快速入门示例。

#### 管理事务

* 开启事务：`setAutoCommit(boolean autoCommit) `：调用该方法设置参数为false，即开启事务；
* 提交事务：`commit() `；
* 回滚事务：`rollback() `。

## Statement和PreparedStatement ：执行sql的对象

### 主要作用

执行SQL语句的对象。

### 解析

1. SQL注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接。会造成安全性问题。
   - 输入用户随便，输入密码：`a' or 'a' = 'a`；
   - 最后的sql会变成`select * from user where username = 'fhdsjkf' and password = 'a' or 'a' = 'a' `，失去了密码的意义。

2. 解决sql注入问题：使用PreparedStatement对象来解决；
3. 预编译的SQL：参数使用?作为占位符。

### 执行sql

1. boolean execute(String sql) ：可以执行任意的sql ，仅需了解； 

2. int executeUpdate(String sql)：执行DML(insert、delete、update)语句、DDL(create，alter、drop)语句。DDL直接使用的比较少，一般都是执行DML语句。

  > 返回值int为影响的行数，可以通过这个影响的行数判断DML语句是否执行成功 返回值>0的则执行成功，反之，则失败。

3. ResultSet executeQuery(String sql)  ：执行DQL(select)语句。

### 进阶练习

1. 在account表中，添加一条记录；

2. 在account表中，修改一条记录；

3. 在account表中，删除一条记录。

   > 修改和删除的操作大同小异，在此只具体讲解添加记录。

#### 解析

在之前的快速入门中，我们的代码很粗糙，比如第8步释放资源的代码，如果之前的代码存在错误的话，会导致运行出错，然后资源就不能被释放，造成内存泄露。

> 内存泄露就是一部分资源未被释放，然后一直驻留在内存中，最终导致系统内存变小的情况。

#### 添加操作

使用 insert 语句在account 表中添加一条记录。

```java
package io.gitee.hek97.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

/**
 * account 表，添加一条记录，使用 insert 语句。
 */
public class JdbcDemo2 {
    public static void main(String[] args){
        Connection coon=null;
        PreparedStatement ps=null;
        //1. 注册驱动
        try {
            Class.forName("com.mysql.jdbc.Driver");
            //2. 获取链接
            coon = DriverManager.getConnection("jdbc:mysql:///db3", "root", "root");
            //3. 定义SQL语句
            String sql1="insert into account values (null,?,?)";
            //4. 获取PreparedStatement对象
            ps = coon.prepareStatement(sql1);
            ps.setObject(1,"麻子");
            ps.setObject(2,6000);
            //5. 执行
            int count = ps.executeUpdate();
            //6. 处理结果
            if(count>0){
                System.out.println("添加成功");
            }
            else{
                System.out.println("添加失败");
            }
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }finally {
            //7. 释放资源
            //避免空指针异常
            if(ps!=null){
                try {
                    ps.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if(coon!=null){
                try {
                    coon.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

   

## ResultSet：结果集对象

### 主要作用

封装查询的结果，用于执行DQL(select)语句。

### 使用步骤

1. 执行DQL(select)语句；
2. 游标向下移动一行；
3. 判断当前行是否有数据；
4. 获取当前行中某一列的数据。

### 使用解析

![](https://gitee.com/hek97/picture/raw/master/img/60df33f05132923bf80ff997.jpg)

#### boolean next()

游标向下移动一行，判断当前行是否是最后一行末尾(是否有数据)，如果是，则返回false，如果不是则返回true

#### getXxx(参数)

获取数据

* Xxx：
  * 这里的Xxx代表的是数据类型，如： 获取int类型的方法为getInt() ，获取String类型的方法为getString()。
* 参数：
	1. int：代表数据库中的列编号，从1开始   如： getString(1)为第1列的值id。
	2. String：代表数据库中的列名称。 如： getDouble("balance")为第3列的值balance。


### 核心代码

```java
//循环判断游标是否是最后一行末尾。
//rs 为 ResultSet 结果集
while(rs.next()){
       //获取数据
      int id = rs.getInt(1);
      String name = rs.getString("name");
      double balance = rs.getDouble(3);
    System.out.println(id + "---" + name + "---" + balance);
}
```

### 练习

#### 需求

定义一个方法，查询account表的数据将其封装为对象，然后装载集合，返回。

#### 步骤

1. 定义[Accout](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/io/gitee/hek97/domian/Account.java)类，将查询的同一类型（account表）的数据封装起来，方便调用；
2. 在[JdbcDemo4](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/io/gitee/hek97/jdbc/JdbcDemo4.java)定义方法` public List<Account> findAll(){}`，将查询的数据封装到Account类中，然后装载到list集合中。
3. 在[JdbcDemo5](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/io/gitee/hek97/jdbc/JdbcDemo5.java)类中调用findAll()方法，得到输出结果。

#### 代码

具体代码查看封装SQL数据的[Accout](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/io/gitee/hek97/domian/Account.java)类和[JdbcDemo4](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/io/gitee/hek97/jdbc/JdbcDemo4.java)类中查询SQL数据的findAll()方法。

### 使用步骤

具体使用参考[快速入门](#快速入门)和[进阶练习](#进阶练习)。

# 抽取JDBC工具类
## 目的

回顾之前的代码，会发现有很多重复代码，JDBC工具类可以简化书写，方便复用代码。

## 分析需要抽取简化的代码

1. 注册驱动抽取，对应[进阶练习](#进阶练习)的第1步

2. 抽取一个方法获取连接对象，对应进阶练习的第2步；
	* 需求：不想传递参数（麻烦），还得保证工具类的通用性。
	* 解决：配置文件，后面要改链接、用户和密码时，只用修改配置文件就好了。
	
3. 抽取一个方法释放资源，对应进阶练习的第7步。

   > 工具类一般使用静态方法，以便后续调用。

   ![目录结构](https://gitee.com/hek97/picture/raw/master/img/60e2b7235132923bf805c459.jpg)

## 代码实现

此处代码过多，可点击连接到GitHub查看。

1. 数据库表

   ![](https://gitee.com/hek97/picture/raw/master/img/60ded5f45132923bf8d0f509.jpg)

2. 配置文件：[jdbc.properties](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/jdbc.properties)

   ```properties
   url=jdbc:mysql:///db3
   user=root
   password=root
   driver=com.mysql.jdbc.Driver
   ```

1. 工具类：[JDBCUtils](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/io/gitee/hek97/jdbcUtils/JDBCUtils.java)
2. 具体使用的例子：[JdbcDemo6](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/io/gitee/hek97/jdbc/JdbcDemo6.java)

## 练习

### 需求

1. 通过键盘录入用户名和密码
2. 判断用户是否登录成功
	* `select * from user where username = "" and password = ""`;
	* 如果这个sql有查询结果，则成功，反之，则失败。

### 步骤

1. 创建数据库表 user；
	
	```mysql
	CREATE TABLE USER(
		id INT PRIMARY KEY AUTO_INCREMENT,
		username VARCHAR(32),
		PASSWORD VARCHAR(32)
	
	);
	INSERT INTO USER VALUES(NULL,'zhangsan','123');
	INSERT INTO USER VALUES(NULL,'lisi','234');# 插入数据
	```

2. 代码实现。

   - 工具类：[JDBCUtils](https://github.com/hekun97/JavaCode/blob/master/Test/JDBC/src/io/gitee/hek97/jdbcUtils/JDBCUtils.java)

   - 具体使用：JdbcDemo7

     ```java
     package io.gitee.hek97.jdbc;
     
     import io.gitee.hek97.jdbcUtils.JDBCUtils;
     
     import java.sql.Connection;
     import java.sql.PreparedStatement;
     import java.sql.ResultSet;
     import java.sql.SQLException;
     import java.util.Scanner;
     
     public class JdbcDemo7 {
         public static void main(String[] args) {
             //1. 键盘录入
             Scanner sc = new Scanner(System.in);
             System.out.println("请输入用户名：");
             String username = sc.nextLine();
             System.out.println("请输入密码：");
             String password = sc.nextLine();
             //2. 调用方法
             boolean user = new JdbcDemo7().login(username, password);
             //3. 处理结果
             if (user) {
                 System.out.println("登录成功");
             } else {
                 System.out.println("登录失败");
             }
         }
     
         public boolean login(String username, String password) {
             if (username == null || password == null) {
                 return false;
             }
             Connection conn = null;
             PreparedStatement ps = null;
             ResultSet rs = null;
             try {
                 //1. 注册驱动、获取连接
                 conn = JDBCUtils.getConnection();
                 //2. 定义sql语句
                 String sql = "select * from user where username = ? and password = ?";
                 //3. 获取执行SQL对象并赋值
                 ps = conn.prepareStatement(sql);
                 ps.setObject(1, username);
                 ps.setObject(2, password);
                 //4. 查询数据
                 rs = ps.executeQuery();
                 return rs.next();
     
             } catch (SQLException e) {
                 e.printStackTrace();
             } finally {
                 //5. 释放资源
                 JDBCUtils.close(rs, ps, conn);
             }
             return false;
         }
     }
     ```

     


​			


# JDBC控制事务
1. 事务：一个包含多个步骤的业务操作。如果这个业务操作被事务管理，则这多个步骤要么同时成功，要么同时失败。

2. 操作：
	1. 开启事务
	2. 提交事务
	3. 回滚事务
	
3. 使用Connection对象来管理事务
  * 开启事务：`setAutoCommit(boolean autoCommit)` ：调用该方法设置参数为false，即开启事务
  	* 在执行sql之前开启事务
  * 提交事务：`commit()` 
  	* 当所有sql都执行完提交事务
  * 回滚事务：`rollback()`
  	* 在catch中回滚事务

4. 代码：
```java
	package cn.itcast.jdbc;
	
	import cn.itcast.util.JDBCUtils;
	
	import java.sql.Connection;
	import java.sql.PreparedStatement;
	import java.sql.SQLException;
	
	/**
	 * 事务操作
	 */
	public class JDBCDemo10 {
	    public static void main(String[] args) {
	        Connection conn = null;
	        PreparedStatement pstmt1 = null;
	        PreparedStatement pstmt2 = null;
	
	        try {
	            //1.获取连接
	            conn = JDBCUtils.getConnection();
	            //开启事务
	            conn.setAutoCommit(false);
	
	            //2.定义sql
	            //2.1 张三 - 500
	            String sql1 = "update account set balance = balance - ? where id = ?";
	            //2.2 李四 + 500
	            String sql2 = "update account set balance = balance + ? where id = ?";
	            //3.获取执行sql对象
	            pstmt1 = conn.prepareStatement(sql1);
	            pstmt2 = conn.prepareStatement(sql2);
	            //4. 设置参数
	            pstmt1.setDouble(1,500);
	            pstmt1.setInt(2,1);
	
	            pstmt2.setDouble(1,500);
	            pstmt2.setInt(2,2);
	            //5.执行sql
	            pstmt1.executeUpdate();
	            // 手动制造异常
	            int i = 3/0;
	
	            pstmt2.executeUpdate();
	            //提交事务
	            conn.commit();
	        } catch (Exception e) {
	            //事务回滚
	            try {
	                if(conn != null) {
	                    conn.rollback();
	                }
	            } catch (SQLException e1) {
	                e1.printStackTrace();
	            }
	            e.printStackTrace();
	        }finally {
	            JDBCUtils.close(pstmt1,conn);
	            JDBCUtils.close(pstmt2,null);
	        }
	    }
	}
```