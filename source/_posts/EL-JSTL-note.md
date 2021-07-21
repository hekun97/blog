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

1. 概念：`JavaServer Pages Tag Library` (`JSP`标准标签库) 
	
	* 是由`Apache`组织提供的开源的免费的`jsp`标签
	
2. 作用：用于简化和替换`jsp`页面上的`java`代码		

3. 使用步骤：
  1. 导入`jstl`相关`jar`包

     ![JSTL导jar包](https://raw.githubusercontent.com/hekun97/picture/main/typora202107/21/170931-394261.png)

  2. 引入标签库：

     1. `taglib`指令： `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`

  3. 使用标签	

4. 常用的`JSTL`标签

   1. `if`:相当于`java`代码的`if`语句

      1. 属性：

         1. `test `必须属性，接受`boolean`表达式

            1.  如果表达式为`true`，则显示if标签体内容，如果为`false`，则不显示标签体内容

               ```jsp
               <c:if test="true">
                   <h1>我是真...</h1>
               </c:if>
               ```

            2. 一般情况下，`test`属性值会结合`el`表达式一起使用

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

            3. 注意：

                 `c:if`标签没有`else`情况，想要`else`情况，则可以在定义一个`c:if`标签

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

   2. `choose`:相当于java代码的switch语句

      ```jsp
      <%@ page contentType="text/html;charset=UTF-8" language="java" %>
      
      <%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
      
      <html>
      <head>
          <title>choose标签</title>
      </head>
      <body>
      
          <%--
              完成数字编号对应星期几案例
                  1.域中存储一数字
                  2.使用choose标签取出数字         相当于switch声明
                  3.使用when标签做数字判断         相当于case
                  4.otherwise标签做其他情况的声明  相当于default
          --%>
      
          <%
              request.setAttribute("number",51);
          %>
      
          <c:choose>
              <c:when test="${number == 1}">星期一</c:when>
              <c:when test="${number == 2}">星期二</c:when>
              <c:when test="${number == 3}">星期三</c:when>
              <c:when test="${number == 4}">星期四</c:when>
              <c:when test="${number == 5}">星期五</c:when>
              <c:when test="${number == 6}">星期六</c:when>
              <c:when test="${number == 7}">星期天</c:when>
      
              <c:otherwise>数字输入有误</c:otherwise>
          </c:choose>
      
      </body>
      </html>
      ```

      

   3. `foreach`:相当于`java`代码的`for`语句	

      1. 完成重复的操作
         `for(int i = 0; i < 10; i ++){	}`

         * 属性：
           `begin`：开始值
           `end`：结束值
           `var`：临时变量
           `step`：步长
           `varStatus`:循环状态对象
              `index`:容器中元素的索引，从0开始
              `count`:循环次数，从1开始

         ```jsp
         <c:forEach begin="1" end="10" var="i" step="1" varStatus="s">
             临时变量：${i} <h3>索引：${s.index}<h3> <h4> 循环次数：${s.count} </h4><br>
         </c:forEach>
         ```

      2. 遍历容器
           `List<User> list;  for(User user : list){  }`

           * 属性：
             `items`:容器对象
             `var`:容器中元素的临时变量
             `varStatus`:循环状态对象
                 `index`:容器中元素的索引，从0开始
                 `count`:循环次数，从1开始

           ```jsp
             <c:forEach items="${list}" var="str" varStatus="s">
             
                     索引：${s.index} 循环次数：${s.count} 临时变量：${str}<br>
             
             </c:forEach>
           ```

5. 练习：
	
	* 需求：在request域中有一个存有User对象的List集合。需要使用jstl+el将list集合数据展示到jsp页面的表格table中

# 三层架构：软件设计架构

1. 界面层(表示层)：用户看的得界面。用户可以通过界面上的组件和服务器进行交互
2. 业务逻辑层：处理业务逻辑的。
3. 数据访问层：操作数据存储文件。

# 案例：用户信息列表展示

1. 需求：用户信息的增删改查操作
2. 设计：
	1. 技术选型：Servlet+JSP+MySQL+JDBCTempleat+Duird+BeanUtilS+tomcat
	
	2. 数据库设计：
		
		```mysql
		create database day17; -- 创建数据库
		use day17; 			   -- 使用数据库
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
	
3. 开发：
	1. 环境搭建
		1. 创建数据库环境
		2. 创建项目，导入需要的jar包

	2. 编码
4. 测试
5. 部署运维

