---
title: Java核心类
date: 2021-06-03 14:39:55
type: artitalk
categories:
- Java基础
tags:
- JAVA
cover: https://cdn.pixabay.com/photo/2018/01/17/20/22/analytics-3088958__340.jpg
---

# 字符串和编码(`String and Coding`)

## 字符串

在Java中，String属于引用类型，本身也是一个类。

### 字符串比较

两个字符串比较，必须总是使用`equals()`方法，不能用`==`。

```java
public class Main {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "hello";
        System.out.println(s1.equals(s2));
        //要忽略大小写比较，使用equalsIgnoreCase()方法
    }
}
```

### 是否包含子串、搜索子串、提取子串

`String`类提供了多种方法来判断是否包含子串、搜索子串、提取子串。

1. 是否包含子串

   ```java
   "Hello".contains("ll"); // true
   ```

2. 搜索子串

   ```java
   "Hello".indexOf("l"); // 2
   "Hello".lastIndexOf("l"); // 3
   "Hello".startsWith("He"); // true
   "Hello".endsWith("lo"); // true
   ```

3. 提取子串

   ```java
   "Hello".substring(2); // "llo"
   "Hello".substring(2, 4); "ll"
   //索引号是从0开始的
   ```

### 去除首尾空白字符



# 字符串拼接(`StringBuilder`)

> 1. 高效拼接字符串用StringBuilder，要在拼接字符串时还需要添加分隔符使用StringJoiner。
> 2. 用指定分隔符拼接字符串数组时，使用`StringJoiner`或者`String.join()`更方便；
> 3. 用`StringJoiner`拼接字符串时，还可以额外附加一个“开头”和“结尾”。

