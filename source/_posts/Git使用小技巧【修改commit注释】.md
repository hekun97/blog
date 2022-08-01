---
title: Git使用小技巧【修改commit注释】
date: 2022-08-01 12:43:21
tags: 
- Git
category:
- Git
type: artitalk
cover: https://cdn.pixabay.com/photo/2022/05/18/12/04/flower-7205105_1280.jpg

---

# Git使用小技巧【修改commit注释】

通常有些朋友在 `git  commit -m "xxxx" file.c` 后，觉得注释的内容描述不够精准，
想将`"xxxx"`修改为`"yyyy"`, 该如何操作呢？
通常有以下两种情况：

第一种： commit之后，将代码`git push`到远程仓库的

第二种:：commit之后，还没有`push`, 代码还在本地仓库的

如果是第一种情况会多一个步骤

## 一、修改最后一次提交的注释

首先，如果仅仅是想修改最后一次注释，修改步骤如下：

1. `git commit --amend`         【第一行出现注释界面】
2. `i`                                         【进入修改模式, 修改完成】
3. `Esc`                                    【退出编辑模式】
4. `:wq`                                    【保存并退出即可】
5. `git log`                             【 查看提交记录】

![img](https://cdn.jsdelivr.net/gh/hekun97/picture/Git_notes/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW95dWxpa2U=,size_16,color_FFFFFF,t_70.png)

## 二、修改以前提交的注释

当需要修改以前早就提交过的注释时，可按以下步骤修改：

1. `git rebase -i HEAD~2`       【数字指的是倒数第n次提交记录的注释】
2. `pick` 改成 `edit`                  【输入 i 编辑模式，只需要将你需要修改的注释前的pick 改为 edit 即可】
3. `Esc`                                       【退出编辑模式】
4. `:wq`                                        【保存退出】
5. `git commit --amend`              【同上有提示，第一行进行你真正需要的修改, 修改完后，保存退出】
6. `git rebase --continue`            【退出后，输入最后一步】

图文步骤如下：

1. git rebase -i HEAD~2 会出现此界面

   ![image-20220801115108768](https://cdn.jsdelivr.net/gh/hekun97/picture/Git_notes/image-20220801115108768.png)

2. pick 改为 edit 后，退出

   ![img](https://cdn.jsdelivr.net/gh/hekun97/picture/Git_notes/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW95dWxpa2U=,size_16,color_FFFFFF,t_70-20220801113531370.png)

3. 会出现，非分支，正变基的分支，别害怕，继续往下 git commit --amend

   ![img](https://cdn.jsdelivr.net/gh/hekun97/picture/Git_notes/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW95dWxpa2U=,size_16,color_FFFFFF,t_70-20220801113534544-20220801115001018.png)

4. 第一行修改注释，保存退出

   ![img](https://cdn.jsdelivr.net/gh/hekun97/picture/Git_notes/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW95dWxpa2U=,size_16,color_FFFFFF,t_70-20220801114957165.png)

5. 最后一步, git rebase --continue, 修改完成啦

   ![img](https://cdn.jsdelivr.net/gh/hekun97/picture/Git_notes/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW95dWxpa2U=,size_16,color_FFFFFF,t_70-20220801114953877.png)

6. git log 可以查看已经修改成功了

   ![img](https://cdn.jsdelivr.net/gh/hekun97/picture/Git_notes/20210728123421918.png)

## 三、已经将代码push到远程仓库的

按照下面的步骤修改：

1. 首先，把最新的版本从远程仓库先pull下来；

   `git pull origin master`

2. 对最新版本的注释进行修改，修改的方法都如上`一`和`二`；

3. 最后修改完成后，强制push到远程仓库。

   `git push --force origin master`

> 注：很重要的一点是，你最好保证在你强制push之前没有人提交代码，如果在你push之前有人提交了新的代码到远程仓库，然后你又强制push，那么会被你的强制更新覆盖！！！
>
> push前先使用`git status`确认下即可。