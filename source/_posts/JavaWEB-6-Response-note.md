---
title: Response相关知识
date: 2021-07-11 14:36:52
tags: 
- Java HTTP Servlet Response
category:
- JavaWEB
type: artitalk
cover: https://cdn.pixabay.com/photo/2021/07/02/09/39/cockpit-6381367_1280.jpg

---

# HTTP协议

## 请求消息

客户端发送给服务器端的数据。

### 数据格式

包含：请求行、请求头、请求空行、请求体。

### 详细解析

详细解析查看<a href="{% post_path 'JavaWEB-4.Servlet&HTTP&Request-note' %}#Request入门">Servlet&HTTP&Request</a>中Request的内容。

## 响应消息

服务器端发送给客户端的数据。

### 数据格式

包含：响应行、响应头、响应空行、响应体。![](https://pic.imgdb.cn/item/60ea985a5132923bf8b31fca.jpg)

### 详细解析

```html
HTTP/1.1 200                         <!--响应行-->
Content-Type: text/html;charset=UTF-8<!--响应头-->
Content-Length: 101
Date: Sun, 11 Jul 2021 06:58:47 GMT
                                     <!--响应空行-->

<html>                               <!--响应体-->
<head>
    <title>Response</title>
</head>
<body>
Hello,Response!
</body>
</html>
```

#### 响应行

1. 组成格式： `协议/版本 响应状态码 状态码描述`，如：`HTTP/1.1 200`。

2. 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态。
  1. 状态码都是3位数字 
  4. 分类：
        1. 1xx：服务器接收客户端消息，但没有接收完成，等待一段时间后，发送1xx状态码；

        2. 2xx：成功，代表：200；

        3. 3xx：重定向，代表：302(重定向)，304(访问缓存)；

           > - 重定向：比如访问demo1路径下的资源时，服务器发送302告诉你该路径的资源不能满足你的要求，然后重定向到demo2路径，去访问demo2路径下的资源。
           >
           > - 访问缓存：比如demo1路径下有张图片资源，之前已经访问过该路径并把该图片缓存到了本地，再访问demo1路径时，服务器就会发送304告诉你去访问本地的缓存图片就可以了，不用从服务器上重新加载该图片。

        4. 4xx：客户端错误，代表：404(请求路径没有对应的资源)，405(请求方式没有对应的doXxx方法)；

        5. 5xx：服务器端错误。代表：500(服务器内部出现异常)。

#### 响应头：

1. 组成格式：`头名称:值`，如：`Content-Type: text/html;charset=UTF-8`。
2. 常见的响应头：
   1. Content-Type：服务器告诉客户端本次响应体数据格式以及编码格式；
   2. Content-disposition：服务器告诉客户端以什么格式打开响应体数据。
      - 值得取值有两种
         1. in-line:默认值,在当前页面内打开；
         2. attachment;filename=xxx：以附件形式打开响应体，比如：文件下载。

#### 响应空行

就是一条空行，用来分隔响应头和响应体。

#### 响应体

传输的数据，原始信息就是html的页面。



# Response对象
## 功能

设置响应消息，包括响应行、响应头、响应体的数据。

## 设置响应行

主要是来设置状态码，使用`setStatus(int sc) `方法。

## 设置响应头

使用`setHeader(String name, String value) `方法。

## 设置响应体

1. 获取输出流；
	* 字符输出流，使用`PrintWriter getWriter()`方法；

	* 字节输出流，使用`ServletOutputStream getOutputStream()`方法。

2. 使用输出流，将数据输出到客户端浏览器。

## Response的更多使用

### 重定向 

#### 概念

资源跳转的一种方式。

![](https://pic.imgdb.cn/item/60eacad15132923bf8c60bc6.jpg)

#### 使用

* 核心代码：
	
  ```java
  //重定向，访问 /responseDemo1 路径会自动跳转到 /responseDemo2 路径。
  /*
  //1.设置状态码为302
  resp.setStatus(302);
  //2.设置响应头location
  resp.setHeader("location","/today/responseDemo2");
  */
  //更简单的重定向，一般都使用该种方式
  resp.sendRedirect("/today/responseDemo2");
  ```
  
  > 请求转发是一种在服务器内部的资源跳转方式。
  >
  > 这里需要和请求转发区分开：请求转发的路径不会变，状态码为200，拥有共享域对象，只能在服务器内部进行跳转；重定向的路径会变，状态码为302，没有共享域对象，可跳转服务器内部和外部的资源。

#### 重定向和转发的区别

##### 重定向(redirect)

1. 地址栏发生变化，状态码为302；
2. 重定向可以访问当前服务器内部和其他站点(服务器外部)的资源；
3. 重定向是两次请求，有两个request对象，不能使用request对象来共享数据；
4. 路径需要加虚拟目录。

##### 转发(forward)

1. 转发地址栏路径不变，状态码为200；
2. 转发只能访问当前服务器内部的资源；
3. 转发是一次请求，可以使用request对象来共享数据；
4. 路径不需要加虚拟目录。

> 请求转发的更多解析可到文章servlet&http&request中的<a href="{% post_path 'JavaWEB-4.Servlet&HTTP&Request-note' %}#请求转发">请求转发</a>查看。							

### 服务器输出字符数据(String)到浏览器

#### 使用步骤

1. 获取字符输出流
2. 输出数据

> 一般用于服务器输出文本信息、HTML代码块到浏览器。

#### 乱码问题

1. `PrintWriter pw = response.getWriter();`获取的流的默认编码是ISO-8859-1；
2. 设置该流的默认编码；
3. 告诉浏览器响应体使用的编码。

简单的形式设置编码，是在获取流之前设置，使用代码：`response.setContentType("text/html;charset=utf-8");`。

#### 核心代码

```java
package io.gitee.hek97.web.servlet;
//去掉了导包的代码

@WebServlet("/responseDemo3")
public class ResponseDemo3 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //获取流对象之前，设置流的编码
        //response.setCharacterEncoding("utf-8");该行可省略，下一行设置响应头中编码的代码会间接设置流的编码。
        //告诉浏览器，服务器发送的消息体数据的编码，建议浏览器使用该编码解码，这里使用的是设置响应头中编码的方法
        response.setHeader("content-type","text/html;charset=utf-8");
        //1.获取字符输出流
        PrintWriter pw = response.getWriter();
        //2.输出数据
        pw.write("你好,response");
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}

```

### 服务器输出字节数据(Byte)到浏览器

#### 使用步骤

1. 获取字节输出流
2. 输出数据

> 一般用于从服务器输出图片到浏览器。

#### 核心代码

```java
package io.gitee.hek97.web.servlet;

//省略导包代码

@WebServlet("/responseDemo4")
public class ResponseDemo4 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //设置响应消息编码
        response.setHeader("content-type","text/html;charset=utf-8");
        //1.获取字节输出流
        ServletOutputStream sos = response.getOutputStream();
        //2.输出数据
        sos.write("你好".getBytes("utf-8"));
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request, response);
    }
}
```

### 练习：验证码

本质就是一张图片，为了防止恶意表单注册。

#### 使用步骤

1. 在内存中生成一张图片
2. 美化图片
3. 输出图片

#### 核心代码

```java
package io.gitee.hek97.web.servlet;

//去除导包代码

@WebServlet("/demo")
public class CheckCodeServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        int width = 100;
        int height = 50;
        //1.创建对象，在内存中存图片（验证码）对象
        BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_3BYTE_BGR);
        //2.美化图片
        //2.1填充背景色
        Graphics g = image.getGraphics();//获取画笔对象
        g.setColor(Color.PINK);//设置画笔颜色
        g.fillRect(0,0,width,height);//设置填充范围
        //2.2画边框
        g.setColor(Color.BLUE);//设置画笔颜色
        g.drawRect(0,0,width-1,height-1);//设置边框范围
        String str ="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
        //生成随机角标
        Random ran = new Random();//获取随机数对象
        for (int i = 1; i <= 4; i++) {
            int index = ran.nextInt(str.length());//生成随机数
            char ch = str.charAt(index);//获取相应位置的字符
            //2.3写验证码
            g.drawString(ch+"",width/5*i,height/2);
        }
        //2.4画干扰线
        g.setColor(Color.GREEN);//设置画笔颜色
        //画10条干扰线
        for (int i = 0; i < 10; i++) {
            //随机生成坐标点
            int x1=ran.nextInt(width);
            int x2=ran.nextInt(width);
            int y1=ran.nextInt(height);
            int y2=ran.nextInt(height);
            //画1条干扰线
            g.drawLine(x1,x2,y1,y2);
        }
        //3.将图片输出到页面展示
        ImageIO.write(image,"jpg",response.getOutputStream());
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}
```

#### 使用代码

在网页中使用验证码，并实现点击图片切换验证码的功能。

```html
<!--login.html-->
<!DOCTYPE html>
<html lang="en">
<script>
    window.onload=function () {
        //1.获取图片对象
        var img =document.getElementById("checkCode");
        //2.绑定单击事件
        img.onclick=function () {
            //加时间戳
            var date = new Date().getTime();
            img.src = "/today/demo?"+date;//时间戳可以防止浏览器认读取本地缓存的图片
        }

    }
</script>
<head>
    <meta charset="UTF-8">
    <title>点击图片切换验证码</title>
</head>
<body>
<img id="checkCode" src="/today/demo"/>//点击图片切换验证码
</body>
</html>
```



## 相对路径和绝对路径

### 相对路径

#### 概念

通过相对路径不可以确定唯一资源，如：`./index.html`，一般以.开头的路径，其中的`./`也可以省略。

#### 使用规则

找到当前资源和目标资源之间的相对位置关系。

> 常用的有`./`：当前目录，`../`：上一级目录。

### 绝对路径

#### 概念

通过绝对路径可以确定唯一资源，如：`http://localhost/day15/responseDemo2`，因为服务器地址是一样的，所以可简化书写为：`/day15/responseDemo2`，以/开头。

#### 使用规则

通过判断定义的路径是给谁使用的？

* 给客户端（浏览器）使用：需要加虚拟目录(项目的访问路径)，相当于在教学楼门前叫张三，需要指明具体是那个班级的张三。
  * 建议虚拟目录动态获取，一但虚拟目录发生改变可以动态获取，不用修改，使用`request.getContextPath()`方法；例如重定向推荐使用：`resp.sendRedirect(req.getContextPath()+"/responseDemo2");`。
  
  > Html中虚拟目录的获取在之后的jsp中学习`<a>` ,` <form> `。
* 给服务器内部使用：不需要加虚拟目录，相当于在班级门前叫张三，直接喊就可以了。

> 可以判断请求将来从哪儿发出，从客户端还是服务器端内部发出，比如转发是从服务器内部发出的，不需要加虚拟目录，重定向是两次请求，由客户端发出，需要加虚拟目录。

![](https://pic.imgdb.cn/item/60eb0f3d5132923bf825bedb.jpg)

# ServletContext对象
## 概念

代表整个web应用，可以和程序的容器(服务器)来通信。

## 获取ServletContext对象

1. 通过request对象获取`request.getServletContext();`；
2. 通过HttpServlet获取`this.getServletContext();`。

> 两个对象获取的ServletContext是没差别的，使用过程中一般通过HttpServlet获取。

## ServletContext对象的功能

主要有获取MIME类型、域对象和获取文件的真实(服务器)路径。

### 具体解析

#### 获取MIME类型

##### 概念

MIME类型是在互联网通信过程中定义的一种文件数据类型，格式为：` 大类型/小类型`，例如：`text/html`，`image/jpeg`。

##### 常用方法

`String getMimeType(String file)  `：获取MIME类型。

##### 核心代码

```java
package io.gitee.hek97.web.servletcontext;

//去掉导包代码

@WebServlet("/servletContextDemo1")
public class ServletContextDemo1 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1.获取ServletContext对象
        ServletContext context = this.getServletContext();
        //定义文件名称
        String filename = "a.jpg";
        //2.获取MIME类型
        String mimeType = context.getMimeType(filename);
        System.out.println(mimeType);//image/jpeg
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}
```

#### 域对象

##### 概念

主要用来在服务器内部共享数据。

> 1. 与<a href="{% post_path 'JavaWEB-4.Servlet&HTTP&Request-note' %}#请求转发">请求转发</a>的域对象相比，ServletContext的域对象的范围更大，请求转发的域对象只存在于转发和被转发的请求之前，ServletContext对象范围为所有用户所有请求的数据（不太安全）；
> 2. ServletContext对象存在生命周期太长，服务器创建便存在，服务器销毁才跟着销毁，所以使用很谨慎，很少使用。

##### 常用方法

1. setAttribute(String name,Object value)：设置域对象；
2. getAttribute(String name)：获取域对象；
3. removeAttribute(String name)：移除域对象。

##### 核心代码

```java
package io.gitee.hek97.web.servletcontext;

//去掉导包代码

@WebServlet("/servletContextDemo2")
public class ServletContextDemo2 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1.通过HttpServlet获取ServletContext对象
        ServletContext context = this.getServletContext();
        //2.设置数据
        context.setAttribute("msg","haha");
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}
```

```java
package io.gitee.hek97.web.servletcontext;

//去掉导包代码

@WebServlet("/servletContextDemo3")
public class ServletContextDemo3 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1.通过HttpServlet获取ServletContext对象
        ServletContext context = this.getServletContext();
        //2.设置数据
        Object msg = context.getAttribute("msg");
        System.out.println(msg);
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}
```

#### 获取文件的真实(服务器)路径

##### 概念

用来获取一些文件的真实路径。

##### 常用方法

String getRealPath(String path) 
String b = context.getRealPath("/b.txt");//web目录下资源访问
  System.out.println(b);

 String c = context.getRealPath("/WEB-INF/c.txt");//WEB-INF目录下的资源访问
 System.out.println(c);

 String a = context.getRealPath("/WEB-INF/classes/a.txt");//src目录下的资源访问
 System.out.println(a);

##### 核心代码

```java
package io.gitee.hek97.web.servletcontext;

//去掉导包代码

@WebServlet("/servletContextDemo4")
public class ServletContextDemo4 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //1.通过HttpServlet获取ServletContext对象
        ServletContext context = this.getServletContext();
        //2.获取文件的服务器路径
        String b = context.getRealPath("/b.txt");//web目录下资源访问
        System.out.println(b);
        //C:\Users\HK\IdeaProjects\Test\out\artifacts\Response_war_exploded\b.txt
        String c = context.getRealPath("/WEB-INF/c.txt");//WEB-INFm
        System.out.println(c);
        //C:\Users\HK\IdeaProjects\Test\out\artifacts\Response_war_exploded\WEB-INF\c.txt
        String a = context.getRealPath("/WEB-INF/classes/a.txt");//src目录下的资源访问
        System.out.println(a);
        //C:\Users\HK\IdeaProjects\Test\out\artifacts\Response_war_exploded\WEB-INF\classes\a.txt
        //src下的文件经过编译后会被放到WEB-INF/classes文件下。
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        this.doPost(request,response);
    }
}
```



## 案例：文件下载
### 文件下载需求

1. 页面显示超链接；
2. 点击超链接后弹出下载提示框；
3. 完成图片文件下载。

### 分析

1. 未设置过的超链接指向的资源如果能够被浏览器解析，则在浏览器中展示，如图片，如果不能解析，则弹出下载提示框，如视频。不满足需求；
2. 要求任何资源都必须弹出下载提示框；
3. 使用响应头设置资源的打开方式。核心代码：`content-disposition:attachment;filename=xxx`

### 使用步骤

1. 定义页面，编辑超链接href属性，指向Servlet，传递资源名称filename
2. 定义Servlet
	1. 获取文件名称；
	2. 使用字节输入流加载文件进内存；
	3. 指定response的响应头： `content-disposition:attachment;filename=xxx`；
	4. 将数据写出到response输出流。

### 中文文件乱码问题

1. 获取客户端使用的浏览器版本信息；
2. 根据不同的版本信息，设置filename的编码方式不同。

核心代码

1. 下载的页面；

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>下载练习</title>
   </head>
   <body>
   <a href="/today/downloadServlet?filename=2.jpg">图片下载</a>
   <a href="/today/downloadServlet?filename=九尾.jpg">中文图片下载</a>
   <a href="/today/downloadServlet?filename=1.avi">视频下载</a>
   </body>
   </html>
   ```

2. 实现的Servlet；

   ```java
   package io.gitee.hek97.web.download;
   
   //省略导包代码
   
   @WebServlet("/downloadServlet")
   public class DownloadServlet extends HttpServlet {
       @Override
       protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           //1.通过获取请求参数，获取文件名称
           String filename = request.getParameter("filename");
           //2.使用字节输入流加载文件进内存
           //2.1找到文件的真实路径
           ServletContext ServletContext = this.getServletContext();
           String realPath = ServletContext.getRealPath("/img/" + filename);
           //2.2用字节流关联
           FileInputStream fis = new FileInputStream(realPath);
           //3.设置response的响应头
           //3.1设置响应头类型content-type
           String mimeType = ServletContext.getMimeType(filename);//获取文件的类型
           response.setHeader("content-type",mimeType);
           //解决中文乱码问题
           //1.获取user-agent中的浏览器信息
           String agent = request.getHeader("user-agent");
           //2.使用工具类解决中文乱码问题，返回设置好格式的字符串
           filename = DownLoadUtils.getFileName(agent, filename);
           //3.2设置响应头content-disposition以附件的形式打开，也就是下载
           response.setHeader("content-disposition","attachment;filename="+filename);
           //4.将输出流的数据写出到输出流中
           ServletOutputStream sos = response.getOutputStream();
           byte[] buff = new byte[1024 * 8];
           int len =0;
           while(((len=fis.read(buff))!=-1)){
               sos.write(buff,0,len);
           }
           fis.close();//g
       }
   
       @Override
       protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           this.doPost(request,response);
       }
   }
   ```

   

3. 中文乱码的工具类。

   ```java
   package io.gitee.hek97.web.utils;
   
   import java.io.UnsupportedEncodingException;
   
   public class DownLoadUtils {
       public static String getFileName(String agent, String filename) throws UnsupportedEncodingException {
           // 针对IE或者以IE为内核的浏览器：
           if (agent.contains("MSIE") || agent.contains("Trident")) {
               filename = java.net.URLEncoder.encode(filename, "UTF-8");
           } else {
               // 非IE浏览器的处理：
               filename = new String(filename.getBytes("UTF-8"), "ISO-8859-1");
           }
           return filename;
       }
   }
   ```

   