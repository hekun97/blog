---
title: SQL语句汇总
date: 2020-10-14 10:47:49
type: artitalk
categories:
- SQL
tags:
- MySQL
- SQL
cover: https://cdn.pixabay.com/photo/2017/06/12/04/21/database-2394312_960_720.jpg
---

# 数据库与表的操作以及创建约束（一）

## 前言

此文旨在汇总从建立数据库到联接查询等绝大部分`SQL`语句。`SQL`语句虽不能说很多，但稍有时间不写就容易出错。

`SQL`是 `Structured Query Language` 即“结构化查询语言”的简称，它是用来管理关系型数据库的。

其包括：

1. 数据控制语言（`DCL`）(`Data Control Language`)【使用最少】

   是用来设置或更改数据库用户或角色权限的语句，包括（`grant`,`deny`,`revoke`等）语句。在默认状态下，只有`sysadmin`,`dbcreator`,`db_owner`或`db_securityadmin`等人员才有权力执行`DCL`

2. 数据定义语言（`DDL`）(`Data Definition Language`)

   `DDL`允许用户定义数据，也就是创建表、删除表、修改表结构这些操作。通常，`DDL`由数据库管理员执行。

3. 数据查询语言（`DQL`）(`Data Query Language`)【使用最多，其中的联合查询内容最重要】

   `DQL`允许用户查询数据，这也是通常最频繁的数据库日常操作

4. 数据操作语言（`DML`）(`Data Manipulation Language`)

   `DML`为用户提供添加、删除、更新数据的能力，这些是应用程序对数据库的日常操作。

## 语法特点

SQL语言关键字不区分大小写！！！但是，针对不同的数据库，对于表名和列名，有的数据库区分大小写，有的数据库不区分大小写。同一个数据库，有的在`Linux`上区分大小写，有的在`Windows`上不区分大小写。

所以，本教程约定：SQL关键字总是大写，以示突出，表名和列名均使用小写。

## 对数据库的操作

### 创建数据库

```sql
CREATE DATABASE 数据库名;
```

数据库名命名规则：

- 第一个字符必须为下列之一：<font color="green">字母、下划线、#及@符号。</font>

- 后续字符由<font color="green">字母、十进制数、下划线、#、$及@符号</font>组成。

- 不能为<font color="green">RDBMS（关系型数据库管理系统）</font>的保留关键字。

- 不允许<font color="green">空格及其他字符</font>。

本文以`SQLyog`软件为例，创建数据库如下：

```sql
CREATE DATABASE test_sql;
```

![](https://pic.downk.cc/item/5e8585d1504f4bcb04ce88cd.jpg)

此图为`SQLyog`的左侧目录栏，前三个为本地自带数据库。将`SQL`语句全部选中运行（`F8`）后，`F5`刷新目录栏，出现了我们创建的数据库。

### 查看数据库

查看当前有几个数据库可供连接。

```sql
SHOW DATABASES;
```

### 连接数据库

```sql
USE test_sql;
```

### 查看当前连接数据库

```sql
SELECT DATABASE();
```

### 删除数据库

```sql
DROP DATABASE test_sql;
```

## MySQL数据类型

接下来就开始创建表了，在创建表之前先列出几种常用的数据类型

### 整数数据类型

| **名称** | **类型** | **说明**                          |
| -------- | -------- | --------------------------------- |
| `INT`    | 整型     | 4字节整数类型，范围约`+/-21亿`    |
| `BIGINT` | 长整型   | 8字节整数类型，范围约`+/-922亿亿` |

### 浮点数据类型

| **名称** | **类型** | **说明**                        |
| -------- | -------- | ------------------------------- |
| `FLOAT`或`REAL`                                             | 浮点型         | 4字节浮点数，范围约`+/-1038`，精度7位小数                    |
| `DOUBLE`                                                     | 浮点型         | 8字节浮点数，范围约`+/-10308`，精度15位小数                  |

> 因为不精准，因此不能使用在货币这类严格要求准确的位置，通常我们更经常使用整数类型用来计算货币。

### 高精度小数

| **名称** | **类型** | **说明**                        |
| -------- | -------- | ------------------------------- |
| `DECIMAL(M,N)`                                               | 高精度小数     | 由用户指定精度的小数，例如，`DECIMAL(20,10)`表示一共20位，其中小数10位，通常用于财务计算 |

### 字符串数据类型

| **名称** | **类型** | **说明**                        |
| -------- | -------- | ------------------------------- |
| `CHAR(N)`                                                    | 定长字符串     | 存储指定长度的字符串，例如，`CHAR(100)`总是存储100个字符的字符串 |
| `VARCHAR(N)`                                                 | 变长字符串     | 存储可变长度的字符串，例如，`VARCHAR(100)`可以存储0~100个字符的字符串 |

### 布尔类型

| **名称** | **类型** | **说明**                        |
| -------- | -------- | ------------------------------- |
| `BOOLEAN`                                                    | 布尔类型       | 存储True或者False                                            |

### 日期数据类型

| **名称** | **类型** | **说明**                        |
| -------- | -------- | ------------------------------- |
| `DATE`                                                       | 日期类型       | 存储日期，例如，2018-06-22                                   |
| `TIME`                                                       | 时间类型       | 存储时间，例如，12:20:59                                     |
| `DATETIME`                                                   | 日期和时间类型 | 存储日期+时间，例如，2018-06-22 12:20:59                     |

## 对表的操作(`DDL`)

也就是前面说的 数据定义语言（`DDL`）(`Data Definition Language`)。

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

### 创建表

```sql
CREATE TABLE < 表名 > (< 列名 > < 列的数据类型 > [<列的约束>]);
```

如：

```sql
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

```sql
SHOW TABLES;
```

### 查看表的结构

如果你想要知道一个表的结构，可以使用`DESCRIBE`命令；它显示表中每个列的信息：

```sql
DESCRIBE t_student;
```

### 删除表

```sql
DROP TABLE t_student;
```

### 复制表

#### 同时复制表的结构和内容

```sql
CREATE TABLE copy_student
SELECT
  *
FROM t_student;
```

如此我们便复制了一张名为`copy_student`的表，它包括`t_student`表中的内容与结构。

> <font color="red">注意：复制表的同时表的约束并不能复制过来。</font>

### 只复制表结构而不复制表内容

```sql
CREATE TABLE copy_student
SELECT
  *
FROM t_student
WHERE
  1 = 0;
```

只需在`WHERE`条件中加入一个永不为真的值即可。

### 修改表

#### 添加新列

```sql
ALTER TABLE t_student
ADD
  student_address VARCHAR(50);
```

![](https://pic.downk.cc/item/5e858643504f4bcb04cee0cf.jpg)

#### 更改列

```sql
ALTER TABLE t_student
CHANGE
  student_birthday student_age INT;
```

这里我们把学生生日列（`student_birthday`）改为学生年龄列（`student_age`），`CHANGE`后第一个为旧列名，第二个为新列名。

![](https://pic.downk.cc/item/5e85867c504f4bcb04cf0e43.jpg)

#### 删除列

```sql
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

### 实体完整性--主键约束

关键字：`PRIMARY KEY`

主键列不能为空也不能重复，通常加在表的id列中。

```sql
CREATE TABLE t_student(
  student_id INT PRIMARY KEY,
  student_name VARCHAR(10),
  student_birthday DATETIME,
  student_phone INT,
  student_score FLOAT
);
```

### 实体完整性--唯一约束

关键字：`UNIQUE`

唯一约束是指给定列的值必须唯一，与主键约束不同的是它可以为空。通常加在表中不能重复的信息中，如电话号码。

```sql
CREATE TABLE t_student(
  student_id INT PRIMARY KEY,
  student_name VARCHAR(10),
  student_birthday DATETIME,
  student_phone INT UNIQUE,
  student_score FLOAT
);
```

### 域完整性--非空约束

关键字：`NOT NULL`

非空约束可以加在诸如姓名等列上。

```sql
CREATE TABLE t_student(
  student_id INT PRIMARY KEY,
  student_name VARCHAR(10) NOT NULL,
  student_birthday DATETIME,
  student_phone INT UNIQUE,
  student_score FLOAT
);
```

### 域完整性--默认约束

关键字：`DEFAULT`

设定默认值后，可以在添加此列时不指定值,数据库会自动填充设定的默认值。

现给学生表加入性别列，默认值设为“男”，这样添加新的学生信息时如果没有填写具体的性别均会默认为男性：  

```sql
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

### 引用完整性--外键约束

外键约束是指在外键关联主键上强制加上一个约束，如果违反该约束，则不允许该条数据的修改。可以通过两种方式创建，第一种是在创建表的同时创建约束，第二种是已经创建表后，再来创建约束。

#### 1.创建表时，同时创建约束

创建主表--班级表：

```sql
CREATE TABLE t_class(
  class_id INT PRIMARY KEY,
  class_name VARCHAR(20) UNIQUE NOT NULL
);
```

创建从表--学生表，并设置外键约束：

```sql
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

#### 2.已创建表后，追加外键约束

```sql
ALTER table t_student
ADD
  CONSTRAINT fk_class_id FOREIGN KEY(s_c_id) REFERENCES t_class(class_id);
```

这里值得注意的是，在追加约束时，如果我们已创建好的表中有不符合约束条件的数据，那么将会报错约束冲突，可以通过添加语句`WITH NOCHECK`解决，这样就只会对后面新加的数据进行校验，而前面已有的数据忽略不管。

上述语句可修改为如下：

```sql
ALTER table t_student WITH NOCHECK
ADD
  CONSTRAINT fk_class_id FOREIGN KEY(s_c_id) REFERENCES t_class(class_id);
```

由于外键约束会降低数据库的性能，大部分互联网应用程序为了追求速度，并不设置外键约束，而是仅靠应用程序自身来保证逻辑的正确性。这种情况下，`s_c_id`仅仅是一个普通的列，只是它起到了外键的作用而已。

#### 3.删除外键约束

```sql
ALTER TABLE t_student drop FOREIGN KEY fk_class_id;
```

> 注意：删除外键约束并没有删除外键这一列，只是去除外键将这一列（`s_c_id`）变为一个普通的列。删除列是通过`DROP COLUMN ...`实现的。

# 数据修改、数据查询（二）

## 创建表

首先创建一张表如下，创建表的方法介绍过了，这里就不再赘述。

![](https://pic.downk.cc/item/5e858868504f4bcb04d08c36.jpg)

## 数据修改(DML)

* 添加新数据
* 更改数据
* 删除数据（行）

### 添加新数据：

```sql
INSERT INTO < 表名 > 
  (< 列名列表 >)
VALUES
  (< 值列表 >)
```

**如：**

```sql
INSERT INTO t_student (
    student_id,
    student_name,
    student_age,
    student_sex
  )
VALUES
  (1, '大毛', 18, '男');
```

![](https://pic.downk.cc/item/5e85889d504f4bcb04d0b802.jpg)
其中列名可以省略，省略之后要求插入的值必须与列一一对应：

```sql
INSERT INTO t_student
VALUES
  (2, '王二', 20, '男');
```

![](https://pic.downk.cc/item/5e8588f6504f4bcb04d10c43.jpg)

**多行数据添加：**

```sql
INSERT INTO t_student
VALUES
  (3, '张三', 22, '男'),
  (4, '李四', 17, '女'),
  (5, '王五', 23, '男');
```

![](https://pic.downk.cc/item/5e8588f6504f4bcb04d10c43.jpg)

### 更改数据：

```sql
UPDATE 表名
SET
  列1 = 新值1,
  列2 = 新值2
WHERE
  过滤条件
```

假如要修改李四的年龄为21岁

```sql
UPDATE t_student
SET
  student_age = 21
WHERE
  student_name = '李四';
```

![](https://pic.downk.cc/item/5e858953504f4bcb04d15f04.jpg)

<font color="red">注：修改多个列的值时用逗号隔开。要想设置某一列的值为空，只需让`<列名>=NULL` 即可。`WHERE`表示过滤条件。</font>

###  删除数据（行）

```sql
DELETE FROM 表名
WHERE
  过滤条件
```

现要删除20到22岁的学生信息：

```sql
DELETE FROM t_student
WHERE
  student_age BETWEEN 20
  AND 22;
```

![](https://pic.downk.cc/item/5e8589b5504f4bcb04d1aa51.jpg)

`WHERE`的判断条件之后会更详细的介绍。

删除除了`DELETE`还有一种方法`TRUNCATE`，写法：

```sql
TRUNCATE TABLE 表名;
```

**二者区别在于：**
`DELETE`会记录日志，意味着删除后的数据还可以恢复，但是效率低。`TRUNCATE`不会记录日志，删除后的数据不能恢复，但是效率高。需要注意的是，`TRUNCATE`不能用于有外键约束引用的表。

## 查询操作(DQL)

### 基本查询

要查询数据库表的数据，我们使用如下的SQL语句：

```sql
SELECT * FROM <表名>;
```

要查询`t_students`表的所有行，我们用如下SQL语句：

```sql
SELECT * FROM t_students;
```

其中`SELECT`是关键字，表示将要执行一个查询，`*`表示“所有列（字段）”，`FROM`表示将要从哪个表查询，本例中是`t_students`表。

该SQL将查询出`t_students`表的所有数据。

再来看一条查询语句。

```sql
SELECT 100+200;
```

上述查询会直接计算出表达式的结果`300`。虽然`SELECT`可以用作计算，但它并不是SQL的强项。但是，不带`FROM`子句的`SELECT`语句有一个有用的用途，就是用来判断当前到数据库的连接是否有效。许多检测工具会执行一条`SELECT 1;`来测试数据库连接。

```sql
SELECT 1;
```

这也是我们在使用大部分数据库连接软件测试连接时使用的语句。

![image-20220908003854140](https://cdn.jsdelivr.net/gh/hekun97/picture/Git_notes/image-20220908003854140.png)

### 条件查询

也就是执行选择操作，关键字是：`WHERE`

指定哪些行出现在结果中

* 单条件选择
* 多条件选择

1.**单条件选择标准结构：**

```sql
SELECT
  列1,
  列2
FROM 表名
WHERE
  列3 = 值
```

关系运算符包括：`>  >=  <  <=  =  !=`

2.**多条件选择标准结构：**

```sql
SELECT
  列A,
  列B
FROM 表
WHERE
  条件1 
    AND 或者 OR 或者 NOT
  条件2
```

其中`AND`表示并且，`OR`表示或者，`NOT`表示排序符合此条件的记录。

如：

```sql
select 
  s_id , 
  s_name 
from students 
where 
  s_id < 8 
    and 
  s_sex = '男';
```

如果不加括号，条件运算按照`NOT`、`AND`、`OR`的优先级进行，即`NOT`优先级最高，其次是`AND`，最后是`OR`。加上括号可以改变优先级。

```sql
select 
  s_id , 
  s_name 
from students 
where 
  (s_id < 8 or s_age>23)
     
  s_sex = '男';
```

这里就会先执行`or`，然后再执行后面的`and`，我们在实际使用中遇到多个条件运算符时，尽量加上括号，方便更好的阅读。

#### 选择范围

关键字：`BETWEEN`

`BETWEEN`后的值为从下限到上限。

如：

```sql
SELECT
  *
FROM t_student
WHERE
  student_age BETWEEN 20
  AND 23;
```

![](https://pic.downk.cc/item/5e858c9c504f4bcb04d3bc84.jpg)

### 投影查询

用于指定查询结果中能显示哪些列。

```sql
SELECT 列1, 列2 FROM 表名
```

多个列中间用逗号隔开，如果选择所有列可以用`*`号简写。

还是此表：

![](https://pic.downk.cc/item/5e858953504f4bcb04d15f04.jpg)

现在只想要查看姓名和年龄列：

```sql
SELECT student_name, student_age FROM t_student;
```

![](https://pic.downk.cc/item/5e858b21504f4bcb04d2a9a9.jpg)

#### 组合查询（CONCAT）

当我们需要将两列的数据放在一列（字段）中时，可以使用`CONCAT`来进行组合查询。

注意这里不是把其他列删除了，而是只显示我们想看见的部分。

```sql
SELECT CONCAT(student_name, '——', student_age) '组合值' FROM t_student;
```

`CONCAT`中除了需要的列（字段），还可以添加字符将列与列之间用想要的符号连接起来，这里使用的是字符`'--'`  。

![](https://pic.downk.cc/item/5e858b6d504f4bcb04d2e262.jpg)

#### 排除重复（DISTINCT）

现给原表加入一班级列`student_class`。

![](https://pic.downk.cc/item/5e858baa504f4bcb04d30d15.jpg)

查询班级列得到：

```sql
SELECT student_class FROM t_student;
```

![](https://pic.downk.cc/item/5e858bba504f4bcb04d31704.jpg)

但是我们只想查看具体有哪些班级，这里就需要用到去重，也就是`DISTINCT`。

```sql
SELECT DISTINCT student_class FROM t_student;
```

![](https://pic.downk.cc/item/5e858bd7504f4bcb04d32e9b.jpg)

#### 定义集合（IN或NOT IN）

现在想查看年龄为17、20、23的学生信息： 

```sql
SELECT
  *
FROM t_student
WHERE
  student_age IN (17, 20, 23);
```

![](https://pic.downk.cc/item/5e858cca504f4bcb04d3d9f6.jpg)
反之`NOT IN`就是选择不包括在集合里的学生信息。

#### 模糊查询（LIKE）

为了更好的解释模糊查询，这里重新建张表`tt_student`。

![](https://pic.downk.cc/item/5e858ce6504f4bcb04d3ea6c.jpg)

这里姓王的兄弟们躺枪...别介意。

**首先先说下占位符与通配符：**

占位符 `_`，表示任何单个字符。

通配符 `%`，表示包含零或多个字符。

下面就来用模糊查询逐一选中我们想要的行。

**名字只有两个字的：**

```sql
SELECT
  *
FROM t_student
WHERE
  student_name LIKE '__';
```

这里可能看不清，引号里实际是两个占位符。

![](https://pic.downk.cc/item/5e858d07504f4bcb04d400b9.jpg)

**所有姓王的：**

```sql
SELECT
  *
FROM t_student
WHERE
  student_name LIKE '王%';
```

![](https://pic.downk.cc/item/5e858d22504f4bcb04d411df.jpg)

**最后一个字是“王”的：**

```sql
SELECT
  *
FROM t_student
WHERE
  student_name LIKE '%王';
```

![](https://pic.downk.cc/item/5e858d59504f4bcb04d43c32.jpg)
**只要是名字中有“王”字的：**

```sql
SELECT
  *
FROM t_student
WHERE
  student_name LIKE '%王%';
```

![](https://pic.downk.cc/item/5e858d69504f4bcb04d448d9.jpg)
这下模糊查询就很明白了吧，当然还有其他组合，大家可以自己尝试。

> `LIKE`是模糊查询这些数据，`NOT LIKE`刚好相反，也就是不需要这些模糊查询出来的数据。

#### 处理空值数据

判断条件不能用`列名=NULL`，而是要用`IS NULL`或`IS NOT NULL`。

**标准写法：**

```sql
SELECT
  *
FROM t_student
WHERE
  student_age IS NULL;
```

### 排序查询（ORDER BY）

指定查询的结果以什么样的顺序显示。

使用`ORDER BY`时，列名上指定`ASC`或`DESC`。`ASC`表示正序，`DESC`表示倒序。如果不指定则默认为正序。

**按年龄排：**

```sql
SELECT
  *
FROM t_student
ORDER BY
  student_age ASC;
```

![](https://pic.downk.cc/item/5e858dc8504f4bcb04d4a68a.jpg)

```sql
SELECT
  *
FROM t_student
ORDER BY
  student_age DESC;
```

![](https://pic.downk.cc/item/5e858dc8504f4bcb04d4a68a.jpg)



### 分页查询

使用SELECT查询时，如果结果集数据量很大，比如几万行数据，放在一个页面显示的话数据量太大，且对数据库的压力也大，不如分页显示，每次显示100条，也每次从数据库拿100条数据。

要实现分页功能，实际上就是从结果集中显示第1~100条记录作为第1页，显示第101~200条记录作为第2页，以此类推。

因此，分页实际上就是从结果集中“截取”出第M~N条记录。这个查询可以通过`LIMIT N OFFSET M`子句实现。

其中：

LIMIT 为返回限定行数的查询，

`LIMIT N` 表示从第一行开始返回 `N` 行结果;

`LIMIT N OFFSET M`中的`OFFSET M`为偏移量，就记录集索引`M`之前的数据不管，从索引`M`开始返回`N`行结果。

#### 实操步骤

这里使用上面模糊操作中的表`tt_student`训练。

1. 我们先把所有学生按照年龄从高到低进行排序：

```sql
-- 按student_age从高到低
SELECT * FROM tt_students ORDER BY student_age DESC;
```

2. 把结果集分页，每页4条记录。要获取第1页的记录，可以使用`LIMIT 4 OFFSET 0`：

```sql
-- 查询第1页
SELECT * 
FROM tt_students 
ORDER BY student_age DESC 
LIMIT 4 OFFSET 0;
-- 或者使用下面语句
SELECT * 
FROM t_students 
ORDER BY student_age DESC 
LIMIT 4 ;
```

上述查询`LIMIT 4 OFFSET 0`表示，对结果集从0号记录开始，最多取4条。注意SQL记录集的索引从0开始。

3. 如果要查询第2页，那么我们只需要“跳过”头4条记录，也就是对结果集从索引为4号的记录开始查询，把`OFFSET`设定为4。

```sql
-- 查询第2页
SELECT * 
FROM t_students 
ORDER BY student_age DESC 
LIMIT 4 OFFSET 4;
-- 或者使用下面语句
SELECT * 
FROM t_students 
ORDER BY student_age DESC 
LIMIT 4 4;
```

由于表`tt_students`只有6条数据，因此第2页只有2条记录，因此最终的结果集按实际数量2显示，`LIMIT 4表示的意思是“最多4条记录”。

如果后续还存在别的页，那么查询第3页的时候，`OFFSET`应该设定为8；

查询第4页的时候，`OFFSET`应该设定为12。

可见，分页查询的关键在于，首先要确定每页需要显示的结果数量`pageSize`（这里是4），然后根据当前页的索引`pageIndex`（从1开始），确定`LIMIT`和`OFFSET`应该设定的值：

- `LIMIT = pageSize`；
- `OFFSET = pageSize * (pageIndex - 1)`。

这样就能正确查询出第N页的记录集。

原本表`tt_student`一共就6条记录，但我们把`OFFSET`设置为20，会得到什么结果呢？

```sql
-- OFFSET设定为20
SELECT * 
FROM t_students 
ORDER BY student_age DESC 
LIMIT 4 OFFSET 20;
```

`OFFSET`超过了查询的最大数量并不会报错，而是得到一个空的结果集。

#### 注意

`OFFSET`是可选的，如果只写`LIMIT 15`，那么相当于`LIMIT 15 OFFSET 0`。

在MySQL中，`LIMIT 15 OFFSET 30`还可以简写成`LIMIT 30, 15`。

使用`LIMIT <M> OFFSET <N>`分页时，随着`N`越来越大，查询效率也会越来越低。

#### 小结

使用`LIMIT <M> OFFSET <N>`可以对结果集进行分页，每次查询返回结果集的一部分；

分页查询需要先确定每页的数量和当前页数，然后确定`LIMIT`和`OFFSET`的值。

#### 思考

在分页查询之前，如何计算一共有几页（`totalPage`）？

这部分可以通过后面学习聚合查询时获取总的数据量（`totalSize`）,然后可以整除的情况下`totalPage = totalSize/pageSize`，不能整除还有余数的情况下，`totalPage = totalSize/pageSize+1`，这里是否整除可以判断`totalSize%pageSize==0`是否为`true`来确定。

## 基本查询SQL的执行顺序（极重要）

1.    执行`FROM `
2.    `WHERE`条件过滤 
3.    `SELECT`投影 
4.    `ORDER BY`排序

还有聚合查询和多表查询，我们在第三节学习。

# 聚合函数、分组、子查询及组合查询（三）

## 聚合函数：

`SQL`中提供的聚合函数可以用来统计、求和、求最值等等。

分类：

- `COUNT`：统计行数量

- `SUM`：获取单个列的合计值

- `AVG`：计算某个列的平均值

- `MAX`：计算列的最大值

- `MIN`：计算列的最小值

 

首先，创建数据表如下：

![](https://pic.downk.cc/item/5e858f04504f4bcb04d5769e.jpg)


### 执行列、行计数（count）

标准格式

```sql
SELECT
  COUNT(< 计数规范 >)
FROM < 表名 >
```

其中，计数规范包括：

- `*`:计数所有选择的行，包括NULL值；

- `ALL `列名：计数指定列的所有非空值行，如果不写，默认为`ALL`；

- `DISTINCT` 列名：计数指定列的唯一非空值行。

**例，计算班里共有多少学生：**

```sql
SELECT
  COUNT(*)
FROM t_student;
```

![](https://pic.downk.cc/item/5e858f75504f4bcb04d5bfd2.jpg)

**也可加入筛选条件，如求女学生数目：**

```sql
SELECT
  COUNT(*)
FROM t_student
WHERE
  student_sex = '女';
```

![](https://pic.downk.cc/item/5e858fba504f4bcb04d5f5c2.jpg)

**如果要计算班级数目，就需要用到`DISTINCT`：**

```sql
SELECT
  COUNT(DISTINCT student_class)
FROM t_student;
```

![](https://pic.downk.cc/item/5e858ffe504f4bcb04d637e9.jpg)

`DISTINCT`即去重，如果不加`DISTINCT`则结果为表行数——5。

 

### 返回列合计值（SUM）

<font color="red">注：`sum`只有`ALL`与`DISTINCT`两种计数规范，无`*`。</font>

**计算学生年龄之和：**

```sql
SELECT
  SUM(student_age)
FROM t_student;
```

![](https://pic.downk.cc/item/5e8591b6504f4bcb04d765aa.jpg)


### 返回列平均值（AVG）

**计算学生平均年龄：**

```sql
SELECT
  AVG(student_age)
FROM t_student;
```

![](https://pic.downk.cc/item/5e8591db504f4bcb04d782a2.jpg)

 

### 返回最大值/最小值（MAX/MIN）

**求年龄最大的学生信息（最小值同理）**

```sql
SELECT
  MAX(student_age)
FROM t_student;
```

![](https://pic.downk.cc/item/5e8591f9504f4bcb04d797a4.jpg)

<font color="red">注：这里只能求出最大年龄，要想显示年龄最大的学生全部信息，需要用到之后的子查询。</font>

 

## 数据分组（GROUP BY)

`SQL`中数据可以按列名分组，搭配聚合函数十分实用。

例，统计每个班的人数:

```sql
SELECT
  student_class,
  COUNT(ALL student_name) AS 总人数
FROM t_student
GROUP BY
  (student_class);
```

`AS`为定义别名，别名的使用在组合及联接查询时会有很好的效果，之后再说。

![](https://pic.downk.cc/item/5e85922e504f4bcb04d7bdd7.jpg)

分组中也可以加入筛选条件`WHERE`，不过这里一定要注意的是，执行顺序为：

1. `WHERE`过滤
2. 分组
3. 聚合函数。

<font color="red">牢记！</font>

**统计每个班上20岁以上的学生人数：**

```sql
SELECT
  student_class,
  COUNT(student_name) AS 总人数
FROM t_student
WHERE
  student_age > 20
GROUP BY
  (student_class);
```

![](https://pic.downk.cc/item/5e859258504f4bcb04d7de5a.jpg)


### HAVING过滤条件

之前说了分组操作、聚合函数、`WHERE`过滤的执行顺序，那如果我们希望在聚合之后执行过滤条件怎么办？

例，我们想查询平均年龄在20岁以上的班级

能用下面的语句吗？

```sql
SELECT
  student_class,
  AVG(student_age)
FROM t_student
WHERE
  AVG(student_age) > 20
GROUP BY
  student_class;
```

结果会出错。正因为聚合函数在`WHERE`之后执行，所以这里在`WHERE`判断条件里加入聚合函数是做不到的。

这里使用`HAIVING`即可完成：

```sql
SELECT
  student_class,
  AVG(student_age) AS 平均年龄
FROM t_student
GROUP BY
  (student_class)
HAVING
  AVG(student_age) > 20;
```

![](https://pic.downk.cc/item/5e859290504f4bcb04d808b0.jpg)


这里再啰嗦一句

**`SQL`的执行顺序：**

- 第一步：执行`FROM`

- 第二步：`WHERE`条件过滤

- 第三步：`GROUP BY`分组

- 第四步：执行`SELECT`投影列

- 第五步：`HAVING`条件过滤

- 第六步：执行`ORDER BY` 排序

 

## 子查询：

为什么要子查询？

现有一数据表如下：

![](https://pic.downk.cc/item/5e8592b7504f4bcb04d825d3.jpg)
根据之前的知识我们可以查出每门科目的最高分，但是要想查出取得最高分的学生信息就做不到了。这时就需要用到子查询来取得完整的信息。

 

什么是子查询？子查询就是嵌套在主查询中的查询。

子查询可以嵌套在主查询中所有位置，包括`SELECT`、`FROM`、`WHERE`、`GROUP BY`、`HAVING`、`ORDER BY`。

但并不是每个位置嵌套子查询都是有意义并实用的，这里对几种有实际意义的子查询进行说明。

现有表两张：一张学生表、一张班表。`id`相关联

![](https://pic.downk.cc/item/5e8592df504f4bcb04d84202.jpg)

![](https://pic.downk.cc/item/5e8592eb504f4bcb04d84c01.jpg)

 

### 在SELECT中嵌套

学生信息和班级名称位于不同的表中，要在同一张表中查出学生的学号、姓名、班级名称：

```sql
SELECT
  s.student_id,
  s.student_name,(
    SELECT
      class_name
    FROM t_class c
    WHERE
      c.class_id = s.class_id
  )
FROM t_student s
GROUP BY
  s.student_id;
```


首先这条`SQL`语句用到了别名，写法为在`FORM`的表名后加上某个字符比如`FROM t_student s，`这样在之后调用`t_student`的某一列时就可以用`s.student_id来`强调此列来源于对应别名的那张表。

别名在子查询及联接查询中的应用有着很好效果，当两张表有相同列名或者为了加强可读性，给表加上不同的别名，就能很好的区分哪些列属于哪张表。

还有种情况就是在子查询或联接查询时，主查询及子查询均为对同一张表进行操作，为主、子查询中的表加上不同的别名能够很好的区分哪些列的操作是在主查询中进行的，哪些列的操作是在子查询中进行的，下文会有实例说明。

接下来回到上面的`SQL`语句中，可以看出本条子查询的嵌套是在`SELECT`位置（括号括起来的部分），它与学号、学生姓名以逗号分隔开并列在`SELECT`位置，也就是说它是我们想要查出的一列，

子查询中查出的是，班级表中的班级`id`与学生表中的班级`id`相同的行，注意 `WHERE c.class_id=s.class_id `这里就是别名用法的一个很好的体现，区分开了两张表中同样列名的列。

结果：

![](https://pic.downk.cc/item/5e859338504f4bcb04d88208.jpg)
最后的`GROUP BY`可以理解为对重复行的去重，如果不加：

![](https://pic.downk.cc/item/5e859345504f4bcb04d88abb.jpg)


### 在WHERE中嵌套

现要查出C语言成绩最高的学生的信息：

```sql
SELECT
  *
FROM t_student
WHERE
  student_subject = 'C语言'
  AND student_score >= ALL (
    SELECT
      student_score
    FROM t_student
    WHERE
      student_subject = 'C语言'
  );
```

结果：

![](https://pic.downk.cc/item/5e85936a504f4bcb04d8a527.jpg)
这里出现了一个`ALL`，其为子查询运算符

分类：

- `ALL`运算符
  和子查询的结果逐一比较，必须全部满足时表达式的值才为真。

- `ANY`运算符
  和子查询的结果逐一比较，其中一条记录满足条件则表达式的值就为真。

- `EXISTS/NOT EXISTS`运算符
  `EXISTS`判断子查询是否存在数据，如果存在则表达式为真，反之为假。`NOT EXISTS`相反。

在子查询或相关查询中，要求出某个列的最大值，通常都是用`ALL`来比较，大意为比其他行都要大的值即为最大值。

要查出C语言成绩比李四高的学生的信息：

```sql
SELECT
  *
FROM t_student
WHERE
  student_subject = 'C语言'
  AND student_score >(
    SELECT
      student_score
    FROM t_student
    WHERE
      student_name = '李四'
      AND student_subject = 'C语言'
  );
```

![](https://pic.downk.cc/item/5e8593dd504f4bcb04d8f4ac.jpg)

通过上面两例，应该可以明白子查询在`WHERE`中嵌套的作用。通过子查询中返回的列值来作为比较对象，在`WHERE`中运用不同的比较运算符来对其进行比较，从而得到结果。

现在我们回到最开始的问题，怎么查出每门课最高成绩的学生的信息：

```sql
SELECT
  *
FROM t_student s1
WHERE
  s1.student_score >= ALL(
    SELECT
      s2.student_score
    FROM t_student s2
    WHERE
      s1.student_subject = s2.student_subject
  );
```

这里就是上文提到的别名的第二种用法，主、子查询对同一张表操作，区分开位于内外表中相同的列名。

结果：

![](https://pic.downk.cc/item/5e8593f4504f4bcb04d90360.jpg)


### 子查询的分类

- 相关子查询
  * 执行依赖于外部查询的数据。
  * 外部查询返回一行，子查询就执行一次。

- 非相关子查询
  * 独立于外部查询的子查询。
  * 子查询总共执行一次，执行完毕后后将值传递给外部查询。

 

上文提到的例子中，第一个例子求学生对应班级名的即为相关子查询，其中`WHERE c.class_id=s.class_id `即为相关条件。其他的例子均只对一张表进行操作，为非相关子查询。

需要注意的是相关子查询主查询执行一回，子查询就执行一回，十分耗费时间，尤其是当数据多的时候。

 

## 组合查询

通过`UNION`运算符来将两张表纵向联接，基本方式为：

```sql
SELECT
  列1,
  列2
FROM 表1
UNION
SELECT
  列3,
  列4
FROM 表2;

```

`UNION ALL`为保留重复行：

```sql
SELECT
  列1,
  列2
FROM 表1
UNION ALL
SELECT
  列3,
  列4
FROM 表2;
```

组合查询并不是太实用，所以这里只是简单提一下，不举出例子了。


## 总结

上文说过相关子查询不推荐使用，组合查询又用的少之又少，那需要关联的多张表我们怎么做？

这就是下一篇博文要详细说明的`SQL`的重点表联接、联接查询。而此篇博文目的是为了对嵌套查询、别名的用法等等打下基础，毕竟只是写法变了，思路还是相似的。

#  表联接与联接查询（四）

## 前言

既然是最后一篇那就不能只列出些干枯的标准语句，更何况表联接也是`SQL`中较难的部分，所以此次搭配题目来详细阐述表联接。


## 表联接

前面说到相关子查询效率低下，那我们怎么能将不同表的信息一起查询出来呢？这就需要用到表联接。

和之前的`UNION`组合查询不同，`UNION`是将不同的表组合起来，也就是纵向联接，说白了就是竖着拼起来。

而表联接是通过笛卡尔乘积将表进行横向联接，所谓的通过笛卡尔乘积简单说就是两表的行依次相联再相加。要想更详细的理解可以百度下，毕竟本文主要是汇总`SQL`语句。

现在有如下两张表：

![](https://pic.downk.cc/item/5e859487504f4bcb04d967d5.jpg)
![](https://pic.downk.cc/item/5e859494504f4bcb04d97383.jpg)

这是当初老师布置的一份作业，我偷个懒就不改数据了。不过把这些真神级人物的大名贴出来做“实验”总觉得心里有很虚，更何况大部分都是IT业的。如有什么不敬我先道个歉，别跟我一般见识。

好了，扯远了。怎么联接这两张表呢？标准写法：

```sql
SELECT
  *
FROM t_student
JOIN t_class;
```

结果这里只截一小部分图，因为笛卡尔乘积后的行数等于两张表的行数乘积，实在太多了。

![](https://pic.downk.cc/item/5e8594ae504f4bcb04d984a7.jpg)

这里就可以理解表联接的原理了，依次相连再相加。当然其中很多是无效行，为了去除无效的行我们就要用到外键来进行约束。学生表中的`_fk`与班级表中的`_infor`相关联：

```sql
SELECT
  *
FROM t_student s
JOIN t_class c ON s._fk = c._infor;
```

结果：

![](https://pic.downk.cc/item/5e8594c2504f4bcb04d9917b.jpg)

这里通过外键的匹配我们就得到了一张完美的联接之后的表，它可以看做一张新表，想要任何数据均可以从此表中查询，这就是表联接的强大之处。

 

## 表联接的分类：

### 内联接

内联接是指两个表中某一行相关的列值匹配时，这一行才会出现在表中。就像上例中`s._fk`与`c._infor`相同时才会出行该行，其他的行剔除。

语法为`INNER JOIN` 其中`INNER`可以省略。

内联接的简写：

```sql
SELECT
  *
FROM t_student s,
  t_class c
WHERE
  c._infor = s._fk
```

此写法也是我们用的最多的。

### 外联接

**分类：**

* 左外联接
* 右处联接。

外联接是指不管有没有匹配，被定义了外联接的表数据都要出现在结果中。比如左外联接，那么在`JOIN`左边的表就被定义为外联接，那么此表中所有数据都会出现在查询结果中。

注意班级表中的四班是没有学生的，所以在内联接之后理所当然的被剔除了。现在以外联接做示例：

```sql
SELECT
  *
FROM t_student s
RIGHT JOIN t_class c ON s._fk = c._infor;
```

上面`SQL`中表`t_class`在写在`JOIN`的右边，所以我们用`RIGHT JOIN`来进行外联接。

![](https://pic.downk.cc/item/5e85951e504f4bcb04d9d2c9.jpg)

最下面多了一行四班的信息

例如我们想查出还没有学生录入的班级信息：

```sql
SELECT
  c._id,
  c._cname,
  c._code
FROM t_student s
RIGHT JOIN t_class c ON s._fk = c._infor
WHERE
  s._id IS NULL;
```

![](https://pic.downk.cc/item/5e859544504f4bcb04d9f03f.jpg)
这就是外联接的用法，通常用在我们想要的数据匹配不上时。

 

### 自联接

自联接属于内联接或外联接的一种特例，自联接所联接的表均是来自同一张，用法个人感觉还是比较巧妙的。

现有一表如下：

![](https://pic.downk.cc/item/5e859558504f4bcb04d9fd75.jpg)

表中，6个人均属于某公司的员工。区别是李四为张三和王五的领导，张八为赵六和孙七的领导。`leader_id`与`work_id`相关联。

现在可以通过自联接巧妙的将一张表分为员工部分和领导部分：

```sql
SELECT
  w.work_name,
  l.work_name as '领导姓名'
FROM t_emp w,
  t_emp l
WHERE
  w.leader_id = l.work_id;
```

注意别名的用法

结果：

![](https://pic.downk.cc/item/5e85957b504f4bcb04da132b.jpg)

是不是有点方便？

 

## 知识点罗列到这里，做题时间到

### 1.查询凤姐所在的班级

```sql
SELECT
  _cname
FROM t_student s,
  t_class c
WHERE
  c._infor = s._fk
  AND s._name = '凤姐';
```

![](https://pic.downk.cc/item/5e8595a1504f4bcb04da2d18.jpg)

### 2.查询同朱军同班级的学生

```sql
SELECT
  s._name
FROM t_student s
WHERE
  s._fk = (
    SELECT
      cc._infor
    FROM t_class cc,
      t_student ss
    WHERE
      ss._fk = cc._infor
      AND ss._name = '朱军'
  )
  AND s._name != '朱军';
```

本题中，括号内为联接后的表，其返回的是'朱军'所在班级的`_infor`，然后主查询在学生表中匹配与`_infor`相等的`_fk`的行，最后从匹配成功后的行中剔除'朱军'自己。

![](https://pic.downk.cc/item/5e8595cd504f4bcb04da49e7.jpg)

### 3.查询每个班级的人数

```sql
SELECT
  d._cname,
  COUNT(_name)
FROM (
    SELECT
      ss.*,
      cc._cname
    FROM t_class cc
    LEFT JOIN t_student ss ON ss._fk = cc._infor
  ) d
GROUP BY
  d._cname;
```

本题中，括号内为班级表外联接后的表，并给该联接后的表以别名`d`，按`d`的班级名称`d._cname`分组后统计各班人数。这里之所以用外联接还是因为四班没有学生但依然要统计。

![](https://pic.downk.cc/item/5e8595fb504f4bcb04da677c.jpg)

### 4.查询班级人数最多的班级

```sql
SELECT
  cc._cname,
  COUNT(_name)
FROM t_class cc,
  t_student ss
WHERE
  cc._infor = ss._fk
GROUP BY
  cc._cname
HAVING
  COUNT(_name) >= ALL(
    SELECT
      COUNT(_name)
    FROM t_class c,
      t_student s
    WHERE
      c._infor = s._fk
    GROUP BY
      c._cname
);
```

这个有点凶残，用了两次表联接。括号内返回的是每个班的人数：

![](https://pic.downk.cc/item/5e859619504f4bcb04da7a8a.jpg)
之后外部又使用了一次表联接，将每个班的人数与括号内的返回值逐一比较，得到最大值，然后找到最大值所在的班级。这里就体现了对`SQL`执行顺序的理解有多重要了，联接、分组、过滤等等的先后顺序。

结果：

![](https://pic.downk.cc/item/5e85962a504f4bcb04da87a3.jpg)

### 5.查询每个班中年龄最低的人

```sql
SELECT
  cc._cname,
  ss._name,
  ss._age
FROM t_student ss,
  t_class cc
WHERE
  ss._fk = cc._infor
  AND ss._age <= ALL(
    SELECT
      MIN(s._age)
    FROM t_student s
    WHERE
      ss._fk = s._fk
);
```

本题中，括号内部返回一个学生表中的最小年龄，外部进行表联接后将年龄列对返回值进行比较，若小于等于返回的最小值那其本身也为最小值。

如果括号内部不加判断条件`WHERE ss._fk = s._fk`，则最后只会查询出一条年龄最小的数据，而并没有按我们想要的查询出每个班的最小值。

如：

![](https://pic.downk.cc/item/5e859647504f4bcb04da99c0.jpg)
有人会问了既然按班分，用分组不就好了？但要注意的是最小年龄的人不只一个，而分组后每一个班只会显示一个人。所以这里用了关联条件`WHERE ss._fk = s._fk`来让内外表关联，从而统计出所有我们想要的值。

结果：

![](https://pic.downk.cc/item/5e859654504f4bcb04daa1c5.jpg)

