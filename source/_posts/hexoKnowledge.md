---
title: hexo的更多知识
date: 2021-07-11 16:38:40
categories: 
- hexo
tags:
- hexo
category:
- hexo
type: artitalk
cover: https://cdn.pixabay.com/photo/2020/03/13/14/06/dead-sea-4927978_1280.jpg
---

# hexo 链接到站内文章指定锚点

## 站内其他文章锚点链接

官方文档有一个 `post_path` ，用于获取文章路径，结合 markdown 内置的链接方式，即可实现锚点超链接，如下：

```markdown
post_path官方文档[hexo 安装]({% post_path 'hexo blog' %}#安装)
```

也可以使用 html a 标签实现，如下：

```markdown
<a href="{% post_path 'hexo blog' %}#安装">hexo 安装</a>
```

> 1. 如果文章中有图片，可能会出现锚点位置不准确问题，原因是图片加载成功之后会把内容高度撑开。
> 2. 如果如果有空格，需要把空格换成连字符 `-`。
>

## 站外文章锚点

直接使用全路径即可，如下：

```markdown
[hexo 引用文章](https://hexo.io/zh-cn/docs/tag-plugins#%E5%BC%95%E7%94%A8%E6%96%87%E7%AB%A0)
```
