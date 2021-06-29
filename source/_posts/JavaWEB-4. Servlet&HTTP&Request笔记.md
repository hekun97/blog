---
title: Servlet&HTTP&Request
date: 2021-06-24 17:03:52
tags: 
- Java HTTP Servlet Request
category:
- JavaWEB
type: artitalk
cover: https://cdn.pixabay.com/photo/2019/08/08/11/42/butterfly-4392802_1280.jpg
---

# Servlet进阶

在入门阶段学习了Serlet的概念、使用步骤、执行原理、生命周期、注解配置。现在学习更多的Servlet的知识。

## Servlet的体系结构

```
Servlet -- 接口
	|
GenericServlet -- 抽象类
	|
HttpServlet  -- 抽象类
```

### GenericServlet

将Servlet接口中其他的方法做了默认空实现，只将service()方法作为抽象方法，将来定义Servlet类时，可以继承GenericServlet，实现service()方法即可。

> 了解即可，真正开发时不用这种方式。

### HttpServlet

对http协议的一种封装，简化操作。

#### 使用步骤

1. 定义类继承HttpServlet
2. 复写doGet/doPost方法

```java
//为保持简介已去掉导包代码
@WebServlet("/demo")
public class ServletDemo extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doPost...");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet...");
    }
}
```

3. 使用doGet方法，默认情况下直接访问该路径就是get请求，触发该方法。

4. 使用doPost方法，最简单的方式为建立一个表单页面(login.html)，然后访问表单的路径(/login.html)，使用post方法将信息提交到/demo路径下。

   ```html
   <!-- web目录下的login.html 表单-->
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>login</title>
   </head>
   <body>
   <form action="/demo" method="post">
       <input type="text">
       <button type="submit">提交</button>
   </form>
   </body>
   </html>
   ```

## Servlet访问路径

一个Servlet可以定义多个访问路径 ，如`@WebServlet({"/d4","/dd4","/ddd4"})`，然后可以通过其中的任意一个路径进行访问到该资源。

### 路径定义规则

1. /xxx：路径匹配
2. /xxx/xxx:多层路径，目录结构
3. *.do：扩展名匹配

![](https://pic.imgdb.cn/item/60d541dc844ef46bb27ee124.jpg)

## HTTP入门

### HTTP的概念

超文本传输协议（Hypertext Transfer Protocol，HTTP）：定义了客户端和服务器端通信时，发送数据的格式。

![](https://pic.imgdb.cn/item/60d5ec90844ef46bb2ea2c9a.jpg)

### HTTP的特点

1. 基于TCP/IP的高级协议，三次握手。
2. 默认端口号:80
3. 基于请求/响应模型的：发送一次请求对应一次响应
4. 无状态的：每次请求之间相互独立，不能交互数据

### 历史版本的区别

* 1.0：每一次请求响应都会建立新的连接
* 1.1：复用连接

> 只用了解。

### 请求消息(Request)数据格式

包含请求行、请求头(也叫请求头部)、请求空行、请求体（正文）。

#### ![](https://pic.imgdb.cn/item/60d5fff7844ef46bb288ad01.jpg)

#### 请求行

这里的请求行的内容为`POST /demo HTTP/1.1`，请求行的格式为`请求方式 请求url 请求协议/版本`。

  * 请求方式：

    HTTP协议有7种请求方式，常用的有GET和POST2种。
    * GET：
    	1. 请求参数放在请求行中，在url后。类似这种`http://localhost:8080/demo1?username=zhangsan`；
    	
    	2. 请求的url长度是有限制的；
    	
    	3. 不太安全，直接暴露在URL中。
    * POST：
    	1. 请求参数在请求体中；
    	
    	   ![](https://pic.imgdb.cn/item/60d5fe1b844ef46bb27952cd.jpg)
    	
    	2. 请求的url长度没有限制；
    	
    	3. 相对安全，需要通过一定的操作才能看到。

#### 请求头

客户端浏览器告诉服务器一些信息。请求头的格式：`请求头名称: 请求头值`。

#### 请求空行

空行，就是用于分割POST请求的请求头，和请求体的。

#### 请求体(正文)

用来封装POST请求消息的请求参数。


### 响应消息(response)数据格式

> 后续的进阶阶段学习。


# Request入门
## request对象和response对象的基本理解

1. request和response对象是由服务器创建的。我们只是来使用它们；

2. request对象的作用是获取请求消息，response对象的作用是设置响应消息。

## request对象和response对象的原理


![](https://pic.imgdb.cn/item/60d602b7844ef46bb2a0050c.jpg)

## request对象继承体系结构

```java
ServletRequest		--	接口
	|	继承
HttpServletRequest	-- 接口
	|	实现
org.apache.catalina.connector.RequestFacade//该位置是拓展HttpServletRequest接口中的request方法的位置。
```

tomcat源码下的实现类，打开看源码可以发现继承了HttpServletRequest接口。

![](https://pic.imgdb.cn/item/60d68eb3844ef46bb2e088ab.jpg)

## request功能

主要可以获取请求消息数据，包括请求行、请求头、请求体的数据。

### 获取请求行数据

此处示例的请求行的数据内容为`GET /today/demo1?name=zhangsan HTTP/1.1`。

* 方法：
  1. 获取请求方式 ：GET

     String getMethod()  

  2. (重点)获取虚拟目录：/today

     String getContextPath()

  3. 获取Servlet路径: /demo1

     String getServletPath()

     > 经常在表单`action`的路径中用该方法，这样修改servlet路径后，会跟着变化，不用再去修改。

  4. 获取get方式请求参数：name=zhangsan

     String getQueryString()

  5. (重点)获取请求URI：/today/demo1

     String getRequestURI():		`/today/demo1`

     StringBuffer getRequestURL() :  `http://localhost/today/demo1`

     URL:统一资源定位符 ： `http://localhost/today/demo1`	中华人民共和国

     URI：统一资源标识符 : `/today/demo1`					共和国

     > URL和URI的区别：URL是全路径包含HTTP协议、IP地址、资源路径， URI是资源路径。

  6. 获取协议及版本：HTTP/1.1

     String getProtocol()
  
  7. 获取客户机的IP地址：
  
     String getRemoteAddr() 
  
  > 重点掌握获取虚拟目录：/today 和获取请求URI：/today/demo1的方法。
  
  ![](https://pic.imgdb.cn/item/60d6d44e844ef46bb2340c76.jpg)

### 获取请求头数据

* 方法：

  1. (重点)`String getHeader(String name)`:通过请求头的名称获取请求头的值

  2. `Enumeration<String> getHeaderNames()`:获取所有的请求头名称

* 常用的请求头

  1. `User-Agent`

     浏览器告诉服务器，我访问你使用的浏览器版本信息。可以在服务器端获取该头的信息，解决浏览器的兼容性问题。

  ![](https://pic.imgdb.cn/item/60d6967e844ef46bb2030be9.jpg)

  2. Referer：`http://localhost:8080/today/login.html`

     告诉服务器，我(当前请求)从哪里来。

     - 作用：
       1. 防盗链，比如防止盗版网站直接使用正版链接作为外链；
       
       2. 统计工作，比如统计访问量。
       
          ![原理](https://pic.imgdb.cn/item/60d899365132923bf8ce976d.jpg)
       
          ![部分代码](https://pic.imgdb.cn/item/60d6a0b7844ef46bb22f5057.jpg)

### 获取请求体数据

只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数。

* 步骤：
  1. 获取流对象；

     - BufferedReader getReader()：获取字符输入流，只能操作字符数据；

     - ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据。

       > 获取字节输入流，在后续学习文件上传知识点中讲解。

  2. 再从流对象中拿数据；

     ![](https://pic.imgdb.cn/item/60d87dc85132923bf8de7831.jpg)

> ​	这里还需要创建一个login.html的表单页面将数据提交到RequestDemo5的虚拟路径/demo5中。

## 其他功能

该部分介绍的功能都是很常用的方法。

### 获取请求参数通用方式

不论get还是post请求方式都可以使用下列方法来获取请求参数，非常重要。

- 通用方式：

  1. `String getParameter(String name)`:根据参数名称获取参数值。    `username=zs&password=123`

  2. `String[] getParameterValues(String name)`:根据参数名称获取参数值的数组，比如复选框的值。  `hobby=game&hobby=study`

  3. `Enumeration<String> getParameterNames()`:获取所有请求的参数名称。

  4. `Map<String,String[]> getParameterMap()`:获取所有参数的map集合。

- 代码部分：

  ```html
  <!--login.html-->
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>login</title>
  </head>
  <body>
  <form action="/today/demo6" method="post">
      <input type="text" name="username"><br>
      <input type="text" name="password"><br>
      <input type="checkbox" name="hobby" value="game">游戏
      <input type="checkbox" name="hobby" value="study">学习<br>
      <input type="submit" value="提交">
  
  </form>
  
  </body>
  </html>
  ```

  ```Java
  //RequestDemo6.java，其中去除了导包的代码
  @WebServlet("/demo6")
  public class RequestDemo6 extends HttpServlet {
      protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          // 设置编码
          request.setCharacterEncoding("utf-8");		
          //post 获取请求参数
  
          //1. 根据参数名称获取参数值
          String username = request.getParameter("username");
          System.out.println("1. post请求方式："+username);//1. post请求方式：zhangsan
  
          //2. 根据参数名称获取参数值大的数组
          String[] hobbies = request.getParameterValues("hobby");
          for (String hobby : hobbies) {
              System.out.println("2. "+hobby);//2. game 2. study
          }
  
          //3. 获取所有请求的参数名称
          Enumeration<String> parameterNames = request.getParameterNames();
          while (parameterNames.hasMoreElements()){
              String name = parameterNames.nextElement();
              String value = request.getParameter(name);
              System.out.println("3. "+name +":"+value);
              //3. username:zhangsan 3. password:1234 3. hobby:game
              //此处存在问题是复选框的值只打印了一个。
          }
          //4. 获取所有参数的map集合
          Map<String, String[]> parameterMap = request.getParameterMap();
          for (String key : parameterMap.keySet()) {
              String name = key;
              String[] values = parameterMap.get(name);
              System.out.println("4. "+name+":"+ Arrays.toString(values));
              //4. username:[zhangsan] 4. password:[1234] 4. hobby:[game, study]
              //完美解决了复选框只打印一个值的问题
          }
  
      }
  
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          /*
          //get 获取请求参数
          //根据参数名称获取参数值
          String username = request.getParameter("username");
          System.out.println("get请求方式："+username);
          */
          this.doPost(request,response);
      }
  }
  ```

* 中文乱码问题

  tomcat 8 已经将get方式乱码问题解决了，但post方式还会乱码。

  - 解决方法

    在获取参数前，设置request的编码`request.setCharacterEncoding("utf-8");`。			
    ​

### 请求转发

一种在服务器内部的资源跳转方式。

- 步骤：

   1. 通过request对象获取请求转发器对象：`RequestDispatcher getRequestDispatcher(String path)`；

   2. 使用RequestDispatcher对象来进行转发：`forward(ServletRequest request, ServletResponse response) `。

      ![](https://pic.imgdb.cn/item/60d8951f5132923bf8a49eba.jpg)

- 特点：

   1. 浏览器地址栏路径不发生变化，状态码为200；

   2. 只能转发到当前服务器内部资源中，服务器外部的资源会报404；

   3. 转发只有一次请求，使用的只有demo7。

      ![](https://pic.imgdb.cn/item/60d896575132923bf8b0db91.jpg)


### 共享数据

- 相关概念：

  1. 域对象：一个有作用范围的对象，可以在范围内共享数据；

  2. request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据。

     ![解析图](https://pic.imgdb.cn/item/60d899ae5132923bf8d378ee.jpg)

- 方法：

  1. void setAttribute(String name,Object obj):存储数据；
  2. Object getAttitude(String name):通过键获取值；
  3. void removeAttribute(String name):通过键移除键值对。

  > 一般只能用于请求转发中共享数据。

- 代码部分

  ```java
  @WebServlet("/demo7")
  public class RequestDemo7 extends HttpServlet {
      protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          request.setAttribute("msg","hello");
          System.out.println("demo7...");
          request.getRequestDispatcher("/demo8").forward(request,response);
      }
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          this.doPost(request,response);
      }
  }
  ```

  ```java
  @WebServlet("/demo8")
  public class RequestDemo8 extends HttpServlet {
      protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          Object msg = request.getAttribute("msg");
          System.out.println("demo8...");
          System.out.println(msg);//hello
      }
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          this.doPost(request,response);
      }
  }
  
  ```

  

### 获取`ServletContext`

```java
@WebServlet("/demo9")
public class RequestDemo9 extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext servletContext = request.getServletContext();
        System.out.println(servletContext);//这里只需了解获取servletContext
    }
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}
```

> 该部分功能放到进阶部分讲解。
