---
title: Tomcat基础知识
date: 2021-06-24 16:57:43
tags: 
- Java Tomcat
category:
- JavaWEB
type: artitalk
cover: https://cdn.pixabay.com/photo/2021/06/20/08/28/wheat-grass-6350274_960_720.jpg
---

# web相关概念回顾

## 软件架构

1. C/S：客户端/服务器端
2. B/S：浏览器/服务器端

## 资源分类

1. 静态资源：所有用户访问后，得到的结果都是一样的，称为静态资源.静态资源可以直接被浏览器解析
	* 如： html,css,JavaScript
2. 动态资源:每个用户访问相同资源后，得到的结果可能不一样。称为动态资源。动态资源被访问后，需要先转换为静态资源，在返回给浏览器
	* 如：servlet/jsp,php,asp....

## 网络通信三要素

1. IP：电子设备(计算机)在网络中的唯一标识。
2. 端口：应用程序在计算机中的唯一标识。 0~65536
3. 传输协议：规定了数据传输的规则
	- 基础协议：
		1. tcp：安全协议，三次握手，速度稍慢。
		2. udp：不安全协议，速度快。


# web服务器软件
## 服务器

安装了服务器软件的计算机。

## 服务器软件

接收用户的请求，处理请求，做出响应。（MySQL）

## web服务器软件

接收用户的请求，处理请求，做出响应。（让用户通过浏览器来访问）

* 在web服务器软件中，可以部署web项目，让用户通过浏览器来访问这些项目
* web容器，动态资源只能在web容器中运行。

## 常见的java相关的web服务器软件

1. webLogic：oracle公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
2. webSphere：IBM公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
3. JBOSS：JBOSS公司的，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
4. Tomcat：Apache基金组织，中小型的JavaEE服务器，仅仅支持少量的JavaEE规范servlet/jsp。开源的，免费的。

> JavaEE规范：Java语言在企业级开发(JavaEE)中使用的技术规范的总和，一共规定了13项大的规范。

# Tomcat：web服务器软件

## 使用步骤

### 下载

在[Tomcat官网](http://tomcat.apache.org/)下载即可。

### 安装

解压下载的压缩包即可。

![](https://pic.imgdb.cn/item/60be2f31844ef46bb200848c.jpg)

> 注意：安装目录建议不要有中文和空格

### 卸载

直接删除Tomcat的安装目录。

### 启动

* 找到Tomcat安装目录中的bin/startup.bat文件 ，双击运行该文件即可
* 访问：浏览器输入`http://localhost:8080 `回车访问自己。访问别人网站的格式：` http://别人的ip:8080`。

#### 启动可能遇到的问题

1. 黑窗口一闪而过：
	* 原因： 没有正确配置JAVA_HOME环境变量
	* 解决方案：正确配置JAVA_HOME环境变量

2. 启动报错：

   - 暴力：找到占用的端口号，并且找到对应的进程，使用命令`netstat -ano`杀死该进程。

   - 温柔：修改自身的端口号

      找到Tomcat安装目录中的conf/server.xml文件，通过修改下面代码中的port修改端口。

      ```java
      <Connector port="8888" protocol="HTTP/1.1"
          connectionTimeout="20000"
          redirectPort="8445" />
      ```

      > 一般会将tomcat的默认端口号修改为80。80端口号是http协议的默认端口号。好处：在访问时，就不用输入端口号。

### 关闭

1. 正常关闭：
	* 找到Tomcat安装目录中的bin/shutdown.bat脚本文件，双击运行该脚本。
	* 在运行`bin/startup.bat`脚本的窗口中按下组合键ctrl+c关闭。
2. 强制关闭：
	* 点击运行`bin/startup.bat`脚本窗口的×。

## 部署WEB项目的三种方式

### 直接进行部署的两种方式

1. 直接将WEB项目放到Tomcat安装目录中的webapps目录下即可。

> 访问路径中的hello是项目的虚拟目录，hello.html是资源名称。

​	![image-20210607222317187](https://pic.imgdb.cn/item/60be2bf3844ef46bb2b387d6.jpg)

2. 将项目打成一个war包，再将war包放置到webapps目录下，可简化部署操作。

> 在tomcat服务器中，war包会自动解压缩。

缺点：必须把项目放到webapps目录下。

### 修改server.xml文件进行部署

1. 在Tomcat安装目录中找到conf/server.xml文件并打开；

2. 在`<Host>`标签体中配置
   `<Context docBase="D:\hello" path="/hehe" />`

   > docBase:项目存放的路径
   >
   > path：虚拟目录

3. 双击bin/startup.bat文件启动服务器；

4. 在浏览器中访问WEB项目。

   ![image-20210607223129490](https://pic.imgdb.cn/item/60be2dca844ef46bb2df16f5.jpg)

缺点：需要到核心文件server.xml中去配置，在正式的WEB开发中很少会修改tomcat服务器的核心文件。

### 热部署

1. 在tomcat安装路径下conf\Catalina\localhost创建任意名称的xml文件，这里举例使用的是bbb.xml；
2. 打开文件，在文件中编写代码
   `<Context docBase="D:\hello" />`；
3. 启动服务器；
4. 浏览该WEB项目。

  ![image-20210607223022034](https://pic.imgdb.cn/item/60be2d88844ef46bb2d8b118.jpg)

  > 虚拟目录为xml文件的名称。

优点：热部署。后续使用idea创建WEB项目时，idea使用的就是这种部署方式。
