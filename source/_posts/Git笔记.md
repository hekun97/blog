---
title: Git笔记
date: 2020-10-14 14:43:43
tags: 
- Git
- 分布式版本控制系统
category:
- Git
type: artitalk
cover: https://cdn.pixabay.com/photo/2020/10/01/22/31/moss-5619857_960_720.jpg
---

# Git简介

## Git的概念

Git是目前世界上最先进的分布式版本控制系统，使用C语言开发。

![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a1d538d63559402fbcfd82d68b08061c~tplv-k3u1fbpfcp-zoom-1.image?imageslim)

## 集中式VS分布式

CVS、SVN为集中式的版本控制系统，Git为分布式版本控制系统。

### 集中式版本控制系统

集中式版本控制系统，版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。中央服务器就好比是一个图书馆，你要改一本书，必须先从图书馆借出来，然后回到家自己改，改完了，再放回图书馆。

![central-repo](https://www.liaoxuefeng.com/files/attachments/918921540355872/l)

### 分布式版本控制系统

那分布式版本控制系统与集中式版本控制系统有何不同呢？首先，分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。既然每个人电脑上都有一个完整的版本库，那多个人如何协作呢？比方说你在自己电脑上改了文件A，你的同事也在他的电脑上改了文件A，这时，你们俩之间只需把各自的修改推送给对方，就可以互相看到对方的修改了。
和集中式版本控制系统相比，分布式版本控制系统的安全性要高很多，因为每个人电脑里都有完整的版本库，某一个人的电脑坏掉了不要紧，随便从其他人那里复制一个就可以了。而集中式版本控制系统的中央服务器要是出了问题，所有人都没法干活了。
在实际使用分布式版本控制系统的时候，其实很少在两人之间的电脑上推送版本库的修改，因为可能你们俩不在一个局域网内，两台电脑互相访问不了，也可能今天你的同事病了，他的电脑压根没有开机。因此，分布式版本控制系统通常也有一台充当“中央服务器”的电脑，但这个服务器的作用仅仅是用来方便交换“大家的修改，没有它大家也一样干活，只是交换修改不方便而已。

![distributed-repo](https://www.liaoxuefeng.com/files/attachments/918921562236160/l)

### 区别

| 区别     | 集中式                             | 分布式                                                   |
| -------- | ---------------------------------- | -------------------------------------------------------- |
| 版本库   | 存放在中央服务器                   | 没有“中央服务器”                                         |
| 联网     | 必须联网（最大的毛病）             | 不需要联网                                               |
| 安全性   | 中央服务器出问题，所有人都没法干活 | 某一个人的电脑坏掉了不要紧，随便从其他人哪里复制一个即可 |
| 分支管理 | 分支功能不够强大                   | 极其强大的分支管理                                       |
| 暂存区   | 无                                 | 有                                                       |

# 使用

## 创建版本库

1. 创建一个空目录

   

   ```bash
   $ mkdir learngit
   $ cd learngit
   $ pwd
   /e/GitTest
   ```

   `pwd`用于显示当前目录。

2. 使用`git init`命令把这个目录变成`Git`可以管理的仓库。

   ```bash
   $ git init
   Initialized empty Git repository in E:/GitTest/.git/
   # 创建好空仓库后，当前目录下多了.git的目录，该目录为Git来跟踪管理版本库的。
   ```
   
   
   
3. 新建文件`readme.txt`

   ```bash
   # 文件内容
   Git is a version control system.
   Git is free software.
   ```

## 把文件添加到版本库

```bash
$ git add readme.txt #添加单个文件到版本库
$ git add readme.txt readme2.txt #添加多个文件到版本库
$ git add --all #添加多个文件（所有修改）到版本库
```

## 当修改了`readme.txt`文件时

```bash
# 修改内容，新增单词distributed
Git is a distributed version control system.
Git is free software
```

1. 运行`git status`查看仓库当前的状态

   ```bash
   $ git status
   On branch master
   Changes not staged for commit:
     (use "git add <file>..." to update what will be committed)
     (use "git restore <file>..." to discard changes in working directory)
           modified:   readme.txt
   
   no changes added to commit (use "git add" and/or "git commit -a")
   ```

   

2. 当不清楚修改的内容时，运行`git diff`查看具体修改了什么内容

   ```bash
   $ git diff
   diff --git a/readme.txt b/readme.txt
   index d8036c1..013b5bc 100644
   --- a/readme.txt
   +++ b/readme.txt
   @@ -1,2 +1,2 @@
   -Git is a version control system.
   +Git is a distributed version control system.
    Git is free software.
   \ No newline at end of file
   ```

3. 添加到仓库，运行`git add`

   ```bash
   $ git add readme.txt
   ```

4. 提交到仓库，运行`git commit -m "add distributed"`

   ```bash
   $ git commit -m "add distributed"
   [master b622eeb] add distributed
    1 file changed, 1 insertion(+), 1 deletion(-)
   ```

5. 运行`git status`查看仓库当前的状态

   ```bash
   $ git status
   On branch master
   nothing to commit, working tree clean
   ```

   

## 版本回退

每次修改文件后，`commit`一次，称为一个新版本。

### 当需要回退到过去的版本时

1. 通过`git log`查看历史记录

   ```bash
   $ git log #显示从最近到最远的提交日志
   commit b622eebed1ea48b4adfa329e690edc5918ceb553 (HEAD -> master)
   Author: hekun97 <hekun97@outlook.com>
   Date:   Fri Oct 16 09:11:39 2020 +0800
   
       add distributed
   
   commit 65d3c6e47f76bc9475c3b841ca44fed1d2752a3c
   Author: hekun97 <hekun97@outlook.com>
   Date:   Thu Oct 15 17:26:30 2020 +0800
   
       renamed and deleted some files
   
   commit e786283270d3ae4489fba874f755d6d7ebde70e4
   Author: hekun97 <hekun97@outlook.com>
   Date:   Thu Oct 15 15:56:19 2020 +0800
   
       add more files
   
   commit 61f336fd68ff39bcd1da26324c755f71a65ccb72
   Author: hekun97 <hekun97@outlook.com>
   Date:   Thu Oct 15 15:21:12 2020 +0800
   
       wrote a readme file
   ```

   可加上`--pretty=oneline`参数减少信息，使输出信息为一行，前面的`b622ee...`为版本号

   ```bash
   $ git log --pretty=oneline # 使输出信息为一行
   b622eebed1ea48b4adfa329e690edc5918ceb553 (HEAD -> master) add distributed
   65d3c6e47f76bc9475c3b841ca44fed1d2752a3c renamed and deleted some files
   e786283270d3ae4489fba874f755d6d7ebde70e4 add more files
   61f336fd68ff39bcd1da26324c755f71a65ccb72 wrote a readme file
   ```

2. 使用`git reset`进行回退。其中上一个版本为`HEAD^`，上上一个版本为`HEAD^^`，更多的可以写为`HEAD~100`。

   ```bash
   # 写法1
   $ git reset --hard HEAD^ # 回退到 renamed and deleted some files 
   HEAD is now at 65d3c6e renamed and deleted some files
   # 写法2
   $ git reset --hard HEAD~1 # 回退到 renamed and deleted some files 
   HEAD is now at 65d3c6e renamed and deleted some files
   # 写法3
   $ git reset --hard 65d3c 
   # 回退到 renamed and deleted some files ，版本号不用输入全部，但不要太短，避免冲突
   HEAD is now at 65d3c6e renamed and deleted some files
   ```

3. 使用`git log`查看版本信息

   ```bash
   $ git log --pretty=oneline
   65d3c6e47f76bc9475c3b841ca44fed1d2752a3c (HEAD -> master) renamed and deleted some files
   e786283270d3ae4489fba874f755d6d7ebde70e4 add more files
   61f336fd68ff39bcd1da26324c755f71a65ccb72 wrote a readme file
   ```

### 当需要回到未来的版本时

1. 后悔需要回到`add distributed`的版本时，使用`git reflog`命令查看之前使用过的每一次命令。

   ```bash
   $ git reflog
   65d3c6e (HEAD -> master) HEAD@{0}: reset: moving to HEAD^
   b622eeb HEAD@{1}: commit: add distributed
   65d3c6e (HEAD -> master) HEAD@{2}: commit: renamed and deleted some files
   e786283 HEAD@{3}: commit: add more files
   61f336f HEAD@{4}: commit (initial): wrote a readme file
   ```

2. 找到`add distributed`的版本号，然后使用`git reset --hard b622e`回到`add distributed`的版本

   ```bash
   $ git reset --hard b622e
   HEAD is now at b622eeb add distributed
   ```

3. 使用`git log`查看版本信息

   ```bash
   $ git log --pretty=oneline
   b622eebed1ea48b4adfa329e690edc5918ceb553 (HEAD -> master) add distributed
   65d3c6e47f76bc9475c3b841ca44fed1d2752a3c renamed and deleted some files
   e786283270d3ae4489fba874f755d6d7ebde70e4 add more files
   61f336fd68ff39bcd1da26324c755f71a65ccb72 wrote a readme file
   ```

### 题外话

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是改变了指针的指向。

#### 修改前

```ascii
┌────┐
│HEAD│
└────┘
   │
   └──> ○ append GPL
        │
        ○ add distributed
        │
        ○ wrote a readme file
```

#### 修改后

```ascii
┌────┐
│HEAD│
└────┘
   │
   │    ○ append GPL
   │    │
   └──> ○ add distributed
        │
        ○ wrote a readme file
```