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

# 1. Git简介

## 1.1 Git的概念

Git是目前世界上最先进的分布式版本控制系统，使用C语言开发。

![Git流程](Git笔记/Git流程.webp)

## 1.2 版本控制系统


概念：版本控制最主要的功能是追踪文件的变更，记录每次文件的改动。

### 1.2.1 集中式VS分布式

CVS、SVN为集中式的版本控制系统，Git为分布式版本控制系统。

### 1.2.2 集中式版本控制系统

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

1. 创建一个空目录；

   ```bash
   $ mkdir learngit
   $ cd learngit
   $ pwd
   /e/GitTest
   ```

   `pwd`用于显示当前目录。

2. 使用`git init`命令把这个目录变成`Git`可以管理的仓库；

   ```bash
   $ git init
   Initialized empty Git repository in E:/GitTest/.git/
   # 创建好空仓库后，当前目录下多了.git的目录，该目录为Git来跟踪管理版本库的。
   ```

3. 新建文件`readme.txt`。

   ```bash
   # 文件内容
   Git is a version control system.
   Git is free software.
   ```

## 添加到版本库

把文件添加到版本库。

```bash
$ git add readme.txt #添加单个文件到版本库
$ git add readme.txt readme2.txt #添加多个文件到版本库
$ git add --all #添加多个文件（所有修改）到版本库
```

## 管理修改

当修改了`readme.txt`文件时。

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

## 撤销修改

### 撤销工作区的修改

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；

一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

**总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。**

1. 发现添加内容有错误，还未添加（add）和提交（commit）到版本库。

2. 使用`git checkout --file`撤销修改回到上一次的添加（add）或提交（commit）的内容。

   ```shell
   $ git checkout --readme.txt
   ```

### 撤销暂存区的修改

1. 发现添加内容有错误，已添加（add）到版本库，还未进行提交（commit）。

2. 用命令`git reset HEAD <file>`可以把暂存区的修改撤销掉（unstage），重新放回工作区。

   ```shell
   $ git reset HEAD readme.txt
   # git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本
   Unstaged changes after reset:
   M	readme.txt
   ```

3. 使用`git status`查看一下，现在暂存区是干净的，工作区有修改。

   ```shell
   $ git status
   On branch master
   Changes not staged for commit:
     (use "git add <file>..." to update what will be committed)
     (use "git checkout -- <file>..." to discard changes in working directory)
   
   	modified:   readme.txt
   ```

4. 使用`git checkout --file`丢弃工作区的修改。

   ```shell
   $ git checkout -- readme.txt
   
   $ git status
   On branch master
   nothing to commit, working tree clean
   ```

### 撤销版本库的修改

1. 发现添加内容有错误，已添加（add）并提交（commit）到版本库。
2. 使用命令`git reset --hard HEAD^ `回到上一个版本即可。

## 删除文件

1. 需要删除某文件，通常直接使用命令`rm file`删除。

   ```shell
   rm test.txt
   ```

2. 使用`git status`查看状态。

   ```shell
   $ git status
   On branch master
   Changes not staged for commit:
     (use "git add/rm <file>..." to update what will be committed)
     (use "git checkout -- <file>..." to discard changes in working directory)
   
   	deleted:    test.txt
   
   no changes added to commit (use "git add" and/or "git commit -a")
   ```

3. 现在分两种情况，确实需要删除和错误删除。

   - 确实需要删除

     那就用命令`git rm`删掉，并且`git commit`。

     ```shell
     $ git rm test.txt
     rm 'test.txt'
     
     $ git commit -m "remove test.txt"
     [master d46f35e] remove test.txt
      1 file changed, 1 deletion(-)
      delete mode 100644 test.txt
     ```

   - 删除错误

     因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本。

     ```shell
     $ git checkout -- test.txt
     # git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”
     ```

# 远程仓库

## 准备工作

可以使用github作为免费的远程仓库。由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以需要一些设置。

1. 创建SSH Key。

   ```shell
   ssh-keygen -t rsa -C "youremail@example.com"
   # 邮箱改为自己的邮件地址，后面一路回车就好了
   ```

   然后在用户主目录下查找.ssh目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

2. 登录GitHub，打开"Accout settings"，"SSH Keys"页面，点击"Add SSH Key"，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容。

![github-addkey-1](https://www.liaoxuefeng.com/files/attachments/919021379029408/0)

为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

## 添加远程库 

1. 登录GitHub，然后在右上角找到"Create a new repo"按钮，创建一个新的仓库。

   ![Git仓库](Git笔记/Git仓库.png)
   
2. 在Repository name填入`learngit`，其他保持默认设置，点击“Create repository”按钮，就成功地创建了一个新的Git仓库。

   ![Git仓库2](Git笔记/Git仓库2.png)

3. 根据GitHub的提示，在本地的`blog`仓库下运行命令。

   ```shell
   $ git remote add origin git@github.com:hekun97/blog.git
   # 其中hekun97为GitHub的账户名
   ```

4. 把本地内容推送到远程库上，`origin`为远程仓库名。

   ```shell
   $ git push -u origin master
   # 由于远程库是空的，第一次推送需加上-u
   Counting objects: 20, done.
   Delta compression using up to 4 threads.
   Compressing objects: 100% (15/15), done.
   Writing objects: 100% (20/20), 1.64 KiB | 560.00 KiB/s, done.
   Total 20 (delta 5), reused 0 (delta 0)
   remote: Resolving deltas: 100% (5/5), done.
   To github.com:michaelliao/learngit.git
    * [new branch]      master -> master
   Branch 'master' set up to track remote branch 'master' from 'origin'.
   ```

## 从远程库克隆

使用命令`git clone`克隆。

```shell
$ git clone git@github.com:hekun97/blog.git # ssh协议
```

```shell
https://github.com/hekun97/blog.git # https协议
```

# 分支管理

分支就是在主分支上创建一个属于你自己的分支，别人看不到，但我能继续工作，想提交就提交，完成之后直接合并到主分支就行。

## 创建与合并分支

### 方法一

1. 创建`dev`分支，然后切换到`dev`分支。

   ```shell
   $ git checkout -b dev
   Switched to a new branch 'dev'
   ```

   `git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：

   ```shell
   $ git branch dev
   $ git checkout dev
   Switched to branch 'dev'
   ```

2. 用`git branch`命令查看当前分支。

   ```shell
   $ git branch
   * dev
     master
   ```

3. 然后在新的分支上就可以进行新功能的开发，并添加（add）和提交（commit）修改。

4. 完成新功能后，切换到主分支。

   ```shell
   $ git checkout master
   Switched to branch &#39;master&#39;55
   ```

5. 把`dev`分支的工作成果合并到`master`分支上。

   ```shell
   $ git merge dev
   Updating d46f35e..b17d20e
   Fast-forward # Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交
    readme.txt | 1 +
    1 file changed, 1 insertion(+)
   ```

6. 删除`dev`分支。

   ```shell
   $ git branch -d dev
   Deleted branch dev (was b17d20e).
   ```

### 方法二

使用`switch`切换分支。我们注意到切换分支使用`git checkout <branch>`，而前面讲过的撤销修改则是`git checkout -- <file>`，同一个命令，有两种作用，确实有点令人迷惑。

因此最新版本的Git提供了新的`git switch`命令来切换分支。

1. 创建并切换到新的dev分支

   ```shell
   $ git switch -c dev
   ```

2. 切换到已有的`master`分支

   ```shell
   $ git switch master
   # 使用新的git switch命令，比git checkout要更容易理解。
   ```

## 分支管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

### 保留`Fast forward`模式的分支信息

1. 创建并切换`dev`分支。

   ```shell
   $ git switch -c dev
   Switched to a new branch 'dev'
   ```

2. 修改readme.txt文件，并提交一个新的commit。

   ```shell
   $ git add readme.txt 
   $ git commit -m "add merge"
   [dev f52c633] add merge
    1 file changed, 1 insertion(+)
   ```

3. 切换到主分支`master`。

   ```shell
   $ git switch master
   Switched to branch 'master'
   ```

4. 合并`dev`分支，请注意添加`--no-ff`参数，表示禁用`Fast forward`。

   ```shell
   $ git merge --no-ff -m "merge with no-ff" dev
   # 因为本次合并要创建一个新的commit，所以加上-m 参数，把commit描述写进去。
   Merge made by the 'recursive' strategy.
    readme.txt | 1 +
    1 file changed, 1 insertion(+)
   ```

5. 使用`git log`查看分支历史。

   ```shell
   $ git log --graph --pretty=oneline --abbrev-commit
   *   e1e9c68 (HEAD -> master) merge with no-ff
   |\  
   | * f52c633 (dev) add merge
   |/  
   *   cf810e4 conflict fixed
   ...
   ```

   分支历史如下图所示。

   ![git-no-ff-mode](https://www.liaoxuefeng.com/files/attachments/919023225142304/0)

### 分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

所以，团队合作的分支看起来就像这样。

![git-br-policy](https://www.liaoxuefeng.com/files/attachments/919023260793600/0)

## “储藏”工作现场

软件开发中，bug就像家常便饭一样。有了bug就需要修复，在Git中，由于分支是如此的强大，所以，每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。

当你接到一个修复一个代号101的bug的任务时，很自然地，你想创建一个分支`issue-101`来修复它，但是，等等，当前正在`dev`上进行的工作还没有提交：