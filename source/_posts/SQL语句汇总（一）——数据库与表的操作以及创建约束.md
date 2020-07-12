# SQL语句汇总（一）——数据库与表的操作以及创建约束

## 前言

此文旨在汇总从建立数据库到联接查询等绝大部分`SQL`语句。`SQL`语句虽不能说很多，但稍有时间不写就容易出错。博主希望通过此文来战胜自己的健忘。

`SQL`是 `Structured Query Language` 即“结构化查询语言”的简称，它是用来管理关系型数据库的。

其包括：

* 数据定义语言（`DDL`）
* 数据查询语言（`DQL`）
* 数据操作语言（`DML`）
* 数据控制语言（`DCL`）

## 对数据库的操作

### 创建数据库：

```mysql
CREATE DATABASE 数据库名;
```

数据库名命名规则：

- 第一个字符必须为下列之一：<font color="green">字母、下划线、#及@符号。</font>

- 后续字符由<font color="green">字母、十进制数、下划线、#、$及@符号</font>组成。

- 不能为<font color="green">RDBMS（数据库管理系统）</font>的保留关键字。

- 不允许<font color="green">空格及其他字符</font>。

本文以`SQLyog`软件为例，创建数据库如下：

```mysql
CREATE DATABASE test_sql
```

![](https://pic.downk.cc/item/5e8585d1504f4bcb04ce88cd.jpg)

此图为SQLyog的左侧目录栏，前三个为本地自带数据库。将SQL语句全部选中运行（F8）后，F5刷新目录栏，出现了我们创建的数据库。
### 查看数据库

查看当前有几个数据库可供连接。

```mysql
SHOW DATABASES;
```

### 连接数据库：

```mysql
USE test_sql;
```
### 查看当前连接数据库

```mysql
SELECT DATABASE();
```

### 删除数据库：

```mysql
DROP DATABASE test_sql;
```

## MySQL数据类型
接下来就开始创建表了，在创建表之前先列出几种常用的数据类型

### 整数数据类型：

- `INT` 大小：4字节

- `BIGINT `大小：8字节

### 浮点数据类型：

- `FLOAT `大小：4字节 精度：7位小数

- `DOUBLE `大小：8字节 精度：15位小数

### 字符串数据类型：
- `VARCHAR `范围：0-65535

### 日期数据类型：

- `DATE `大小：3字节 格式：YYYY

- `DATETIME `大小：8字节 格式：YYYY-MM-DD

## 对表的操作

数据库中对表的操作有：

* 创建表；
* 查看表的数量；
* 查看表的结构;
* 删除表；
* 复制表；
	* 复制表的结构及内容
	* 只复制表的结构
* 修改表；
	* 添加列
	* 更改列
	* 删除列

### 创建表：

```mysql
CREATE TABLE < 表名 > (< 列名 > < 列的数据类型 > [<列的约束>]);
```

如：

```mysql
CREATE TABLE t_student(
  student_name VARCHAR(10),
  student_birthday DATETIME,
  student_phone INT,
  student_score FLOAT
);
```

上表中我们创建了一张学生表，并定义了姓名、生日、电话、得分四列，列名后加上数据类型。其中要注意的是`VARCHAR`需要在括号内设置字符串的最大长度。

刷新之后就可以看到我们创建的数据库中多了一张表：

![](https://pic.downk.cc/item/5e858623504f4bcb04ceca12.jpg)

选择打开表可以直观的看到内容：

![](https://pic.downk.cc/item/5e858643504f4bcb04cee0cf.jpg)

之后我们可以通过SQL语句也可以通过手动为表添加内容。
### 查看表的数量

```mysql
SHOW TABLES;
```

### 查看表的结构

如果你想要知道一个表的结构，可以使用`DESCRIBE`命令；它显示表中每个列的信息：

```mysql
DESCRIBE t_student;
```

### 删除表：

```mysql
DROP TABLE t_student;
```

### 复制表：

#### 同时复制表的结构和内容

```mysql
CREATE TABLE copy_student
SELECT
  *
FROM t_student;
```

如此我们便复制了一张名为`copy_student`的表，它包括`t_student`表中的内容与结构。
<font color="red">注意：复制表的同时表的约束并不能复制过来。</font>

### 只复制表结构而不复制表内容：

```mysql
CREATE TABLE copy_student
SELECT
  *
FROM t_student
WHERE
  1 = 0;
```

只需在`WHERE`条件中加入一个永不为真的值即可。

### 修改表

#### 添加新列：

```mysql
ALTER TABLE t_student
ADD
  student_address VARCHAR(50);
```

![](https://pic.downk.cc/item/5e858643504f4bcb04cee0cf.jpg)

#### 更改列：

```mysql
ALTER TABLE t_student
CHANGE
  student_birthday student_age INT;
```

这里我们把学生生日列改为学生年龄列，`CHANGE`后第一个为旧列名，第二个为新列名。

![](https://pic.downk.cc/item/5e85867c504f4bcb04cf0e43.jpg)

#### 删除列：

```mysql
ALTER TABLE t_student
DROP COLUMN
  student_score;
```

![](https://pic.downk.cc/item/5e8586ed504f4bcb04cf65cf.jpg) 

## 数据库完整性

保证数据库的完整性是为了防止垃圾数据的产生，以免影响数据库的执行效率。这里简要说一些，因为毕竟不是理论类的文章，这里主要是整理汇总`SQL`语句。

**分类：**

1. 实体完整性（主键约束，唯一约束）
保证一行数据是有效的

2. 域完整性（非空约束，默认约束）
保证一列数据是有效的

3. 引用完整性（外键约束）
保证引用的编号是有效的

4. 用户自定义完整性
保证自定义规则

### 实体完整性--主键约束：

`PRIMARY KEY`

主键列不能为空也不能重复，通常加在表的id列中。

```mysql
CREATE TABLE t_student(
  student_id INT PRIMARY KEY,
  student_name VARCHAR(10),
  student_birthday DATETIME,
  student_phone INT,
  student_score FLOAT
);
``` 

### 实体完整性--唯一约束：

`UNIQUE`

唯一约束是指给定列的值必须唯一，与主键约束不同的是它可以为空。通常加在表中不能重复的信息中，如电话号码。

```mysql
CREATE TABLE t_student(
  student_id INT PRIMARY KEY,
  student_name VARCHAR(10),
  student_birthday DATETIME,
  student_phone INT UNIQUE,
  student_score FLOAT
);
```

### 域完整性--非空约束：

`NOT NULL`

非空约束可以加在诸如姓名等列上。

```mysql
CREATE TABLE t_student(
  student_id INT PRIMARY KEY,
  student_name VARCHAR(10) NOT NULL,
  student_birthday DATETIME,
  student_phone INT UNIQUE,
  student_score FLOAT
);
```

### 域完整性--默认约束：

设定默认值后，可以在添加此列时不指定值,数据库会自动填充设定的默认值。

`DEFAULT`

现给学生表加入性别列，默认值设为“男”，这样添加新的学生信息时如果没有填写具体的性别均会默认为男性：  

```mysql
CREATE TABLE t_student(
  student_id INT PRIMARY KEY,
  student_name VARCHAR(10) NOT NULL,
  student_sex VARCHAR(5) DEFAULT '男',
  student_birthday DATETIME,
  student_phone INT UNIQUE,
  student_score FLOAT
);
```

![](https://pic.downk.cc/item/5e858713504f4bcb04cf85ed.jpg)

### 引用完整性--外键约束：

外键约束是指在外键关联主键上强制加上一个约束，如果违反该约束，则不允许该条数据的修改。

#### 创建表时，同时创建约束

创建主表--班级表：

```mysql
CREATE TABLE t_class(
  class_id INT PRIMARY KEY,
  class_name VARCHAR(20) UNIQUE NOT NULL
);
```

创建从表--学生表，并设置外键约束：

```mysql
CREATE TABLE t_student(
  student_id INT PRIMARY KEY,
  s_c_id INT,
  student_name VARCHAR(10) NOT NULL,
  student_sex VARCHAR(5) DEFAULT '男',
  student_birthday DATETIME,
  student_phone INT UNIQUE,
  student_score FLOAT,
  CONSTRAINT fk_class_id FOREIGN KEY(s_c_id) REFERENCES t_class(class_id);
```

上面的第8行代码就是创建外键约束的方法，个人认为也是`SQL`语句中最难记的。

#### 已创建表后，追加外键约束

```mysql
ALTER table t_student
ADD
  CONSTRAINT fk_class_id FOREIGN KEY(s_c_id) REFERENCES t_class(class_id);
```

#### 删除外键约束

```mysql
ALTER TABLE person drop FOREIGN KEY fk_class_id;
```

## 结语

本篇博文先到这里，写起来才发现`SQL`比想像的多啊，所以初步打算分为三篇。第二篇讲数据增删改查这些操作、聚合函数及分组，第三篇讲`SQL`的子查询、组合查询以及连接查询。
