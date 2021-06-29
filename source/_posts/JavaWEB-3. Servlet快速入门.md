---
title: Servlet快速入门
date: 2021-06-24 17:01:52
tags: 
- Java Tomcat Servlet
category:
- JavaWEB
type: artitalk
cover: https://cdn.pixabay.com/photo/2021/05/24/12/17/rocks-6278936_960_720.jpg

---

# Servlet

## 概念

Servlet(server applet)是运行在服务器端的小程序。

![图解](https://pic.imgdb.cn/item/60d34f53844ef46bb231cab9.jpg)

- Servlet就是一个接口，定义了Java类被浏览器访问到(tomcat识别)的规则；
- 使用时需要自定义一个类来实现Servlet接口，复写Servlet接口的方法。

> 动态资源：不同的人访问的资源可能不一样。

## 快速入门

  1. 创建一个JavaWEB项目

     ![](https://pic.imgdb.cn/item/60d35175844ef46bb2486922.jpg)

  2. 定义一个类，实现Servlet接口，代码为`public class ServletDemo1 implements Servlet`

  3. 覆写接口中的抽象方法

     ![](https://pic.imgdb.cn/item/60d35440844ef46bb26a28d7.jpg)

  4. 配置Servlet，在web.xml中配置Servlet，并映射虚拟路径。
     
     ![](https://pic.imgdb.cn/item/60d355a7844ef46bb27ba45e.jpg)
     
5. 启动Tomcat服务器，并访问相应虚拟路径；

   ![访问资源](https://pic.imgdb.cn/item/60d3fcf4844ef46bb213d5f3.jpg)
   
   ![控制台的输出](https://pic.imgdb.cn/item/60d3fd45844ef46bb2158b99.jpg)

## 执行原理

1. 当服务器接受到客户端浏览器的请求后，会解析请求URL路径，获取访问的Servlet的资源路径；

2. 查找web.xml文件，是否有对应的`<url-pattern>`标签体内容；

3. 如果有，则在找到对应的`<servlet-class>`全类名；

4. tomcat会将字节码文件加载进内存，并且创建其对象；

5. 调用其方法。

   ![](https://pic.imgdb.cn/item/60d48af6844ef46bb2631f6f.jpg)

## Servlet的生命周期

Servlet中的生命周期主要有Servlet被创建时执行一次的init方法，提供服务的service方法和正常关闭服务器时执行一次的destroy方法。

### init方法

被创建时执行init方法，只执行一次，Servlet默认情况下，第一次被访问时，Servlet被创建。

- 可以在web.xml中的`<servlet>`标签下配置执行Servlet的创建时机。

  1. 第一次被访问时，创建；

     `<load-on-startup>`的值为负数

  2. 在服务器启动时，创建。

     `<load-on-startup>`的值为0或正整数

> Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的。
>
> 1. 多个用户同时访问时，可能存在线程安全问题。
> 2. 解决方法：尽量不要在Servlet中定义成员变量。即使定义了成员变量，也不要对修改值

### service方法

提供服务时执行service方法，可执行多次。
* 每次访问Servlet时，Service方法都会被调用一次。

### destroy方法

被销毁时执行destroy方法，只执行一次。

> 1. 服务器关闭时，Servlet被销毁。
> 2. 只有服务器正常关闭时，才会执行destroy方法。
> 3. destroy方法在Servlet被销毁之前执行，一般用于释放资源。

## Servlet的注解配置

Servlet3.0以上支持注解配置。可以不需要web.xml了。

### 使用步骤

1. 创建JavaEE项目，选择Servlet的版本3.0以上，不创建web.xml；

   ![](https://pic.imgdb.cn/item/60d43fb3844ef46bb2d035fc.jpg)

2. 定义一个类，实现Servlet接口

3. 复写Servlet的方法

4. 在类上使用@WebServlet注解，进行配置，代码`@WebServlet("/资源路径")`。

   > 资源路径必须加上反斜线，不然会报错。

   

   ![](https://pic.imgdb.cn/item/60d48cbb844ef46bb26f87b3.jpg)

### 实现原理

WebServlet的注解中对XML的配置给了默认值，可根据自己的需求进行传入参数，默认传入的为资源路径。

   ```java
   @Target({ElementType.TYPE})
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   public @interface WebServlet {
   String name() default "";//相当于<Servlet-name>
   
   String[] value() default {};//代表urlPatterns()属性配置
   
   String[] urlPatterns() default {};//相当于<url-pattern>
   
   int loadOnStartup() default -1;//相当于<load-on-startup>
   
   WebInitParam[] initParams() default {};
   
   boolean asyncSupported() default false;
   
   String smallIcon() default "";
   
   String largeIcon() default "";
   
   String description() default "";
   
   String displayName() default "";
   }
   ```

 默认传入的是虚拟目录路径。![默认传入](https://pic.imgdb.cn/item/60d4437f844ef46bb2e77bc5.jpg)

# IDEA与tomcat的相关配置

## IDEA使用tomcat服务器的解析

IDEA会为每一个tomcat部署的项目单独建立一份配置文件。

解析如下：

1. 启动服务器后，将控制台的log信息翻到最顶部，找到`Using CATALINA_BASE:   "C:\Users\HK\AppData\Local\JetBrains\IntelliJIdea2020.1\tomcat\_Test"`。

   ![](https://pic.imgdb.cn/item/60d48f2f844ef46bb281a810.jpg)

2. 在文件资源管理器中打开该路径，路径下的资源就是IDEA中当前项目对Tomcat的配置。其中conf 为配置信息文件夹，比如该文件下的server.xml可以查看服务器的端口等信息，logs为日志文件夹。

   ![目录信息](https://pic.imgdb.cn/item/60d490e0844ef46bb28e8581.jpg)

   ![conf下的server.xml文件](https://pic.imgdb.cn/item/60d491a6844ef46bb294a0d1.jpg)

   > 可以发现该路径下的目录和文件和tomcat安装路径下的目录和文件差不多。为了便于理解，这里简单的认为在该路径下和在安装tomcat的路径下热部署WEB项目是一样的。

3. Catalina文件夹下的文件为Tomcat的第三种项目部署-热部署的方式。

  ![Catalina文件夹下的文件](https://pic.imgdb.cn/item/60d491ed844ef46bb296d64d.jpg)

  ![Tomcat_war_exploded2.xml文件信息](https://pic.imgdb.cn/item/60d4925d844ef46bb29a870d.jpg)

4. 在资源管理器中打开docBase中的路径，就是需要部署的WEB项目文件。包含jsp、html、css等文件，在WEB-INF下包含class等文件。

  ![](https://pic.imgdb.cn/item/60d492e5844ef46bb29ee883.jpg)

## 区分工作空间项目和tomcat部署的web项目

- `"C:\Users\HK\AppData\Local\JetBrains\IntelliJIdea2020.1\tomcat\_Test"`为工作空间项目，也就是在IDEA中编辑的WEB项目，可以理解为源代码;
- `"C:\Users\HK\IdeaProjects\Test\out\artifacts\Tomcat_war_exploded2`"为tomcat部署的web项目，其中的Java代码已经被编译为class文件存放到web/WEB-INF目录下。

> 1. tomcat真正访问的是“tomcat部署的web项目”，"tomcat部署的web项目"对应着"工作空间项目" 的web目录下的所有资源；
>
> 2. WEB-INF目录下的资源不能被浏览器直接访问，不要把WEB资源放到该目录下。

![](https://pic.imgdb.cn/item/60d494bf844ef46bb2ae790c.jpg)

## 断点调试

使用"小虫子"启动 dubug 启动。

![](https://pic.imgdb.cn/item/60d496b0844ef46bb2bed4e2.jpg)