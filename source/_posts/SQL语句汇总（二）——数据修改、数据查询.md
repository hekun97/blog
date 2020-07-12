# SQL语句汇总（二）——数据修改、数据查询

## 前言

`SQL`语句第二篇，不说废话直接开始吧。

首先创建一张表如下，创建表的方法在上篇介绍过了，这里就不再赘述。

![](https://pic.downk.cc/item/5e858868504f4bcb04d08c36.jpg)

## 数据修改

* 添加新数据
* 更改数据
* 删除数据（行）

### 添加新数据：

```mysql
INSERT INTO < 表名 > 
  (< 列名列表 >)
VALUES
  (< 值列表 >)
```

**如：**

```mysql
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

```mysql
INSERT INTO t_student
VALUES
  (2, '王二', 20, '男');
```
![](https://pic.downk.cc/item/5e8588f6504f4bcb04d10c43.jpg)

**多行数据添加：**

```mysql
INSERT INTO t_student
VALUES
  (3, '张三', 22, '男'),
  (4, '李四', 17, '女'),
  (5, '王五', 23, '男');
```

![](https://pic.downk.cc/item/5e8588f6504f4bcb04d10c43.jpg)

### 更改数据：

```mysql
UPDATE 表名
SET
  列1 = 新值1,
  列2 = 新值2
WHERE
  过滤条件
```

假如要修改李四的年龄为21岁

```mysql
UPDATE t_student
SET
  student_age = 21
WHERE
  student_name = '李四';
```

![](https://pic.downk.cc/item/5e858953504f4bcb04d15f04.jpg)

<font color="red">注：修改多个列的值时用逗号隔开。要想设置某一列的值为空，只需让`<列名>=NULL` 即可。`WHERE`表示过滤条件。</font>

###  删除数据（行）：

```mysql
DELETE FROM 表名
WHERE
  过滤条件
```

现要删除20到22岁的学生信息：

```mysql
DELETE FROM t_student
WHERE
  student_age BETWEEN 20
  AND 22;
```

![](https://pic.downk.cc/item/5e8589b5504f4bcb04d1aa51.jpg)

`WHERE`的判断条件之后会更详细的介绍。

删除除了`DELETE`还有一种方法`TRUNCATE`，写法：

```mysql
TRUNCATE TABLE 表名
```

**二者区别在于：**
`DELETE`会记录日志，意味着删除后的数据还可以恢复，但是效率低。`TRUNCATE`不会记录日志，删除后的数据不能恢复，但是效率高。需要注意的是，`TRUNCATE`不能用于有外键约束引用的表。

## 查询操作

分类：

- 投影操作
指定查询结果中能显示哪些列

- 选择操作
指定哪些行出现在结果中

- 排序操作
指定查询的结果以什么样的顺序显示

### 投影操作：

```mysql
SELECT
  列1,
  列2
FROM 表名
```

多个列中间用逗号隔开，如果选择所有列可以用`*`号简写。

还是此表：

![](https://pic.downk.cc/item/5e858953504f4bcb04d15f04.jpg)

现在只想要查看姓名和年龄列：

```mysql
SELECT
  student_name,
  student_age
FROM t_student;
```

![](https://pic.downk.cc/item/5e858b21504f4bcb04d2a9a9.jpg)

注意这里不是把其他列删除了，而是只显示我们想看见的部分。

```mysql
SELECT
  CONCAT(student_name, '——', student_age) '组合值'
FROM t_student;
```

`CONCAT`，可以将列与列之间用想要的符号连接起来：  

![](https://pic.downk.cc/item/5e858b6d504f4bcb04d2e262.jpg)

#### 排除重复——DISTINCT

现给原表加入一班级列：

![](https://pic.downk.cc/item/5e858baa504f4bcb04d30d15.jpg)

按照之前方法查询班级列得到：

![](https://pic.downk.cc/item/5e858bba504f4bcb04d31704.jpg)

但是我们只想查看具体有哪些班级，这里就需要用到去重，也就是`DISTINCT`。

```mysql
SELECT
  DISTINCT student_class
FROM t_student;
```

![](https://pic.downk.cc/item/5e858bd7504f4bcb04d32e9b.jpg)

#### 返回限定行数的查询——`LIMIT`

`LIMIT`后面参数为1或2个：

`LIMIT N `表示从第一行开始返回`N`行结果，`LIMIT i,N `表示从第`i+1`行开始返回`N`行结果。

例：

```mysql
SELECT
  *
FROM t_student
LIMIT
  3;
```

![](https://pic.downk.cc/item/5e858c13504f4bcb04d35ddb.jpg)

```mysql
SELECT
  *
FROM t_student
LIMIT
  2, 3;
```

![](https://pic.downk.cc/item/5e858c21504f4bcb04d36847.jpg)

<font color="red">注：`LIMIT`很重要，它是之后做数据表格分页的关键。</font>

### 选择操作——WHERE：

* 单条件选择
* 多条件选择

**单条件选择标准结构：**

```mysql
SELECT
  列1,
  列2
FROM 表名
WHERE
  列3 = 值
```

关系运算符包括：`>  >=  <  <=  =  !=`

**多条件选择标准结构：**

```mysql
SELECT
  列A,
  列B
FROM 表
WHERE
  条件1 (
    AND 或者OR
  ) 条件2
```

其中`AND`表示并且，`OR`表示或者。

#### 选择范围——BETWEEN

如：

```mysql
SELECT
  *
FROM t_student
WHERE
  student_age BETWEEN 20
  AND 23;
```

![](https://pic.downk.cc/item/5e858c9c504f4bcb04d3bc84.jpg)

`BETWEEN`后的值为从下限到上限。

#### 定义集合——IN或NOT IN

现在想查看年龄为17、20、23的学生信息： 

```mysql
SELECT
  *
FROM t_student
WHERE
  student_age IN (17, 20, 23);
```

![](https://pic.downk.cc/item/5e858cca504f4bcb04d3d9f6.jpg)
反之`NOT IN`就是选择不包括在集合里的学生信息。

#### 模糊查询——LIKE

为了更好的解释模糊查询，这里重新建张表：

![](https://pic.downk.cc/item/5e858ce6504f4bcb04d3ea6c.jpg)

这里姓王的兄弟们躺枪...别介意。

**首先先说下占位符与通配符：**

占位符 `_`，表示任何单个字符。

通配符 `%`，表示包含零或多个字符。

下面就来用模糊查询逐一选中我们想要的行。

**名字只有两个字的：**

```mysql
SELECT
  *
FROM t_student
WHERE
  student_name LIKE '__';
```

这里可能看不清，引号里实际是两个占位符。

![](https://pic.downk.cc/item/5e858d07504f4bcb04d400b9.jpg)

**所有姓王的：**

```mysql
SELECT
  *
FROM t_student
WHERE
  student_name LIKE '王%';
```

![](https://pic.downk.cc/item/5e858d22504f4bcb04d411df.jpg)

**最后一个字是“王”的：**

```mysql
SELECT
  *
FROM t_student
WHERE
  student_name LIKE '%王';
```

![](https://pic.downk.cc/item/5e858d59504f4bcb04d43c32.jpg)
**只要是名字中有“王”字的：**

```mysql
SELECT
  *
FROM t_student
WHERE
  student_name LIKE '%王%';
```

![](https://pic.downk.cc/item/5e858d69504f4bcb04d448d9.jpg)
这下模糊查询就很明白了吧，当然还有其他组合，大家可以自己尝试。

#### 处理空值数据：

判断条件不能用`列名=NULL`，而是要用`IS NULL`或`IS NOT NULL`。

**标准写法：**

```mysql
SELECT
  *
FROM t_student
WHERE
  性别 IS NULL
```

### 排序操作——ORDER BY：

使用`ORDER BY`时，列名上指定`ASC`或`DESC`。`ASC`表示正序，`DESC`表示倒序。如果不指定则默认为正序。

**按年龄排：**

```mysql
SELECT
  *
FROM t_student
ORDER BY
  student_age ASC;
```

![](https://pic.downk.cc/item/5e858dc8504f4bcb04d4a68a.jpg)

```mysql
SELECT
  *
FROM t_student
ORDER BY
  student_age DESC;
```

![](https://pic.downk.cc/item/5e858dc8504f4bcb04d4a68a.jpg)

## 最后一定要注意！

**基本查询SQL的执行顺序：**

1.    执行`FROM `
2. `WHERE`条件过滤 
3. `SELECT`投影 
4. `ORDER BY`排序

## 结语

`SQL`的第二篇就到这里了。