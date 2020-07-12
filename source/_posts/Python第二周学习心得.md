---
title: Python第二周学习心得
date: 2020-02-28 10:20:40
categories: 
- 学习心得
- 海龟库
tags:
- Python
- turtle
---

# turtle 库

## turtle 库的使用

### turtle简介

`turtle`(海龟)库是`turtle`绘图体系的`Python`实现

* 是`Python`语言的 <font color='#E53A40'>标准库</font>之一
* 是一个入门级的图形绘制函数库

#### 什么是标准库？

`Python`计算生态=标准库+第三方库

* 标准库：随解释器直接安装到操作系统中的功能模块
* 第三方库：需要经过安装才能使用的功能模块
* 库`Library`、包`Package`、模块`Module`，统称<font color='#E53A40'><b>模块</b></font>

#### turtle的原理

`turle`(海龟)是一种真实的存在

* 想象有一只海龟，在窗体的正中心，在画布上游走
* 走过的轨迹形成了绘制的图形
* 海龟由程序控制，可以改变颜色、宽度等

### turtle绘图窗体布局

`turtle`窗体是一个画布空间，最小单位是像素

![绘图窗体](https://pic.downk.cc/item/5e5a29d06127cc07134aef44.jpg)

函数：

```python
turtle.setup(width,height,startx,starty)
```

解析：

* setup()函数设置窗体大小及位置
	* width 窗体的宽度
	* height 窗体的高度
	* startx 窗体相对于屏幕左上角的x值
	* starty 窗体相对与屏幕左上角的y值
* 4个参数中后两个可选，没指定系统会认为该窗口在屏幕的正中心

![绘图实例](https://pic.downk.cc/item/5e5a29896127cc07134ae79c.jpg)
<font color='red'>注意：</font>`setup()`函数不是必须的，只有我们需要指定窗口的大小时才使用

### turtle空间坐标体系

分为绝对坐标和海龟坐标

#### 绝对坐标

海龟在画布的正中心，正中心的位置就是`(0,0)`，其它和空间直角坐标系一致
![绝对坐标](https://pic.downk.cc/item/5e5a29536127cc07134ae277.jpg)
函数：

```python
turtle.goto(x,y)
```

解析：`goto()`函数指的是让在任何位置的海龟，无论它在哪里，去到达某一个坐标位置

* 其中`(x,y)`表示坐标

例：

```python
import turtle
turtle.goto(100,100)
turtle.goto(100,-100)
turtle.goto(-100,-100)
turtle.goto(-100,100)
turtle.goto(0,0)
```

输出：
![goto()函数](https://pic.downk.cc/item/5e5a25346127cc07134a67dd.png)

#### 海龟坐标

![海龟坐标](https://pic.downk.cc/item/5e5a2a0b6127cc07134af5a9.jpg)

以海龟为出发点，海龟的前方就是前进方向，后面就是后退方向，左侧就是左侧方向，右侧就是右侧方向
![解析](https://pic.downk.cc/item/5e5a2ba26127cc07134b22eb.jpg)
* `turtle.fd(d)`：前进`d`个像素
* `turtle.bk(d)`：后退`d`个像素
* `turtle.circle(r,angle)`：正数以海龟左侧为圆心，以`r`为半径，旋转`angle`度，附属以海龟右侧为圆心，以`r`为半径，旋转`angle`度。

### turtle 角度坐标体系

#### 绝对角度

![绝对角度](https://pic.downk.cc/item/5e5a2cd96127cc07134b433c.jpg)

`turtle.seth(angle)`

* seth() 改变海龟行进方向
* seth() 只改变方向但不前进
* angle为绝对度数

例1：

```python
turtle.seth(45)
```

![turtle.seth(45)](https://pic.downk.cc/item/5e5a2dbb6127cc07134b62b9.jpg)

例2：

```python
turtle.seth(-135)
```

![turtle.seth(-135)](https://pic.downk.cc/item/5e5a2e066127cc07134b6d2d.jpg)

#### 海龟角度

为了更好的改变海龟的运行方向，从海龟坐标的角度，对海龟运行的一个方向，我们可以使用左右的方式，来改变它的运行角度。

![海龟角度](https://pic.downk.cc/item/5e5a2e516127cc07134b7469.jpg)

* `turtle.left(angle)`：向左侧转angle角度
* `turtle.right(angle)`：向右侧转angle角度

例1：

```python
# 导入海龟库
import turtle
# 海龟向左转45度
turtle.left(45)
# 海龟前进150像素
turtle.fd(150)
# 海龟右转135度
turtle.right(135)
# 海龟前进300像素
turtle.fd(300)
# 海龟左转135度
turtle.left(135)
# 海龟前进150度
turtle.fd(150)
# 使窗口不自动关闭
turtle.done()
```

输出：
![输出](https://pic.downk.cc/item/5e5a32586127cc07134bf301.png)

## turtle 程序语法元素分析

> - 库引用：import、from...import、import...as...
- penup()、pendown()、pensize()、pencolor()
- fd()、circle()、seth()
- 循环语句：for和in、range()函数

### 库引用与 import

#### 库引用

扩充`Python`程序功能的方式

* 使用`import`保留字完成，采用`<a>.<b>()`编码风格

```python
	import <库名>
	# <库名>.<函数名>组成了一个新函数，不会出现函数重名问题
	<库名>.<函数名>(<函数参数>)
```

例：

```python
	import turtle
	turtle.setup(650,200,200)
```

#### import 更多用法

* 使用`from`和`import`保留字共同完成

```python
	from <库名> import <函数名>
	from <库名> import *
	# <函数名>就是它的函数名，会出现函数重名问题
	<函数名>(<函数参数>)
```

例：

```python
	from turtle import *
	setup(650,200,200)
```

* 使用`import`和`as`保留字共同完成

```python
	import <库名> as <库别名>
	# <库别名>.<函数名>构成新的函数名
	<库别名>.<函数名>(<函数参数>)
```

	* 给调用的外部库关联一个更短、更适合自己的名字
例：

```python
	import turtle as t
	# 便于使用，并且能防止重名问题
	t.setup(650,200,200)
```

### turtle 画笔控制函数

#### 画笔控制函数

画笔操作后一直有效，一般成对出现；
画笔设置后一直有效，直至下次重新设置

| 函数名  | 别名  | 描述  |
| ------------ | ------------ | ------------ |
|  `turtle.penup()` | `turtle.pu()`  | 抬起画笔，海龟在飞行（不形成绘图轨迹）  |
|  `turtle.pendown()` |  `turtle.pd()` | 落下画笔，海龟在爬行（形成绘图轨迹）  |
|  `turtle.pensize(width)` |  `turtle.width(width)` | 画笔宽度，海龟的腰围  |
|  `turtle.pencolor(color)` |   | color为颜色字符串或r，g，b值，画笔颜色，海龟在涂装  |

pencolor(color)的color参数可以有三种形式

* 颜色字符串：`turtle.pencolor("purple")`
* RGB的小数值：`turtle.pencolor(0.63,0.13,0.94)`
* RGB的元组值：`turtle.pencolor((0.63,0.13,0.94))`

### turtle 运动控制函数

控制海龟行进：走直线 & 走曲线

#### 走直线

| 函数名  | 别名  | 描述  | 参数解析 |
| ------------ | ------------ | ------------ | ------------ |
|  `turtle.forword(d)` | `turtle.fd()`  | 向前行进，海龟走直线  | `d`:行进距离，可以为负数 |

#### 走曲线

| 函数名  | 别名  | 描述  | 参数解析 |
| ------------ | ------------ | ------------ | ------------ |
|  `turtle.circle(r,extent=None)` |   | 根据半径`r`绘制`extent`角度的弧形  |`r`:默认圆心在海龟左侧r距离的位置；`extent`:绘制角度，默认是360度整圆 |

例：
`turtle.circle(100)`
配图
`turtle.circle(-100,90)`
配图

### turtle 方向控制函数

控制海龟面对方向：绝对角度 & 海龟角度

#### 绝对角度

| 函数名  | 别名  | 描述  | 参数解析 |
| ------------ | ------------ | ------------ | ------------ |
|  `turtle.setheading(angle)` | `turtle.seth(angle)`  | 改变行进方向，让海龟转向  | `angle`:改变行进方向，海龟走角度 |

例：
`turtle.seth(45)`
配图
`turtle.seth(-135)`
配图

#### 海龟角度

| 函数名  | 描述  | 参数解析 |
| ------------ | ------------ | ------------ |
|  `turtle.left(angle)`    | 海龟向左转  | `angle`:在海龟当前行进方向上旋转的角度 |
|  `turtle.right(angle)`   | 海龟向右转  | `angle`:在海龟当前行进方向上旋转的角度 |

注意：方向控制函数只控制海龟的方向，并不实际绘图，绘图请使用运动控制函数

### 循环语句与range()函数

#### 循环语句

按照一定次数循环执行一组语句

```python
for <变量> in range(<参数>)
	<被循环执行的语句>
```

* <变量> 表示每次循环执行的次数，0到<次数>-1
例1：

```python
for i in range(5):
	print(i)
```

输出：

```python
0
1
2
3
4
```

例2：

```python
for i in range(5):
	print("Hello:",i)
```

输出：

```python
Hello: 0
Hello: 1
Hello: 2
Hello: 3
Hello: 4
```

`print`中间加上`,`后，会输出形成一个空格

#### range() 函数

产生循环计数序列

* range(N)

| 函数名    | 描述  |
| ------------ | ------------ |
|  `range(N)`    | 产生 0 到 N - 1 的整数序列，共 N 个  |
|  `range(M,N)`   | 产生M到N-1的整数序列，共N-Mge   |

例1：

```python
range(5):
```

输出：`0,1,2,3,4`
例2：

```python
range(2,5):
```

输出：`2,3,4`
