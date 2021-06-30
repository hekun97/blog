---
title: Servlet&HTTP&Request练习案例-用户登录
date: 2021-06-28 09:39:52
tags: 
- Java HTTP Servlet Request
category:
- JavaWEB
type: artitalk
cover: https://cdn.pixabay.com/photo/2021/06/11/16/02/clouds-6328911_960_720.jpg


---

# 案例：用户登录

## 用户登录案例需求

1.编写login.html登录页面，含有username & password 两个输入框；
2.使用Druid数据库连接池技术,操作mysql，today数据库中user表；
3.使用JdbcTemplate技术封装JDBC；
4.登录成功跳转到SuccessServlet展示：登录成功！用户名,欢迎您；
5.登录失败跳转到FailServlet展示：登录失败，用户名或密码错误。

## 分析

![](https://pic.imgdb.cn/item/60d9dfd05132923bf83e8e99.jpg)

## 开发步骤

1. 创建WEB项目，导入html页面到web目录下，数据库的配置文件到src目录下，bin目录下的jar包到WEB-INF目录下。

   ![](https://pic.imgdb.cn/item/60d9e6e45132923bf8805824.jpg)

2. 创建数据库环境

  ```mysql
  CREATE DATABASE today;
  USE today;
  CREATE TABLE USER(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	username VARCHAR(32) UNIQUE NOT NULL,
  	PASSWORD VARCHAR(32) NOT NULL
  );
  ```

3. 创建包`cn.itcast.domain`,创建类User

  ```java
  package cn.itcast.domain;
  
  /**
   * 用户的实体类
   */
  public class User {
  
      private int id;
      private String username;
      private String password;
  
      private String gender;
  
      public void setHehe(String gender){
          this.gender = gender;
      }
  
      public String getHehe(){
          return gender;
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getUsername() {
          return username;
      }
  
      public void setUsername(String username) {
          this.username = username;
      }
  
      public String getPassword() {
          return password;
      }
  
      public void setPassword(String password) {
          this.password = password;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", username='" + username + '\'' +
                  ", password='" + password + '\'' +
                  ", gender='" + gender + '\'' +
                  '}';
      }
  }
  ```

4. 创建包cn.itcast.util,编写工具类JDBCUtils

  ```java
  package cn.itcast.util;
  
  import com.alibaba.druid.pool.DruidDataSourceFactory;
  
  import javax.sql.DataSource;
  import javax.xml.crypto.Data;
  import java.io.IOException;
  import java.io.InputStream;
  import java.sql.Connection;
  import java.sql.SQLException;
  import java.util.Properties;
  
  /**
   * JDBC工具类 使用Durid连接池
   */
  public class JDBCUtils {
  
      private static DataSource ds ;
  
      static {
  
          try {
              //1.加载配置文件
              Properties pro = new Properties();
              //使用ClassLoader加载配置文件，获取字节输入流
              InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
              pro.load(is);
  
              //2.初始化连接池对象
              ds = DruidDataSourceFactory.createDataSource(pro);
  
          } catch (IOException e) {
              e.printStackTrace();
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
      /**
       * 获取连接池对象
       */
      public static DataSource getDataSource(){
          return ds;
      }
  
  
      /**
       * 获取连接Connection对象
       */
      public static Connection getConnection() throws SQLException {
          return  ds.getConnection();
      }
  }
  ```

5. 创建包cn.itcast.dao,创建类UserDao,提供login方法

  ```java
  package cn.itcast.dao;
  
  import cn.itcast.domain.User;
  import cn.itcast.util.JDBCUtils;
  import org.springframework.dao.DataAccessException;
  import org.springframework.jdbc.core.BeanPropertyRowMapper;
  import org.springframework.jdbc.core.JdbcTemplate;
  
  /**
   * 操作数据库中User表的类
   */
  public class UserDao {
  
      //声明JDBCTemplate对象共用
      private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
  
      /**
       * 登录方法
       * @param loginUser 只有用户名和密码
       * @return user包含用户全部数据,没有查询到，返回null
       */
      public User login(User loginUser){
          try {
              //1.编写sql
              String sql = "select * from user where username = ? and password = ?";
              //2.调用query方法
              User user = template.queryForObject(sql,
                      new BeanPropertyRowMapper<User>(User.class),
                      loginUser.getUsername(), loginUser.getPassword());
  
  
              return user;
          } catch (DataAccessException e) {
              e.printStackTrace();//记录日志
              return null;
          }
      }
  }
  ```

6. 编写cn.itcast.web.servlet.LoginServlet类

  ```java
  package cn.itcast.web.servlet;
  
  import cn.itcast.dao.UserDao;
  import cn.itcast.domain.User;
  import org.apache.commons.beanutils.BeanUtils;
  
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;
  import java.lang.reflect.InvocationTargetException;
  import java.util.Map;
  
  
  @WebServlet("/loginServlet")
  public class LoginServlet extends HttpServlet {
  
  
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          //1.设置编码
          req.setCharacterEncoding("utf-8");
         /* //2.获取请求参数
          String username = req.getParameter("username");
          String password = req.getParameter("password");
          //3.封装user对象
          User loginUser = new User();
          loginUser.setUsername(username);
          loginUser.setPassword(password);*/
          //2.获取所有请求参数
          Map<String, String[]> map = req.getParameterMap();
          //3.创建User对象
          User loginUser = new User();
          //3.2使用BeanUtils封装
          try {
              BeanUtils.populate(loginUser,map);
          } catch (IllegalAccessException e) {
              e.printStackTrace();
          } catch (InvocationTargetException e) {
              e.printStackTrace();
          }
  
  
          //4.调用UserDao的login方法
          UserDao dao = new UserDao();
          User user = dao.login(loginUser);
  
          //5.判断user
          if(user == null){
              //登录失败
              req.getRequestDispatcher("/failServlet").forward(req,resp);
          }else{
              //登录成功
              //存储数据
              req.setAttribute("user",user);
              //转发
              req.getRequestDispatcher("/successServlet").forward(req,resp);
          }
  
      }
  
      @Override
      protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          this.doGet(req,resp);
      }
  }
  ```

  

7. 编写cn.itcast.web.FailServlet和cn.itcast.web.SuccessServlet类

  ```Java
  @WebServlet("/successServlet")
  public class SuccessServlet extends HttpServlet {
      protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //获取request域中共享的user对象
          User user = (User) request.getAttribute("user");
  
          if(user != null){
              //给页面写一句话
  
              //设置编码
              response.setContentType("text/html;charset=utf-8");
              //输出
              response.getWriter().write("登录成功！"+user.getUsername()+",欢迎您");
          }
      }
  
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          this.doPost(request,response);
      }
  }
  ```

  ```Java
  package cn.itcast.web.servlet;
  
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;
  
  @WebServlet("/failServlet")
  public class FailServlet extends HttpServlet {
      protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          //给页面写一句话
  
          //设置编码
          response.setContentType("text/html;charset=utf-8");
          //输出
          response.getWriter().write("登录失败，用户名或密码错误");
  
      }
  
      protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          this.doPost(request,response);
      }
  }
  ```

8. login.html中form表单的action路径的写法

    * 虚拟目录+Servlet的资源路径

9. BeanUtils工具类，简化数据封装
	* 用于封装JavaBean的
	1. JavaBean：标准的Java类
		1. 要求：
			1. 类必须被public修饰
			2. 必须提供空参的构造器
			3. 成员变量必须使用private修饰
			4. 提供公共setter和getter方法
		2. 功能：封装数据

	2. 概念：
		成员变量：
		属性：setter和getter方法截取后的产物
			例如：getUsername() --> Username--> username

	3. 方法：
		1. setProperty()
		2. getProperty()
		3. populate(Object obj , Map map):将map集合的键值对信息，封装到对应的JavaBean对象中