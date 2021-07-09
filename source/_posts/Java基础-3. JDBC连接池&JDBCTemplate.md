---
title: JDBC连接池&JDBCTemplate
date: 2021-07-06 09:54:16
tags: 
- JavaSE JDBC
category:
- Java基础
type: artitalk
cover: https://cdn.pixabay.com/photo/2021/06/22/17/24/torres-del-paine-6356782_960_720.jpg
---

# 数据库连接池

## 基本知识

### 概念

数据库连接池其实就是一个容器(集合)，存放数据库连接的容器。
当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。

> 之前使用工具类JDBCUtils时，每次使用都要获取连接，使用完就被释放掉了，非常浪费资源并导致执行效率比较低。使用数据库连接池可以简化操作，数据库连接池存放几个连接，使用时从连接池中进行获取连接，使用完之后将连接归还到连接池中，不用反复的获取和释放连接，提高执行效率。

### 好处

* 节约资源：不用每次都创建，用完后删除。
* 用户访问高效：直接从容器中获取。

### 实现

1. 需要使用在javax.sql包下由官方提供的标准接口(DataSource)，DataSource接口由驱动程序供应商实现，生成标准的Connection对象。
   - 方法：
     1. 获取连接：getConnection()

     2. 归还连接：Connection.close()。

        > 如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接了，而是归还连接。
2. 一般我们不去实现它，由数据库厂商来实现。
    1. C3P0：数据库连接池技术，比较老了，使用比较少；
    2. Druid：数据库连接池实现技术，由阿里巴巴提供的，性能最好的数据库连接技术之一。



## C3P0数据库连接池技术

### 使用步骤

1. 导入 两个jar包，`c3p0-0.9.5.2.jar`和`mchange-commons-java-0.2.12.jar `；

   > 不要忘记导入数据库驱动jar包：`mysql-connector-java-5.1.37-bin.jar`。

2. 定义配置文件：
      - 名称： c3p0.properties 或者 c3p0-config.xml，这里使用xml进行配置。
      
        > xml的知识可在[XML 笔记](2021/03/23/xml-bi-ji/)查看。
      
      - 路径：直接将文件放在src目录下即可，可以被自动加载进内存。
      
3. 创建核心对象-数据库连接池对象(ComboPooledDataSource)

4. 获取连接： getConnection。

> 连接获取后，具体的使用和之前的差不多。

#### 配置文件

```xml
<!--c3p0-config.xml-->
<?xml version="1.0" encoding="UTF-8"?>
<c3p0-config>
    <!-- 使用默认的配置读取连接池对象 -->
    <default-config>
        <!--  连接参数 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql:///db3</property>
        <property name="user">root</property>
        <property name="password">root</property>

        <!-- 连接池参数 -->
        <!--初始化的申请的连接数量-->
        <property name="initialPoolSize">5</property>
        <!--最大的连接数量-->
        <property name="maxPoolSize">10</property>
        <!--连接超时时间-->
        <property name="checkoutTimeout">3000</property>
    </default-config>

    <named-config name="otherc3p0">
        <!--  连接参数 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql:///db3</property>
        <property name="user">root</property>
        <property name="password">root</property>

        <!-- 连接池参数 -->
        <property name="initialPoolSize">5</property>
        <property name="maxPoolSize">8</property>
        <property name="checkoutTimeout">1000</property>
    </named-config>
</c3p0-config>
```

#### 实现代码

```java
package io.gitee.hek97.datasource.c3p0;

import com.mchange.v2.c3p0.ComboPooledDataSource;

import java.sql.Connection;
import java.sql.SQLException;

public class C3P0Demo1 {
    public static void main(String[] args) throws SQLException {
        //1. 导入jar包
        //2. 定义配置文件
        //3. 创建数据库连接池对象
        ComboPooledDataSource ds = new ComboPooledDataSource();
        //4. 获取连接对象
        Connection conn = ds.getConnection();
        //打印
        System.out.println(conn);
        //com.mchange.v2.c3p0.impl.NewProxyConnection@4de4b452 [wrapping: com.mysql.jdbc.JDBC4Connection@50b5ac82]
    }
}
```

## Druid

数据库连接池实现技术，由阿里巴巴提供的。

### 使用步骤

1. 导入jar包 `druid-1.0.9.jar`；
2. 定义配置文件：
    - 名称：只能使用properties形式的，可以叫任意名称；
    - 路径：可以放在任意目录下，需要手动加载进内存。
3. 加载Properties配置文件；
4. 获取数据库连接池对象：通过工厂类 来获取  DruidDataSourceFactory；
5. 获取连接，使用getConnection方法。

#### 配置文件

```properties
# druid.properties
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql:///db3?characterEncoding=UTF-8
username=root
password=root
# 初始化连接数
initialSize=5
# 最大连接数
maxActive=10
# 超时时间
maxWait=3000
```

#### 实现代码

```java
package io.gitee.hek97.datasource.druid;

import com.alibaba.druid.pool.DruidDataSource;
import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.util.Properties;

public class DruidDemo {
    public static void main(String[] args) throws Exception {
        //1. 导入jar包
        //2. 定义配置文件
        //3. 加载配置文件
        Properties pro = new Properties();
        InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
        pro.load(is);
        //4. 获取连接池对象
        DataSource ds = DruidDataSourceFactory.createDataSource(pro);
        //5. 获取连接
        Connection conn = ds.getConnection();
        //6. 打印
        System.out.println(conn);
    }
}
```

### 定义工具类

#### 分析

  1. 定义一个类 JDBCUtils；
  2. 提供静态代码块加载配置文件，初始化连接池对象；
  3. 提供方法供外部调用。
        1. 获取连接方法：通过数据库连接池获取连接
        2. 释放资源
        3. 获取连接池的方法

#### 实现代码

* 工具类 JDBCUtils

  ```java
  package io.gitee.hek97.utils;
  
  import com.alibaba.druid.pool.DruidDataSourceFactory;
  
  import javax.sql.DataSource;
  import java.io.IOException;
  import java.io.InputStream;
  import java.sql.Connection;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  import java.util.Properties;
  
  public class JDBCUtils {
      private static DataSource ds;
      static {
          try {
              //1. 加载配置文件
              Properties pro = new Properties();
              InputStream rs = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
              pro.load(rs);
              //2. 初始化连接池对象
              ds= DruidDataSourceFactory.createDataSource(pro);
          } catch (IOException e) {
              e.printStackTrace();
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 连接池对象
       * @return 连接池对象
       */
      public static DataSource getDs(){
          return ds;
      }
  
      /**
       * 获取连接
       * @return 连接对象
       * @throws SQLException
       */
      public static Connection getConnection() throws SQLException {
          return ds.getConnection();
      }
      public static void close(PreparedStatement ps,Connection conn){
          close(null,ps,conn);
  
      }
  
      /**
       * 释放资源
       * @param rs
       * @param ps
       * @param conn
       */
      public static void close(ResultSet rs, PreparedStatement ps, Connection conn){
          if(rs!=null){
              try {
                  rs.close();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
          }
          if(ps!=null){
              try {
                  ps.close();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
          }
          if(conn!=null){
              try {
                  conn.close();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
          }
  
      }
  }
  

* 使用工具类 DruidDemo2

  ```java
  package io.gitee.hek97.datasource.druid;
  
  import io.gitee.hek97.utils.JDBCUtils;
  
  import javax.sql.DataSource;
  import java.sql.Connection;
  import java.sql.PreparedStatement;
  import java.sql.ResultSet;
  import java.sql.SQLException;
  
  
  public class DruidDemo2 {
      public static void main(String[] args){
          Connection conn = null;
          PreparedStatement ps=null;
          ResultSet rs=null;
          try {
              //1. 获取连接
              conn = JDBCUtils.getConnection();
              //2. 定义SQL
              String sql ="select * from account";
              //3. 获取执行SQL对象
              ps = conn.prepareStatement(sql);
              //4. 执行SQL
              rs = ps.executeQuery();
              //5. 处理结果
              while (rs.next()){
                  int id = rs.getInt(1);
                  String name = rs.getString(2);
                  int balance = rs.getInt(3);
                  System.out.println(id+"---"+name+"---"+balance);
              }
          } catch (SQLException e) {
              e.printStackTrace();
          }finally {
              //6. 释放资源
              JDBCUtils.close(rs,ps,conn);
          }
      }
  }
  ```

# Spring JDBC

## 基本概念

Spring JDBC是Spring框架对JDBC的简单封装，提供了一个JDBCTemplate对象简化JDBC的开发。

> 主要作用是简化代码。

## 使用步骤

  1. 导入jar包

     ![](https://pic.imgdb.cn/item/60e315155132923bf8e1af60.jpg)

  2. 创建JdbcTemplate对象，依赖于数据源DataSource，代码为`JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());`，其中的JDBCUtils.getDataSource()是用[JDBCUtils](https://github.com/hekun97/JavaCode/blob/master/Test/JDBCTemplate/src/io/gitee/hek97/utils/JDBCUtils.java)工具类中的getDataSource()方法来获取数据库连接池。

  3. 调用JdbcTemplate的方法来完成CRUD的操作

     * `update()`：执行DML语句。增、删、改语句；
     * `queryForMap()`：查询结果将结果集封装为map集合，将列名作为key，将值作为value 将这条记录封装为一个map集合

       > 这个方法查询的结果集只能为数据库的1行数据。
     * `queryForList()`：查询结果将结果集封装为list集合；

       > 将每一条记录封装为一个Map集合，再将Map集合装载到List集合中
     * `query()`：查询结果，将结果封装为JavaBean对象；

       > 一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean的自动封装，代码格式：`new BeanPropertyRowMapper<类型>(类型.class)`。
     * `queryForObject()`：查询结果，将结果封装为对象，一般用于聚合函数的查询。

## 快速入门

```java
package io.gitee.hek97.datasource.template;

import io.gitee.hek97.utils.JDBCUtils;
import org.springframework.jdbc.core.JdbcTemplate;

public class JdbcTemplateDemo1 {
    public static void main(String[] args) {
        //1. 导入jar包
        //2. 创建JdbcTemplate对象
        JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
        //3. 定义SQL语句
        String sql="update account set balance =? where id = ?";
        //4. 执行SQL语句
        int count = template.update(sql,500,1);//第一个参数为SQL语句，后面的参数为?的值。
        //5. 处理结果
        System.out.println(count);//1
    }
}
```

> 其中需要使用[JDBCUtils](https://github.com/hekun97/JavaCode/blob/master/Test/JDBCTemplate/src/io/gitee/hek97/utils/JDBCUtils.java)工具类中的getDataSource()方法来获取数据库连接池，getConnection()和close()方法在这不使用可以选择直接删除。

## 进阶练习

### 练习内容

  1. 修改1号数据的 balance 为 10000
  2. 添加一条记录
  3. 删除刚才添加的记录
  4. 查询id为1的记录，将其封装为Map集合
  5. 查询所有记录，将其封装为List
  6. 查询所有记录，将其封装为Emp对象的List集合
  7. 查询总记录数

### 实现代码

```java
package io.gitee.hek97.datasource.template;

import io.gitee.hek97.domain.Account;
import io.gitee.hek97.utils.JDBCUtils;
import org.junit.Test;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import java.lang.invoke.VarHandle;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;
import java.util.Map;

/**
 * 执行DML，DQL语句
 */
public class JdbcTemplateDemo2 {
    //导入jar包
    //创建template对象
    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
    
    //Junit单元测试，可以让方法独立运行
    //1. 修改1号数据的 balance 为 10000
    @Test
    public void test1() {
        String sql = "update account set balance = ? where id = ?";
        int count = template.update(sql, 10000, 1);
        System.out.println(count);
    }

    //2. 添加一条记录
    @Test
    public void test2() {
        String sql = "insert into account values(null,'麻子',2000)";
        int count = template.update(sql);
        System.out.println(count);
    }

    //3. 删除刚刚添加的记录
    @Test
    public void test3() {
        String sql = "delete from account where id =?";
        int count = template.update(sql, 4);
        System.out.println(count);
    }

    //4. 查询id为1的记录，将其封装为Map集合
    @Test
    public void test4() {
        String sql = "select * from account where id = ?";
        Map<String, Object> map = template.queryForMap(sql, 1);
        System.out.println(map);//{id=1, name=张三, balance=3000}
    }

    //5. 查询所有记录，将其封装为List
    @Test
    public void test5() {
        String sql = "select * from account";
        List<Map<String, Object>> maps = template.queryForList(sql);
        System.out.println(maps);
        for (Map<String, Object> map : maps) {
            System.out.println(map);
        }
    }

    //6.1 查询所有记录，将其封装为Emp对象的List集合，传统方式
    @Test
    public void test6_1() {
        String sql = "select * from account";
        List<Account> accounts = template.query(sql, new RowMapper<Account>() {
            @Override
            public Account mapRow(ResultSet rs, int i) throws SQLException {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                int balance = rs.getInt("balance");
                Account account = new Account();
                account.setId(id);
                account.setName(name);
                account.setBalance(balance);
                return account;
            }
        });
        System.out.println(accounts);
    }

    //6.2 查询所有记录，将其封装为Emp对象的List集合，使用new BeanPropertyRowMapper对象简化操作。
    @Test
    public void test6_2() {
        String sql = "select * from account";
        List<Account> accounts = template.query(sql, new BeanPropertyRowMapper<Account>(Account.class));
        System.out.println(accounts);
        for (Account account : accounts) {
            System.out.println(account);
        }
    }

    //7. 查询总记录数
    @Test
    public void test7() {
        String sql = "select count(id) from account";
        Long count = template.queryForObject(sql, Long.class);
        System.out.println(count);
    }
}
```

