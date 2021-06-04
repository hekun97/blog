---
title: SQL笔记
date: 2021-06-03 11:02:14

type: artitalk
categories:
- SQL
tags:
- MySQL
- SQL
cover: https://cdn.pixabay.com/photo/2017/06/12/04/21/database-2394312_960_720.jpg
---

# 关系数据库概述

`SQL`是 `Structured Query Language` 即“结构化查询语言”的简称，它是用来管理关系型数据库的。

其包括：

1. 数据控制语言（`DCL`）(`Data Control Language`)

   是用来设置或更改数据库用户或角色权限的语句，包括（`grant`,`deny`,`revoke`等）语句。在默认状态下，只有`sysadmin`,`dbcreator`,`db_owner`或`db_securityadmin`等人员才有权力执行`DCL`

2. 数据定义语言（`DDL`）(`Data Definition Language`)

   `DDL`允许用户定义数据，也就是创建表、删除表、修改表结构这些操作。通常，`DDL`由数据库管理员执行。

3. 数据查询语言（`DQL`）(`Data Query Language`)

   `DQL`允许用户查询数据，这也是通常最频繁的数据库日常操作

4. 数据操作语言（`DML`）(`Data Manipulation Language`)

   `DML`为用户提供添加、删除、更新数据的能力，这些是应用程序对数据库的日常操作。
