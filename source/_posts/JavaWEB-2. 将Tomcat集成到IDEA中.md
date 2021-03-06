---
title: 将Tomcat集成到IDEA中
date: 2021-06-24 17:00:49
tags: 
- Java Tomcat IDEA
category:
- JavaWEB
type: artitalk
cover: https://cdn.pixabay.com/photo/2021/04/22/15/46/landscape-6199355_960_720.jpg

---

# 将Tomcat集成到IDEA中

将Tomcat集成到IDEA中，并且创建JavaEE的项目，部署项目。

## 具体步骤

1. 创建一个空项目以便配置Tomcat；

   ![创建JavaWEB项目](https://pic.imgdb.cn/item/60d34577844ef46bb2d78ab7.jpg)

2. 在Run中编辑配置；

   ![在Run中编辑配置](https://pic.imgdb.cn/item/60d340e7844ef46bb2aadf03.jpg)
   
3. 导入Tomcat安装路径；

   ![导入Tomcat安装路径](https://pic.imgdb.cn/item/60d342f5844ef46bb2bec78f.jpg)

4. 配置成功，现在新建一个模块JavaWEB的项目；

   ![](https://pic.imgdb.cn/item/60d34514844ef46bb2d3e12b.jpg)

5. 编辑index.jsp文件，把网页标题和内容进行了修改；

   ![](https://pic.imgdb.cn/item/60d34630844ef46bb2de5319.jpg)

6. 运行该项目；

   ![](https://pic.imgdb.cn/item/60d346cd844ef46bb2e4023b.jpg)

7. 打开浏览器，查看运行结果。

   ![](https://pic.imgdb.cn/item/60d942785132923bf8fac063.jpg)

## 注意事项

### 修改Tomcat服务器的配置信息

在编辑配置中，可对Tomcat服务器进行新增、删除和修改的操作。

![](https://pic.imgdb.cn/item/60d34abd844ef46bb2081c08.jpg)

### 根路径的配置

![](https://pic.imgdb.cn/item/60d34911844ef46bb2f8b043.jpg)

### 热部署

通过热部署，实现在修改资源后，不重启 Tomcat服务器。

![](https://pic.imgdb.cn/item/60d34962844ef46bb2fb9a2d.jpg)

### 访问资源

访问资源通过修改访问路径实现，这里的index.jsp属于配置中声明的默认首页，所以可以不加也能访问到该资源。

![](https://pic.imgdb.cn/item/60d943465132923bf8001128.jpg)

### 目录结构

java动态项目web下目录结构：

```
—— 项目的根目录
	—— WEB-INF目录：
		—— web.xml：web项目的核心配置文件
		—— classes目录：放置字节码文件的目录
		—— libs目录：放置依赖的jar包
```

![](https://pic.imgdb.cn/item/60d34dd8844ef46bb223a792.jpg)
