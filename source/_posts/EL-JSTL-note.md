# JSP

## 指令

  * 作用：用于配置JSP页面，导入资源文件 
  * 格式：`<%@ 指令名称 属性名1=属性值1 属性名2=属性值2 ... %>`
  * 分类（三类）：
    1. `page`： 配置`JSP`页面的
    	* `contentType`：等同于`response.setContentType()`
    		1. 设置响应体的`mime`类型以及字符集
    		2. 设置当前`jsp`页面的编码（只能是高级的IDE才能生效，如果使用低级工具，则需要设置`pageEncoding`属性设置当前页面的字符集）
    		3. 例子：`<%@ page contentType="text/html;charset=UTF-8" language="java" %>`
    	* `import`：导包
    	  * 例子：`<%@ page import="java.util.List" %>`
    	* `errorPage`：当前页面发生异常后，会自动跳转到指定的错误页面
    	* `isErrorPage`：标识当前页面是否是错误页面。
    		* `true`：是，可以使用内置对象exception
    		* `false`：否。默认值。不可以使用内置对象`exception`
    2. `include`	： 页面包含的。导入页面的资源文件
    	* 例子：`<%@include file="top.jsp"%>`
    3. `taglib`	： 导入资源（标签库）
       1. `prefix`：前缀，自定义的
       2. 例子：`<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>`

## 注释

1. html注释：
	`<!-- -->`:只能注释html代码片段
2. jsp注释：推荐使用
	`<%-- --%>`：可以注释所有

## 内置对象

1. 在jsp页面中不需要创建，直接使用的对象
2. 一共有9个：

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


  	 

# MVC：开发模式	

## `jsp`演变历史

1. 早期只有`servlet`，只能使用`response`输出标签数据，非常麻烦
2. 后来使用`jsp`，简化了`Servlet`的开发，如果过度使用`jsp`，在`jsp`中即写大量的`java`代码，又写`html`代码，造成难于维护，难于分工协作
3. 再后来，`java`的`web`开发，借鉴`mvc`开发模式，使得程序的设计更加合理性	

## `MVC`

1. `M`：`Model`，模型。`JavaBean`
	* 完成具体的业务操作，如：查询数据库，封装对象
2. `V`：`View`，视图。`JSP`
	* 展示数据
3. `C`：`Controller`，控制器。`Servlet`
	* 获取用户的输入
	* 调用模型
	* 将数据交给视图进行展示

## MVC的优缺点

1. 优点：
	1. 耦合性低，方便维护，可以利于分工协作；
	2. 重用性高。
2. 缺点：
	1. 使得项目架构变得复杂，对开发人员要求高。

# EL表达式

1. 概念：`Expression Language `（表达式语言）

2. 作用：替换和简化`jsp`页面中`java`代码的编写

3. 语法：`${表达式}`

4. 注意：
	* jsp默认支持el表达式的。如果要忽略el表达式
		1. 设置`jsp`中`page`指令中：`isELIgnored="true" `忽略当前jsp页面中所有的el表达式
		2. `\\${表达式}` ：忽略当前这个el表达式
	
5. 使用：
	1. 运算：
		* 运算符：
			1. 算数运算符：` + - * /(div) %(mod)`
			2. 比较运算符： `> < >= <= == !=`
			3. 逻辑运算符：` &&(and) ||(or) !(not)`
			4. 空运算符： `empty`
				* 功能：用于判断字符串、集合、数组对象是否为`null`或者长度是否为0
				* `${empty list}`:判断字符串、集合、数组对象是否为`null`或者长度为0
				* `${not empty str}`:表示判断字符串、集合、数组对象是否不为`null `并且 长度>0
	2. 获取值
		1. el表达式只能从域对象中获取值
		2. 语法：
			1. ${域名称.键名}：从指定域中获取指定键的值
				* 域名称：
					1. `pageScope`		--> `pageContext`
					2. `requestScope `	--> `request`
					3. `sessionScope `	--> `session`
					4. `applicationScope `--> `application（ServletContext）`
				* 举例：在`request`域中存储了`name=张三`
				* 获取：`${requestScope.name}`
				
			2. `${键名}`：表示依次从最小的域中查找是否有该键对应的值，直到找到为止。
			
			   例：`${name}`，先到`pageContext`中找是否有`name`，再到`request`中去找，直到找到为止。

			3. 获取对象、List集合、Map集合的值
  6. 对象：`${域名称.键名.属性名}`
         本质上会去调用对象的getter方法

        1. List集合：
           * `${域名称.键名[索引]}`
        2. Map集合：
           * `${域名称.键名.key名称}`
           * `${域名称.键名["key名称"]`

        ```jsp
        <!-- el3.jsp -->
        <%@ page import="cn.itcast.domain.User" %>
        <%@ page import="java.util.*" %>
        <%@ page contentType="text/html;charset=UTF-8" language="java" %>
        <html>
        <head>
            <title>el获取数据</title>
        </head>
        <body>
        
            <%
                User user = new User();
                user.setName("张三");
                user.setAge(23);
                user.setBirthday(new Date());
        
                request.setAttribute("u",user);
        
                List list = new ArrayList();
                list.add("aaa");
                list.add("bbb");
                list.add(user);
        
                request.setAttribute("list",list);
        
        
                Map map = new HashMap();
                map.put("sname","李四");
                map.put("gender","男");
                map.put("user",user);
        
                request.setAttribute("map",map);
        
            %>
        
        <h3>el获取对象中的值</h3>
        ${requestScope.u}<br>
        
        <%--
            * 通过的是对象的属性来获取
                * setter或getter方法，去掉set或get，在将剩余部分，首字母变为小写。
                * setName --> Name --> name
        --%>
        
            ${requestScope.u.name}<br>
            ${u.age}<br>
            ${u.birthday}<br>
            ${u.birthday.month}<br>
        
            ${u.birStr}<br>
        
            <h3>el获取List值</h3>
            ${list}<br>
            ${list[0]}<br>
            ${list[1]}<br>
            ${list[10]}<br>
        
            ${list[2].name}
        
            <h3>el获取Map值</h3>
            ${map.gender}<br>
            ${map["gender"]}<br>
            ${map.user.name}
        
        </body>
        </html>
        
        ```

        

        1. 隐式对象：

           * el表达式中有11个隐式对象

           * pageContext：

             1. 获取jsp其他八个内置对象
                `${pageContext.request.contextPath}`：动态获取虚拟目录

                ```jsp
                <%@ page contentType="text/html;charset=UTF-8" language="java" %>
                <html>
                <head>
                    <title>el隐式对象</title>
                </head>
                <body>
                
                
                    ${pageContext.request}<br>
                    <h4>在jsp页面动态获取虚拟目录</h4>
                    ${pageContext.request.contextPath}
                    <from action = "${pageContext.request.contextPath}/loginServlet">
                        使用示例
                    </from>
                
                <%
                    response.sendRedirect(request.getContextPath()+"/day17");
                
                %>
                </body>
                </html>
                
                ```

                

# JSTL

1. 概念：`JavaServer Pages Tag Library` (`JSP`标准标签库) 
	
	* 是由`Apache`组织提供的开源的免费的`jsp`标签
	
2. 作用：用于简化和替换`jsp`页面上的`java`代码		

3. 使用步骤：
  1. 导入`jstl`相关`jar`包

     ![JSTL导jar包](https://raw.githubusercontent.com/hekun97/picture/main/typora202107/20/165742-172097.png)

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

