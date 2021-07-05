---
title: 使用hexo搭建博客
date: 2020-02-16 17:45:19
tags: 
- hexo
category:
- hexo搭建博客
type: artitalk
cover: https://cdn.pixabay.com/photo/2021/06/21/05/01/clouds-6352673_960_720.jpg
---

# 前言

花了几天用 hexo 搭建了我的个人博客，点击链接可以访问，欢迎来访： [贺坤的个人博客](https://hekun97.github.io/)

## 搭建自己的个人博客总体来说有三种选择

### 1. 使用现有的

现在市面上的博客有很多，如CSDN，博客园，简书等平台。都可以直接在上面发表自己的博客，用户交互也做的很好，写的文章在各大搜索引擎下也能搜索的到，比如百度、搜狗等。但是缺点是不太自由，会受到平台的各种限制和很多烦人的广告。

### 2. 自己购买域名和服务器

这种方式搭建博客的成本比较高，购买成本，还有花费力气自己搭这么一个网站，并且需要定期的维护它，对于我们大多数人来说，实在是没有这样的精力和时间。

### 3. 使用GitHub Page平台

第三种选择，直接在GitHub Page平台上托管我们的博客。这样就可以安心的来写作，又不需要定期维护，而且hexo作为一个快速简洁的博客框架，用它来搭建博客真的非常容易。

## 本教程分三个部分

本教程大部分是通过网络进行收集，并结合我个人的一些理解编写的。

* 第一部分：hexo的搭建并部署到GitHub Page上，以及个人域名的绑定。
* 第二部分：hexo的基本配置，更换主题，实现多终端工作，以及在coding page部署实现国内外分流
* 第三部分：hexo添加各种功能，包括搜索的SEO，阅读量统计，访问量统计和评论系统等。

## 第一部分

hexo的搭建并部署到GitHub Page上，以及个人域名的绑定。

### hexo 简介

hexo是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub和Coding上，是搭建博客的首选框架。大家可以进入[hexo官网](https://hexo.io/)进行详细查看，因为Hexo的创建者是台湾人，对中文的支持很友好，可以选择中文进行查看。

### Hexo搭建步骤

1. 安装Git
2. 安装Node.js
3. 安装Hexo
4. GitHub创建个人仓库
5. 生成SSH添加到GitHub
6. 将hexo部署到GitHub
7. 设置个人域名
8. 发布文章

#### 1. 安装Git

Git是目前世界上最先进的分布式版本控制系统，可以有效、高速的处理从很小到非常大的项目版本管理。也就是用来管理你的hexo博客文章，上传到GitHub的工具。Git非常强大，我觉得建议每个人都去[Git官网](https://git-scm.com/)了解一下。

* windows：到git官网上下载，[Download git](https://git-scm.com/downloads)，下载后会有一个Git Bash的命令行工具，以后就用这个工具来使用git。

* linux：对linux来说实在是太简单了，因为最早的git就是在linux上编写的，只需要一行代码。

```shell
sudo apt-get install git
```

安装好后，用 `git --version` 来查看一下版本

#### 2. 安装nodejs

Hexo是基于nodeJS编写的，所以需要安装一下nodeJs和里面的npm工具。

* windows：nodejs选择LTS版本就行了。[node.js LTS Download](https://nodejs.org/en/download/)

* linux：

```shell
sudo apt-get install nodejs
sudo apt-get install npm
```

安装完后，打开命令行查看版本。

```shell
node -v
npm -v
```

检查一下有没有安装成功

顺便说一下，windows在git安装完后，就可以直接使用git bash来敲命令行了，不用自带的cmd，cmd有点难用。

#### 3. 安装hexo

前面git和nodejs安装好后，就可以安装hexo了，你可以先创建一个文件夹blog，然后cd到这个文件夹下（或者在这个文件夹下直接右键git bash打开）。

输入命令

```shell
npm install -g hexo-cli
```

依旧用`hexo -v`查看一下版本

至此hexo的环境就部署完了。

接下来初始化一下hexo

```shell
hexo init myBlog
```

这个myBlog可以自己取什么名字都行，然后

```shell
cd myBlog //进入这个myBlog文件夹
npm install
```

新建完成后，文件夹目录下有：

* node_modules: 依赖包
* public：存放生成的页面
* scaffolds：生成文章的一些模板
* source：用来存放你的文章
* themes：主题
* _config.yml：config.yml: 博客的配置文件

启动hexo服务

```shell
hexo g
hexo server
```

打开hexo的服务，在浏览器输入localhost:4000 就可以看到你生成的博客了。

使用ctrl+c可以把服务关掉。

#### 4. GitHub创建个人仓库

首先，你先要有一个GitHub账户，没有请注册。[GitHub官网](https://github.com/)

注册完登录后，在GitHub.com中看到一个New repository，新建仓库

创建一个和你用户名相同的仓库，后面加.github.io，比如你注册的用户名叫 zhangsan，那么你创建的仓库名就叫 zhangsan.github.io，只有这样，将来要部署到GitHub Page的时候，才会被识别，也就是xxxx.github.io，其中xxx就是你注册GitHub的用户名。

点击create repository。

#### 5. 生成SSH添加到GitHub

回到你的git bash中。

```shell
git config --global user.name "yourname"
git config --global user.email "youremail"
```

这里的yourname输入你的GitHub用户名，youremail输入你GitHub的邮箱。这样GitHub才能知道你是不是对应它的账户。

可以用以下两条，检查一下你有没有输对

```shell
git config user.name
git config user.email
```

然后输入下面的命令，创建SSH,一路回车就可以了。

```shell
ssh-keygen -t rsa -C "youremail"
```

这个时候它会告诉你已经生成了.ssh的文件夹。在你的电脑中找到这个文件夹。

ssh，简单来讲，就是一个秘钥，其中，id_rsa是你这台电脑的私人秘钥，不能给别人看的，id_rsa.pub是公共秘钥，可以随便给别人看。把这个公钥放在GitHub上，这样当你链接GitHub自己的账户时，它就会根据公钥匹配你的私钥，当能够相互匹配时，才能够顺利的通过git上传你的文件到GitHub上。

而后在GitHub的setting中，找到SSH keys的设置选项，点击New SSH key
把你的id_rsa.pub里面的信息复制进去。

在gitbash中，查看是否成功

```shell
ssh -T git@github.com
```

#### 6. 将hexo部署到GitHub

这一步，我们将hexo和GitHub关联起来，也就是将hexo生成的文章部署到GitHub上，打开站点配置文件_config.yml，翻到最后，修改为
YourgithubName就是你的GitHub账户

```shell
deploy:
  type: git
  repo: https://github.com/**YourgithubName**/**YourgithubName**.github.io.git
  branch: master
```

这个时候需要先安装deploy-git ，也就是部署的命令,这样你才能用命令部署到GitHub。

```shell
npm install hexo-deployer-git --save
```

然后输入

```shell
hexo clean
hexo generate
hexo deplo
```

其中 hexo clean清除了你之前生成的东西，也可以不加。
hexo generate 顾名思义，生成静态文章，可以用 hexo g缩写
hexo deploy 部署文章，可以用hexo d缩写
此处命令可以用一条命令代替，然后等它全部执行完就部署到网站上了，不用在哪里等着输下一条命令

```shell
hexo clean && hexo g && hexo d
```

注意：第一次使用deploy时会让你你输入GitHub的username和password。

得到下图就说明部署成功了，过一会儿就可以在<http://yourname.github.io> 这个网站看到你的博客了！！

#### 7. 设置个人域名

现在你的个人网站的地址是 yourname.github.io，如果觉得这个网址逼格不太够，这就需要你设置个人域名了。但是需要花钱。

注册一个阿里云账户,在阿里云上买一个域名，我买的是 fangzh.top，各个后缀的价格不太一样，比如最广泛的.com就比较贵，看个人喜好咯。

你需要先去进行实名认证,然后在域名控制台中，看到你购买的域名。

点解析进去，添加解析。

其中，192.30.252.153 和 192.30.252.154 是GitHub的服务器地址。
注意，解析线路选择默认，不要像我一样选境外。这个境外是后面来做国内外分流用的,在后面的博客中会讲到。记得现在选择默认！！

登录GitHub，进入之前创建的仓库，点击settings，设置Custom domain，输入你的域名fangzh.top

然后在你的博客文件source中创建一个名为CNAME文件，不要后缀。写上你的域名。

最后，在gitbash中，输入

```shell
hexo clean && hexo g && hexo d
```

过不了多久，再打开你的浏览器，输入你自己的域名，就可以看到搭建的网站啦！

接下来你就可以正式开始写文章了。

```shell
hexo new newpapername
```

然后在source/_post中打开markdown文件，就可以开始编辑了。当你写完的时候，再更新一下。

```shell
hexo clean && hexo g && hexo d
```

就可以看到更新了。
![avatar][base64str]

# 总结

教程总体来说还是比较简单的，用心花费点时间就能搭建好。当然如果你有什么不理解的，可以通过在线聊天找到我！

[base64str]:data:image/png;base64,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