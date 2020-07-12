# SQL语句汇总（终篇）—— 表联接与联接查询

## 前言

既然是最后一篇那就不能只列出些干枯的标准语句，更何况表联接也是`SQL`中较难的部分，所以此次搭配题目来详细阐述表联接。

 
## 表联接

上一篇博文说到相关子查询效率低下，那我们怎么能将不同表的信息一起查询出来呢？这就需要用到表联接。

和之前的`UNION`组合查询不同，`UNION`是将不同的表组合起来，也就是纵向联接，说白了就是竖着拼起来。

而表联接是通过笛卡尔乘积将表进行横向联接，所谓的通过笛卡尔乘积简单说就是两表的行依次相联再相加。要想更详细的理解可以百度下，毕竟本文主要是汇总`SQL`语句。

现在有如下两张表：

![](https://pic.downk.cc/item/5e859487504f4bcb04d967d5.jpg)
![](https://pic.downk.cc/item/5e859494504f4bcb04d97383.jpg)

这是当初老师布置的一份作业，我偷个懒就不改数据了。不过把这些真神级人物的大名贴出来做“实验”总觉得心里有很虚，更何况大部分都是IT业的。如有什么不敬我先道个歉，别跟我一般见识。

好了，扯远了。怎么联接这两张表呢？标准写法：

```mysql
SELECT
  *
FROM t_student
JOIN t_class;
```

结果这里只截一小部分图，因为笛卡尔乘积后的行数等于两张表的行数乘积，实在太多了。

![](https://pic.downk.cc/item/5e8594ae504f4bcb04d984a7.jpg)

这里就可以理解表联接的原理了，依次相连再相加。当然其中很多是无效行，为了去除无效的行我们就要用到外键来进行约束。学生表中的`_fk`与班级表中的`_infor`相关联：

```mysql
SELECT
  *
FROM t_student s
JOIN t_class c ON s._fk = c._infor;
```

结果：

![](https://pic.downk.cc/item/5e8594c2504f4bcb04d9917b.jpg)

这里通过外键的匹配我们就得到了一张完美的联接之后的表，它可以看做一张新表，想要任何数据均可以从此表中查询，这就是表联接的强大之处。

 

## 表联接的分类：

### 内联接：

内联接是指两个表中某一行相关的列值匹配时，这一行才会出现在表中。就像上例中`s._fk`与`c._infor`相同时才会出行该行，其他的行剔除。

语法为`INNER JOIN` 其中`INNER`可以省略。

内联接的简写：

```mysql
SELECT
  *
FROM t_student s,
  t_class c
WHERE
  c._infor = s._fk
```

此写法也是我们用的最多的。

### 外联接：

**分类：**

* 左外联接
* 右处联接。

外联接是指不管有没有匹配，被定义了外联接的表数据都要出现在结果中。比如左外联接，那么在`JOIN`左边的表就被定义为外联接，那么此表中所有数据都会出现在查询结果中。

注意班级表中的四班是没有学生的，所以在内联接之后理所当然的被剔除了。现在以外联接做示例：

```mysql
SELECT
  *
FROM t_student s
RIGHT JOIN t_class c ON s._fk = c._infor;
```

上面`SQL`中表`t_class`在写在`JOIN`的右边，所以我们用`RIGHT JOIN`来进行外联接。

![](https://pic.downk.cc/item/5e85951e504f4bcb04d9d2c9.jpg)

最下面多了一行四班的信息

例如我们想查出还没有学生录入的班级信息：

```mysql
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

 

### 自联接：

自联接属于内联接或外联接的一种特例，自联接所联接的表均是来自同一张，用法个人感觉还是比较巧妙的。

现有一表如下：

![](https://pic.downk.cc/item/5e859558504f4bcb04d9fd75.jpg)

表中，6个人均属于某公司的员工。区别是李四为张三和王五的领导，张八为赵六和孙七的领导。`leader_id`与`work_id`相关联。

现在可以通过自联接巧妙的将一张表分为员工部分和领导部分：

```mysql
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

 

## 知识点罗列到这里，做题时间到：

### 1.查询凤姐所在的班级

```mysql
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

```mysql
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

```mysql
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

```mysql
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

```mysql
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

## 结语

终于把这个小系列写完了！