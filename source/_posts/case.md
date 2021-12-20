# 需求

通过用户信息列表展示，对用户信息的增删改查等操作。

# 设计

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

# 开发

## 环境搭建

<a href="#需求">点击跳转</a>

1. 创建数据库环境；
2. 创建项目，导入需要的jar包，如下：
   - JSP页面中JSTL标签库所需jar包：`javax.servlet.jsp.jstl.jar`、`jstl-impl.jar`；
   - MySQL的数据库驱动jar包：`mysql-connector-java-5.1.18-bin.jar`；
   - JDBCtemplate的jar包：`commons-logging-1.1.1.jar`、`spring-beans-4.2.4.RELEASE.jar`、`spring-core-4.2.4.RELEASE.jar`、`spring-jdbc-4.2.4.RELEASE.jar`、`spring-tx-4.2.4.RELEASE.jar`；
   - Duird数据库连接池所需jar包：`druid-1.0.9.jar`；
   - 用来把请求头数据封装到JavaBean·的jar包：`commons-beanutils-1.8.3.jar`；
   - 可作为Duird后续切换使用的c3p0数据库连接池jar包：`c3p0-0.9.1.2.jar`。
3. 导入资料文件中的页面文件夹内的HTML等文件。

## 编码

### 简单功能

1. 列表查询
2. 登录
3. 添加
4. 删除
5. 修改

### 复杂功能 

#### 删除选中



#### 分页查询

##### 好处

1. 可以减轻服务器内存的开销（每次只从服务器中查询少量的数据进行展示）；
2. 提升用户体验（等待时间少，方便查看数据）。

##### 分析实现的步骤

###### 输入、输出数据	

确认好分页功能需要的输入和输出数据。

- 输入数据

  1. currentPage：提供当前页码给服务器；
  2. rows：提供每页显示条数给服务器。

- 输出数据：封装到pageBean中

  1. `int totalCount`：服务器中的总记录数；

  2. `int totalPage`：总页码；

     > 计算公式：`总页码 = 总记录数%每页显示条数 == 0 ? 总记录数 / 每页显示条数 ：总记录数 / 每页显示条数 + 1`；

  3. `List<User> list`：每页的数据，返回List集合；

     > 查询SQL：`select  * from user limit ? , ?`。第一个?：`开始查询的索引 = (currentPage-1) * rows`；第二个?：`rows`。

  4. `int currentPage`：当前页码；

  5. `int rows`：每页显示的条数。

![7.分页查询功能](https://raw.githubusercontent.com/hekun97/picture/main/img/7.%E5%88%86%E9%A1%B5%E6%9F%A5%E8%AF%A2%E5%8A%9F%E8%83%BD.bmp)

###### 实现逻辑

实现逻辑如下图：

![7_2.分页查询功能2](https://raw.githubusercontent.com/hekun97/picture/main/img/7_2.分页查询功能2.bmp)

##### 代码实现

1. FindUserByPageServlet

   ```java
   package io.gitee.hek97.web.servlet;
   //省略导包代码
   @WebServlet("/findUserByPageServlet")
   public class FindUserByPageServlet extends HttpServlet {
       protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           //1.设置编码
           request.setCharacterEncoding("utf-8");
           //2.获取请求参数
           String currentPage = request.getParameter("currentPage");
           String rows = request.getParameter("rows");
           //3.调用service查询到pageBean
           UserService service = new UserServiceImpl();
           PageBean<User> pb = service.findUserByPage(currentPage, rows);
           //4.存入request域中
           request.setAttribute("pb", pb);
           System.out.println(pb);
           //5.请求转发页面到list.jsp
           request.getRequestDispatcher("/list.jsp").forward(request, response);
       }
       protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           this.doPost(request, response);
       }
   }
   ```

2. UserService的实现类UserServiceImp

   ```java
   package io.gitee.hek97.service.impl;
   //省略导包和其它方法，只保留分页逻辑的方法
   public class UserServiceImpl implements UserService {
   	public PageBean<User> findUserByPage(String _currentPage, String _rows) {
           int currentPage = Integer.parseInt(_currentPage);
           int rows = Integer.parseInt(_rows);
           //1.创建空的PageBean对象
           PageBean<User> pb = new PageBean<>();
           //2.给PageBean对象的当前页码(currentPage)和每页显示的条数(rows)赋值
           pb.setCurrentPage(currentPage);
           pb.setRows(rows);
           //3.调用dao查询总记录数totalCount，并给PageBean对象的总记录数(totalCount)赋值
           int totalCount = dao.findTotalCount();
           pb.setTotalCount(totalCount);
           //4.计算开始的索引
           int start = (currentPage - 1) * rows;
           // 5.调用dao查询每页数据的list集合，并赋值给pageBean对象
           List<User> list = dao.findByPage(start, rows);
           pb.setList(list);
           //6.计算总页数,并赋值给pageBean对象
           int totalPage = (totalCount % rows == 0) ? (totalCount / rows) : (totalCount / rows + 1);
           pb.setTotalPage(totalPage);
           //7.返回pageBean对象
           return pb;
       }
   }
   ```

3. UserDao的实现类UserDaoImp

   ```java
   package io.gitee.hek97.dao.impl;
   //省略导包和其它方法，只保留分页逻辑中需操纵数据库的方法
   public class UserDaoImpl implements UserDao {
       //使用JDBC操作数据库
       //1.创建JdbcTemplate对象
       JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
       //查找总记录数的方法
      @Override
       public int findTotalCount() {
           //2.定义sql语句
           String sql = "select count(*) from user";
           //3.执行sql
           Integer count = template.queryForObject(sql, Integer.class);
           return count;
       }
   	//根据开始索引和每页记录数查询当前页码
       @Override
       public List<User> findByPage(int start, int rows) {
           //2.定义sql语句
           String sql = "select * from user limit ? , ?";
           //3.执行sql
           List<User> list = template.query(sql, new BeanPropertyRowMapper<User>(User.class), start, rows);
           return list;
       }
   }
   ```

   

#### 复杂条件查询

带有分页的效果

## 测试

## 部署运维

