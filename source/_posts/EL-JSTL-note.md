---
title: JSP进阶-MVC-EL表达式-JSTL
date: 2021-07-21 10:14:52
tags: 
- Java JSP MVC EL JSTL
category:
- JavaWEB
type: artitalk
cover: https://cdn.pixabay.com/photo/2021/07/02/09/39/cockpit-6381367_1280.jpg
---

# JSP进阶

<a href="{% post_path 'JavaWEB-7-Cookie-JSP-Session' %}#JSP入门">JSP入门</a>学习可在之前文章回顾。

## JSP的指令

### 作用

用于配置JSP页面，导入资源文件。

### 格式

`<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 ... %>`，实例：`<%@ page contentType="text/html;charset=UTF-8" language="java" %>`。

### 指令名称分类

共三类，包括page、include和taglib。

#### page

 配置JSP页面的，以下为page指令常用的属性名：

1. contentType：等同于`response.setContentType()`
	* 设置响应体的mime类型以及字符集；
	* 设置当前jsp页面的编码（只能是高级的IDE才能生效，如果使用低级工具，则需要设置pageEncoding属性设置当前页面的字符集）；
	* 例子：`<%@ page contentType="text/html;charset=UTF-8" language="java" %>`。
2. import：导入java代码需使用的包
   - 例子：`<%@ page import="java.util.List" %>`。

3. errorPage：当前页面发生异常后，会自动跳转到指定的错误页面。
4. isErrorPage：标识当前页面是否是错误页面。
	* true：是，可以使用内置对象exception；
	* false：否。默认值。不可以使用内置对象exception。

#### include

导入页面的资源文件，例子：`<%@include file="top.jsp"%>`。

#### taglib

导入资源（标签库），例子：`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`。

> 这里的prefix表示之后使用该标签库的前缀，是自定义的。

## JSP的注释

1. html注释：
	`<!-- -->`：只能注释html代码片段。
2. jsp注释：
	`<%-- --%>`：可以注释所有，包括java和html等，一般推荐使用这种方式。

## JSP的内置对象

在jsp页面中不需要创建，直接使用的对象。

### 9个内置对象

JSP的内置对象共9个，如下表：

| 变量名      | 真实类型            | 作用                                                     |
| ----------- | ------------------- | -------------------------------------------------------- |
| pageContext | PageContext         | 当前页面共享数据，还可以获取其他八个内置对象             |
| request     | HttpServletRequest  | 一次请求访问的多个资源(转发)                             |
| session     | HttpSession         | 一次会话的多个请求间                                     |
| application | ServletContext      | 所有用户间共享数据                                       |
| response    | HttpServletResponse | 响应对象                                                 |
| page        | Object              | 当前页面(Servlet)的对象  this                            |
| out         | JspWriter           | 输出对象，数据输出到页面上                               |
| config      | ServletConfig       | Servlet的配置对象                                        |
| exception   | Throwable           | 异常对象，isErrorPage为true时，可以使用内置对象exception |


  	 

# MVC

是一种常用的开发模式。

## jsp演变历史

1. 早期只有servlet，只能使用response输出标签数据，非常麻烦；
2. 后来使用jsp，简化了Servlet的开发，但是过度使用jsp，在jsp中即写大量的java代码，又写html代码，会造成代码难于维护，不利于分工协作；
3. 再后来，java的web开发，借鉴mvc开发模式，使得程序的设计更加合理性	。

## MVC的概念

1. `M`：`Model`，模型。`JavaBean`

  用于完成具体的业务操作，如：查询数据库，封装对象。
2. `V`：`View`，视图。`JSP`

  用于展示数据给用户。
3. `C`：`Controller`，控制器。`Servlet`
	* 获取用户的输入；
	* 调用模型；
	* 将数据交给视图进行展示。

![](https://pic.imgdb.cn/item/60f9140d5132923bf8747659.jpg)

## MVC的优缺点

1. 优点：
	1. 耦合性低，方便维护，可以利于分工协作；
	2. 重用性高。
2. 缺点：
	1. 使得项目架构变得复杂，对开发人员要求高。

# EL表达式

## 概念

`Expression Language `（表达式语言）。

## 作用

替换和简化`jsp`页面中`java`代码的编写

## 语法格式

格式为：`${表达式}`。

> jsp默认支持el表达式的，如果要忽略el表达式：
> 1. 设置`jsp`中`page`指令中：`isELIgnored="true" `忽略当前jsp页面中所有的el表达式；
> 2. `\\${表达式}` ：忽略当前这个el表达式。

## 使用

### 用于运算

包括算数运算符、比较运算符、逻辑运算符、空运算符。具体如下：

1. 算数运算符：` + - * /(div) %(mod)`
2. 比较运算符： `> < >= <= == !=`
3. 逻辑运算符：` &&(and) ||(or) !(not)`
4. 空运算符： `empty`

  用于判断字符串、集合、数组对象是否为`null`或者长度是否为0。解析如下：

  * `${empty list}`：表示判断字符串、集合、数组对象是否为`null`或者长度为0；
  * `${not empty str}`：表示判断字符串、集合、数组对象是否不为`null `并且 长度>0。

### 获取域对象中的值

el表达式只能从域对象中获取值。具体如下：

1. ${域名称.键名}：从指定域中获取指定键的值
	* 域名称：
		1. pageScope		    --> pageContext
		2. requestScope 	   --> request
		3. sessionScope 	   --> session
		4. applicationScope   --> application（ServletContext）
	* 示例：在request域中存储了`name=张三`；获取：`${requestScope.name}`。
	
2. `${键名}`：表示依次从最小的域中查找是否有该键对应的值，直到找到为止。

   例：`${name}`，先到pageContext中找是否有name，再到request中去找，直到找到为止。

   > 上述的域名称顺序也是默认查找的顺序。

### 获取对象、List集合、Map集合的值

可获取对象、List集合、Map集合的值，具体如下：

 1. 对象：`${域名称.键名.属性名}`

    > 本质上会去调用对象的getter方法

 2. List集合：`${域名称.键名[索引]}`

 3. Map集合：
    * `${域名称.键名.key名称}`
    * `${域名称.键名["key名称"]`

代码演示如下：

1. User类型的JavaBean：

   ```java
   package cn.itcast.domain;
   
   import java.text.SimpleDateFormat;
   import java.util.Date;
   
   public class User {
       private String name;
       private int age;
       private Date birthday;
   
       public User(String name, int age, Date birthday) {
           this.name = name;
           this.age = age;
           this.birthday = birthday;
       }
   
       public User() {
       }
   
       public String getBirStr(){
           if(birthday != null){
               //1.格式化日期对象
               SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
               //2.返回字符串即可
               return sdf.format(birthday);
           }else{
               return "";
           }
       }
   
   
       public String getName() {
           return name;
       }
   
       public void setName(String name) {
           this.name = name;
       }
   
       public int getAge() {
           return age;
       }
   
       public void setAge(int age) {
           this.age = age;
       }
   
       public Date getBirthday() {
           return birthday;
       }
   
       public void setBirthday(Date birthday) {
           this.birthday = birthday;
       }
   }
   ```

2. JSP中EL表达式的使用：

   ```jsp
   <%@ page import="cn.itcast.domain.User" %>
   <%@ page import="java.util.*" %>
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>el获取数据</title>
   </head>
   <body>
       <%	//1.设置对象
           User user = new User();//获取JavaBean的User对象
       	//设置user对象的值
           user.setName("张三");
           user.setAge(23);
           user.setBirthday(new Date());
           request.setAttribute("u",user);//共享数据
   		//2.设置List集合
           List list = new ArrayList();
           list.add("aaa");
           list.add("bbb");
           list.add(user);
           request.setAttribute("list",list);
   		//3.设置Map集合
           Map map = new HashMap();
           map.put("sname","李四");
           map.put("gender","男");
           map.put("user",user);
           request.setAttribute("map",map);
       %>
   
       	<h3>1.el获取对象中的值</h3>
   	<!--
           通过的是对象中属性来获取。
           setter或getter方法，去掉set或get，在将剩余部分，首字母变为小写。
           过程：setName -- Name -- name
   	-->
           ${requestScope.u.name}<br>
       	<!-- 通过默认顺序查找 -->
           ${u.age}<br>
           ${u.birthday}<br>
           ${u.birthday.month}<br><!-- u.birthday是Date类型，可以调用GetMonth方法返回月份 -->
       	${u.birStr}<br>
   
           <h3>2.el获取List集合中的值</h3>
           ${list}<br>
           ${list[0]}<br>
           ${list[1]}<br>
           ${list[10]}<br>
       	${list[2].name}<!-- list[2]存储的User类型的user，可以调用GetName方法返回姓名-->
   
           <h3>3.el获取Map集合中的值</h3>
           ${map.gender}<br>
           ${map["gender"]}<br>
           ${map.user.name}<!--map.user存储的Uesr类型的user，可以调用GetName方法返回姓名-->
   </body>
   </html>
   ```

## 隐式对象

### pageContext

用于获取jsp其他八个内置对象。常用的有`${pageContext.request.contextPath}`：动态获取虚拟目录。

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>el隐式对象</title>
</head>
<body>

    ${pageContext.request}<br><!--获取request对象-->
    ${pageContext.request.contextPath}<!--pageContext获取request对象后，使用getContextpath()方法实现在jsp页面动态获取虚拟目录-->
    <h4>动态获取虚拟目录</h4>
    <from action = "${pageContext.request.contextPath}/loginServlet">
        使用示例
    </from>
<%
    response.sendRedirect(request.getContextPath()+"资源路径");//java代码重定向动态获取虚拟目录

%>
</body>
</html>

```

> el表达式中有11个隐式对象，将在后面进行学习。

# JSTL

## 概念

`JavaServer Pages Tag Library` (JSP标准标签库) ，是由Apache组织提供的开源的免费的jsp标签。

## 作用

用于简化和替换jsp页面上的java代码		

## 使用步骤

  1. 导入jstl相关jar包；

     ![JSTL导jar包](https://raw.githubusercontent.com/hekun97/picture/main/img/JSTL%E5%AF%BCjar%E5%8C%85.png)

  2. 在JSP页面中引入标签库：taglib指令： `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`；

  3. 使用标签。

## 常用的JSTL标签

### if

#### 概念

相当于java代码的if语句。

#### 使用

if标签的属性值test为必须填的属性，接受boolean表达式。以下为使用的注意事项：

1.  如果表达式为true，则显示if标签体内容，如果为false，则不显示标签体内容

   ```jsp
   <c:if test="true">
       <h1>我是真...</h1>
   </c:if>
   ```

2. 一般情况下，test属性值会结合el表达式一起使用

   ```jsp
   <%
       //判断request域中的一个list集合是否为空，如果不为null则显示遍历集合
       List list = new ArrayList();
       list.add("aaaa");
       request.setAttribute("list",list);
   %>
   
   <c:if test="${not empty list}">
       遍历集合...
   </c:if>
   ```
   
3. `c:if`标签没有else情况，想要else情况，则可以在定义一个`c:if`标签

     ```jsp
   <%
       //在域中存入一个数字
       request.setAttribute("number",4);
   %>
   <c:if test="${number % 2 != 0}">
           ${number}为奇数
   </c:if>
   
   <c:if test="${number % 2 == 0}">
       ${number}为偶数
   </c:if>
   ```

### choose

#### 概念

相当于java代码的switch语句

#### 使用

这里以一个小案例（完成数字编号对应星期几）来学习choose标签的使用。

##### 完成步骤

1. 域中存储一数字；
2. 使用choose标签取出数字，相当于switch声明；
3. 使用when标签做数字判断，相当于case；
4. otherwise标签做其他情况的声明，相当于default。

##### 实现代码

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %><!--引入JSTL标签库-->

<html>
<head>
    <title>choose标签</title>
</head>
<body>
    <%
        request.setAttribute("number",51);//1.域中存储一数字
    %>
    <c:choose><%--2.使用choose标签取出数字，相当于switch声明--%>
        <c:when test="${number == 1}">星期一</c:when><%--3.使用when标签做数字判断，相当于case--%>
        <c:when test="${number == 2}">星期二</c:when>
        <c:when test="${number == 3}">星期三</c:when>
        <c:when test="${number == 4}">星期四</c:when>
        <c:when test="${number == 5}">星期五</c:when>
        <c:when test="${number == 6}">星期六</c:when>
        <c:when test="${number == 7}">星期天</c:when>
		<%--4.otherwise标签做其他情况的声明，相当于default--%>
        <c:otherwise>数字输入有误</c:otherwise>
    </c:choose>
</body>
</html>
```

### foreach

#### 概念

相当于java代码的for语句，可完成重复的操作和遍历容器。

#### 使用	

##### 完成重复的操作

java的代码为`for(int i = 0; i < 10; i ++){	}`，在JSTL中需对foreach标签的如下属性进行配置：

1. begin：开始值
2. end：结束值
3. var：临时变量
4. step：步长
5. varStatus：循环状态对象
   - index：容器中元素的索引，从0开始
   - count：循环次数，从1开始

```jsp
<c:forEach begin="1" end="10" var="i" step="1" varStatus="s">
    <h3> 索引：${s.index}<h3> <h4> 循环次数：${s.count} </h4> 临时变量：${i} <br>
</c:forEach>
```

##### 遍历容器

java代码：`List<User> list = new List();  for(User user : list){  }`，在JSTL中需对foreach标签的如下属性进行配置：

1. items：容器对象
2. var：容器中元素的临时变量
3. varStatus：循环状态对象
   - index：容器中元素的索引，从0开始
   - count：循环次数，从1开始

```jsp
  <c:forEach items="${list}" var="str" varStatus="s">
       索引：${s.index} 循环次数：${s.count} 临时变量：${str}<br>
  </c:forEach>
```

## 练习

### 需求

在request域中有一个存有User对象的List集合。需要使用jstl+el将list集合数据展示到jsp页面的表格table中。

### 实现代码

实现代码只展示jsp页面的代码，另外需要的User类就是封装用户数据的JavaBean这里不进行展示。

```jsp
<%@ page import="io.gitee.hek97.domain.User" %>
<%@ page import="java.util.List" %>
<%@ page import="java.util.ArrayList" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<%--1.导入jstl的相关jar包并引入jstl标签库--%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<html>
<head>
  <title>JSP+JSTL+EL获取User对象的值</title>
</head>
<body>
<%
  List<User> users = new ArrayList<>();
  //第一名人员信息
  User user1 = new User();
  user1.setId(1001);
  user1.setUsername("张三");
  user1.setPassword("123");
  //第二名人员信息
  User user2 = new User();
  user2.setId(1002);
  user2.setUsername("李四");
  user2.setPassword("456");
  //将user添加到List集合中
  users.add(user1);
  users.add(user2);
  //将信息存储到共享域中
  request.setAttribute("users",users);
%>
<%--视图V:表格展示--%>
<table border="1">
 <%--表头--%>
  <tr>
    <th>索引</th>
    <th>循环次数</th>
    <th>用户id</th>
    <th>用户名</th>
    <th>用户密码</th>
  </tr>
<%--表体--%>
  <%--遍历users集合容器--%>
  <c:forEach items="${users}" var="user" varStatus="s">
    <tr>
      <td>${s.index}</td>
      <td>${s.count}</td>
      <td>${user.id}</td>
      <td>${user.username}</td>
      <td>${user.password}</td>
    </tr>
  </c:forEach>
</table>
</body>
</html>
```

### 效果展示

![](https://raw.githubusercontent.com/hekun97/picture/main/img/60f9128a5132923bf86dd021.jpg)

# 三层架构：软件设计架构

1. 界面层(表示层)：用户看的得界面。用户可以通过界面上的组件和服务器进行交互；
2. 业务逻辑层：处理业务逻辑的；
3. 数据访问层：操作数据存储文件。

![三层架构](https://raw.githubusercontent.com/hekun97/picture/main/img/%E4%B8%89%E5%B1%82%E6%9E%B6%E6%9E%84.bmp)

# 案例：用户信息列表展示

## 需求

用户信息的增删改查操作。

## 设计

1. 技术选型：Servlet+JSP+MySQL+JDBCTempleat+Duird+BeanUtilS+tomcat

2. 数据库设计：
	
	```mysql
	create database today; -- 创建数据库
	use today; 			   -- 使用数据库
	create table user(   -- 创建表
		id int primary key auto_increment,
		name varchar(20) not null,
		gender varchar(5),
		age int,
		address varchar(32),
		qq	varchar(20),
		email varchar(50)
	);	
	```

3. 列表查询设计：

   ![列表查询分析](https://raw.githubusercontent.com/hekun97/picture/main/img/%E5%88%97%E8%A1%A8%E6%9F%A5%E8%AF%A2%E5%88%86%E6%9E%90.bmp)

## 开发

1. 环境搭建
	1. 创建数据库环境；
	2. 创建项目，导入需要的jar包，如下：
	   - JSP页面中JSTL标签库所需jar包：`javax.servlet.jsp.jstl.jar`、`jstl-impl.jar`；
	   - MySQL的数据库驱动jar包：`mysql-connector-java-5.1.18-bin.jar`；
	   -  JDBCtemplate的jar包：`commons-logging-1.1.1.jar`、`spring-beans-4.2.4.RELEASE.jar`、`spring-core-4.2.4.RELEASE.jar`、`spring-jdbc-4.2.4.RELEASE.jar`、`spring-tx-4.2.4.RELEASE.jar`；
	   -  Duird数据库连接池所需jar包：`druid-1.0.9.jar`；
	   -  用来把请求头数据封装到JavaBean·的jar包：`commons-beanutils-1.8.3.jar`；
	   -  可作为Duird后续切换使用的c3p0数据库连接池jar包：`c3p0-0.9.1.2.jar`。
	3. 导入资料文件中的页面文件夹内的HTML等文件。
	
2. 编码

## 测试

## 部署运维

