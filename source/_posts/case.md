# 案例：用户信息列表展示

## 需求

用户信息的增删改查操作。

## 设计

1. 技术选型：Servlet+JSP+MySQL+JDBCTempleat+Duird+BeanUtilS+tomcat

2. 数据库设计：

   ```mysql
   create database today; -- 创建数据库
   use today; 			   -- 使用数据库
   create table user(   -- 创建表
   	id int primary key auto_increment,
   	name varchar(20) not null,
   	gender varchar(5),
   	age int,
   	address varchar(32),
   	qq	varchar(20),
   	email varchar(50)
   );	
   ```

3. 列表查询设计：

   ![列表查询分析](https://raw.githubusercontent.com/hekun97/picture/main/img/列表查询分析.bmp)

## 开发

### 环境搭建

1. 创建数据库环境；
2. 创建项目，导入需要的jar包，如下：
   - JSP页面中JSTL标签库所需jar包：`javax.servlet.jsp.jstl.jar`、`jstl-impl.jar`；
   - MySQL的数据库驱动jar包：`mysql-connector-java-5.1.18-bin.jar`；
   - JDBCtemplate的jar包：`commons-logging-1.1.1.jar`、`spring-beans-4.2.4.RELEASE.jar`、`spring-core-4.2.4.RELEASE.jar`、`spring-jdbc-4.2.4.RELEASE.jar`、`spring-tx-4.2.4.RELEASE.jar`；
   - Duird数据库连接池所需jar包：`druid-1.0.9.jar`；
   - 用来把请求头数据封装到JavaBean·的jar包：`commons-beanutils-1.8.3.jar`；
   - 可作为Duird后续切换使用的c3p0数据库连接池jar包：`c3p0-0.9.1.2.jar`。
3. 导入资料文件中的页面文件夹内的HTML等文件。

### 编码

#### 简单功能

1. 列表查询
2. 登录
3. 添加
4. 删除
5. 修改

#### 复杂功能 

1. 删除选中
2. 分页查询
   * 好处：
     1. 减轻服务器内存的开销
     2. 提升用户体验
3. 复杂条件查询，带有分页的效果

## 测试

## 部署运维

