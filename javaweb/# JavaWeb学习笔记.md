# JavaWeb学习笔记  

## JDBC   
### JDBC简单介绍  
+ JDBC 就是使用Java语言操作关系型数据库的一套API
+ JDBC 就是使用Java语言操作关系型数据库的一套API
+ 全称:(Java DataBase Connectivity)Java 数据库连接


+ 各数据库厂商使用相同的接口，Java代码不需要针对不同数据库分别开发
+ 可随时替换底层数据库，访问数据库的Java代码基本不变

### JDBC的快速入门  

1. 步骤

   ![image-20250803124339623](image-20250803124339623.png)

```java
package com.jdbc;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

/**
 * @version 1.0
 * @auther Ryan
 */
public class JDBCDemo {
    public static void main(String[] args) throws Exception {
        //1.注册驱动
        Class.forName("com.mysql.jdbc.Driver");

        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        Connection conn = DriverManager.getConnection(url, username, password);

        //3. 定义SQL
        String sql = "UPDATE tbl_student SET NAME = \"Ryan\" WHERE ID = 100;";

        //4.获取执行sql的对象statement
        Statement stmt= conn.createStatement();

        //5.执行sql
        int count = stmt.executeUpdate(sql);//受影响的行数

        //6、处理结果
        System.out.println(count);

        //7.释放资源
        stmt.close();
        conn.close();
    }
}
```

### JDBC的API   
####  DriverManager  
1. 注册驱动
	+ MySQL5之后的驱动包，可以省略注册驱动的步骤
	+ 自动加载jar包中META-INF/services/ava.sql.Driver文件中的驱动类
2. 获取数据库连接
	+ url:如果连接的是本机mysqI服务器，并且mysq服务默认端口是3306，则ur可为-------jdbc:mysql:/数据库名称?参数键值对; 配置 useSSL=false 参数，禁用安全连接方式，解决警告提示
	+ user
	+ pws

#### Connection  
1. 获取执行 SQL 的对象
	+ 普通执行SQL对象 Statement createStatement()
	+ 预编译SQL的执行SQL对象: 防止SQL注入 PreparedStatement prepareStatement (sql)
	+ 执行存储过程的对象  CallableStatement prepareCall (sql)------不常用
2. 管理事务
	+ MySQL 事务管理 
		+ 开启事务:BEGIN;/START TRANSACTION:
		+ 提交事务:COMMIT;
		+ 回滚事务:ROLLBACK;
		+ MySQL默认自动提交事务
	+ JDBC 事务管理: Connection接口中定义了3个对应的方法
		+ 开启事务:setAutoCommit(boolean autoCommit):true为自动提交事务;false为手动提交事务，即为开启事务
		+ 提交事务:commit()
		+ 回滚事务:rollback()

```java
package com.jdbc;

import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

/**
 * @version 1.0
 * @auther Ryan
 */
//事务操作
public class Connection {
    public static void main(String[] args) throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");//MYSQL5之后的驱动jar包这行代码可以不写


        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        java.sql.Connection conn = DriverManager.getConnection(url, username, password);

        //3. 定义SQL
        String sql1 = "UPDATE tbl_student SET SEX = \"女\" WHERE ID = 101;";
        String sql2 = "UPDATE tbl_student SET SEX = \"女\" WHERE ID = 123;";

        //4.获取执行sql的对象statement
        Statement stmt= conn.createStatement();

        try {
            //开启事务
            conn.setAutoCommit(false);
            //5.执行sql1
            int count1 = stmt.executeUpdate(sql1);//受影响的行数
            //6、处理结果
            System.out.println(count1);

            //5.执行sql2
            int count2 = stmt.executeUpdate(sql2);//受影响的行数
            //6、处理结果
            System.out.println(count2);

            //提交事务
            conn.commit();
        } catch (Exception e) {
            //回滚事务
            conn.rollback();
            e.printStackTrace();
        }
        //7.释放资源
        stmt.close();
        conn.close();
    }
}
```

#### Statement  
1. 执行SQL语句
	+ int executeUpdate(sql):执行DML、DDL语句
	+ 返回值:(1)DML语句影响的行数 (2)DDL语句执行后，执行成功也可能返回0
	+ Result   SetexecuteQuery(sql):执行DQL语句
	+ 返回值: ResultSet结果集对象

```java
package com.jdbc;

import org.junit.Test;
import java.sql.Connection;
import java.sql.DriverManager;

/**
 * @version 1.0
 * @auther Ryan
 */
public class Statement {

    /*
    DML语句
     */

    @Test
    public void TestDML() throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");//MYSQL5之后的驱动jar包这行代码可以不写

        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        Connection conn = DriverManager.getConnection(url, username, password);

        //3. 定义SQL
        String sql = "UPDATE tbl_student SET NAME = \"Ysy\" WHERE ID = 101;";

        //4.获取执行sql的对象statement
        java.sql.Statement stmt= conn.createStatement();

        //5.执行sql
        int count = stmt.executeUpdate(sql);//执行完DML语句，受影响的行数

        //6、处理结果
        System.out.println(count);
        if(count >0){
            System.out.println("修改成功~");
        }else {
            System.out.println("修改失败~");
        }

        //7.释放资源
        stmt.close();
        conn.close();
    }

     /*
    DDL语句
     */

    @Test
    public void TestDDL() throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");//MYSQL5之后的驱动jar包这行代码可以不写

        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        Connection conn = DriverManager.getConnection(url, username, password);

        //3. 定义SQL
        String sql = "DROP DATABASE db2";

        //4.获取执行sql的对象statement
        java.sql.Statement stmt= conn.createStatement();

        //5.执行sql
        int count = stmt.executeUpdate(sql);//执行DDL语句，可能是0

        //6、处理结果
        //执行DDL语句，可能是0

        //7.释放资源
        stmt.close();
        conn.close();
    }



    public static void main(String[] args) {
        try {
            new Statement().TestDDL();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```


#### ResultSet  
1. 封装了DQL查询语句的结果
	+ ResultSet stmt.executeQuery(sql):执行DQL语句，返回 ResultSet 对象
	+ 获取查询结果
		+ boolean next():(1)将光标从当前位置向前移动一行(2)判断当前行是否为有效行返回值:
			true:有效行，当前行有数据
			false:无效行，当前行没有数据
			使用步骤:
			+ 游标向下移动一行，并判断该行否有数据:next()
			+ 获取数据:getXxx(参数)
			//循环判断游标是否是最后一行末尾
			while(rs.next()){
                //获取数据
                rs.getXxx(参数);
			}
		+ XXX getXxx(参数):获取数据
			XXX:数据类型; 如:int getInt(参数);String getString(参数)
			参数: int:列的编号，从1开始 ; String:列的名称
			
```java
package com.jdbc;

import org.junit.Test;

import java.sql.Connection;
import java.sql.DriverManager;

/**
 * @version 1.0
 * @auther Ryan
 */
public class ResultSet {
    @Test
    public void ResultSet_() throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");//MYSQL5之后的驱动jar包这行代码可以不写

        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        Connection conn = DriverManager.getConnection(url, username, password);

        //3. 定义SQL
        String sql = "SELECT * FROM tbl_student";

        //4.获取执行sql的对象statement
        java.sql.Statement stmt = conn.createStatement();

        //5.执行sql
        java.sql.ResultSet rs = stmt.executeQuery(sql);

        //6、处理结果、遍历rs中的所有数据
        //6.1 光标向下移动一行，并目判断当前行是否有数据
//        while (rs.next()) {
//            //6.2 获取数据 getxxx()
//            int id = rs.getInt(1);
//            String name = rs.getString(3);
//            System.out.println(id);
//            System.out.println(name);
//            System.out.println("==============");
//        }

        while (rs.next()) {
            //6.2 获取数据 getxxx()
            int id = rs.getInt("id");
            String name = rs.getString("name");
            System.out.println(id);
            System.out.println(name);
            System.out.println("==============");
        }

        //7.释放资源
        rs.close();
        stmt.close();
        conn.close();
    }
}
```

+ 需求:查询tbl_student 账户表数据，封装为Account对象中，并且存储到ArrayList集合中
```java
public void ResultSet_2() throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");//MYSQL5之后的驱动jar包这行代码可以不写

        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        Connection conn = DriverManager.getConnection(url, username, password);

        //3. 定义SQL
        String sql = "SELECT * FROM tbl_student";

        //4.获取执行sql的对象statement
        java.sql.Statement stmt = conn.createStatement();

        //5.执行sql
        java.sql.ResultSet rs = stmt.executeQuery(sql);

        //创建集合
        ArrayList<Object> list = new ArrayList<>();

        //6、处理结果、遍历rs中的所有数据
        //6.1 光标向下移动一行，并目判断当前行是否有数据

        while (rs.next()) {
            Account account = new Account();
            //6.2 获取数据 getxxx()
            int id = rs.getInt("id");
            String name = rs.getString("name");
            String pwd = rs.getString("password");

            //赋值
            account.setId(id);
            account.setName(name);
            account.setPassword(pwd);

            // 存入集合
            list.add(account);

            System.out.println(list);
        }

        //7.释放资源
        rs.close();
        stmt.close();
        conn.close();
    }
```

#### PreparedStatement  
1. PreparedStatement作用:
	+ 预编译SQL语句并执行:预防SQL注入问题
2. SQL注入
	+ SQL注入是通过操作输入来修改事先定义好的SQL语句，用以达到执行代码对服务器进行攻击的方法。

+ SQl注入演示
+ SELECT * FROM tbl_student WHERE name = '1234434' AND password = ' ' or '1' = '1'；
```java
package com.jdbc;

import org.junit.Test;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

/**
 * @version 1.0
 * @auther Ryan
 */
public class UserLogin {

    //用户登录
    @Test
    public void TestLogin() throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");//MYSQL5之后的驱动jar包这行代码可以不写

        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        Connection conn = DriverManager.getConnection(url, username, password);


        // 按收用户输入 用户名和密码
        String name = "Ysy";
        String pwd = "123";

        String sql = "SELECT * FROM tbl_student WHERE name = '"+ name + "' AND password = '"+ pwd +"'";

        //获取stmt对象
        Statement stmt =  conn.createStatement();

        // 执行sql
        ResultSet rs = stmt.executeQuery(sql);

        // 判断发录是否成功
        if(rs.next()){
            System.out.println("登录成功~");
        }else{
            System.out.println("登录失败~");
        }

        //登录成功~


        //7.释放资源
        stmt.close();
        conn.close();
    }

    //用户登录 SQL注入
    @Test
    public void TestLoginInject_() throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");//MYSQL5之后的驱动jar包这行代码可以不写

        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        Connection conn = DriverManager.getConnection(url, username, password);


        // 按收用户输入 用户名和密码
        String name = "1234434";
        String pwd = "' or '1' = '1";

        String sql = "SELECT * FROM tbl_student WHERE name = '"+ name + "' AND password = '"+ pwd +"'";

        System.out.println(sql);//SELECT * FROM tbl_student WHERE name = '1234434' AND password = '' or '1' = '1'
        //获取stmt对象
        Statement stmt =  conn.createStatement();

        // 执行sql
        ResultSet rs = stmt.executeQuery(sql);

        // 判断发录是否成功
        if(rs.next()){
            System.out.println("登录成功~");
        }else{
            System.out.println("登录失败~");
        }

        //登录成功~


        //7.释放资源
        stmt.close();
        conn.close();
    }
}
```


3. PreparedStatement作用:  预编译SQL并执行SQL语句
	1.  获取 PreparedStatement 对象
	+ SQL语句中的参数值，使用?占位符着代String sql ="select *from user where username = ? and password = ?"
	+ 通过Connection对象获取，并传入对应的sqIl语句PreparedStatement pstmt = copn.prepareStatement(sql);

	2. 设置参数值
	+ PreparedStatement对象:setXxx(参数1，参数2):给?赋值
	+ Xxx:数据类型;如 setlnt(参数1，参数2)参数:
	+ 参数1:?的位置编号，从1开始
	+ 参数2:?的值

	3. 执行SQL
	executeUpdate();/executeQuery();:不需要再传递sql 
	
```java
package com.jdbc;

import org.junit.Test;

import java.sql.*;
import java.sql.Connection;

/**
 * @version 1.0
 * @auther Ryan
 */
public class JDBCPreparedStatement {
    @Test
    public void TestLogin() throws Exception {
        //1.注册驱动
        //Class.forName("com.mysql.jdbc.Driver");//MYSQL5之后的驱动jar包这行代码可以不写

        //2.获取连接
        String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false";
        String username = "root";
        String password = "2000Ysy1028";
        Connection conn = DriverManager.getConnection(url, username, password);


        // 按收用户输入 用户名和密码
        String name = "Ysy";
        String pwd = "123";


        // 定义sql
        String sql="select * from tbl_student where name = ? and password = ?";

        //获取pstmt对象
        PreparedStatement pstmt = conn.prepareStatement(sql);

        // 设置?的值
        pstmt.setString(1,name);
        pstmt.setString(2,pwd);

        // 执行sql
        ResultSet rs = pstmt.executeQuery();

        // 判断发录是否成功
        if(rs.next()){
            System.out.println("登录成功~");
        }else{
            System.out.println("登录失败~");
        }

        //7.释放资源
        rs.close();
        pstmt.close();
        conn.close();
    }
}
```

##### PreparedStatement原理  
+ PreparedStatement 好处
    + 预编译SQL，性能更高
    + 防止SQL注入:将敏感字符进行转义

+ PreparedStatement预编译功能开启:  useServerPrepStmts=true
+ String url = "jdbc:mysql://localhost:3306/projectdemo?useSSL=false&useServerPrepStmts=true";

+ PreparedStatement 原理
    1. 在获取PreparedStatement对象时，将sql语发送给mysqI服务器进行检查，编译(这些步骤很耗时)
    2. 执行时就不用再进行这些步骤了，速度更快
    3. 如果sql模板一样，则只需要进行一次检查、编译
    ![image-20250804003914115](image-20250804003914115.png)

### 数据连接池  
1. 数据库连接池是个容器，负责分配、管理数据库连接(Connection)
2. 它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个;
3. 释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏
4. 好处:
    + 资源重用
    + 提升系统响应速度
    + 避免数据库连接遗漏

5. 数据库连接池实现
    + 标准接口:DataSource
    + 官方(SUN)提供的数据库连接池标准接口，由第三方组织实现此接口。
	+ 功能:获取连接
		Connection getConnection()
6. 常见的数据库连接池:
+ DBCP
+ C3P0
+ Druid

7. Druid(德鲁伊)
+ Druid连接池是阿里巴巴开源的数据库连接池项目
+ 功能强大，性能优秀，是Java语言最好的数据库连接池之一

8. Driud使用步骤
    1. 导入jar包 druid-1.1.12jar
    2. 定义配置文件
    3. 加载配置文件
    4. 获取数据库连接池对象
    5. 获取连接

```java
package com.druid;

import com.alibaba.druid.pool.DruidDataSourceFactory;

import javax.sql.DataSource;
import java.io.FileInputStream;
import java.sql.Connection;
import java.sql.SQLException;
import java.util.Properties;

/**
 * @version 1.0
 * @auther Ryan
 */
//Druid数据库连按池演示
public class DruidDemo {
    public static void main(String[] args) throws Exception {
        //1.导入jar包

        //2.定义配置文件

        //查找路径小方法
        System.out.println(System.getProperty("user.dir"));
        //C:\Users\Administrator\IdeaProjects\JDBC

        //3.加载配置文件
        Properties prop = new Properties();
        prop.load(new FileInputStream("src/druid.properties"));

        //4.获取连接池对象
        DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);

        //5.获取数据库连按 Connection
        Connection connection=dataSource.getConnection();

        System.out.println(connection);

    }
}
```

## Maven  
### Maven入门   
1. Maven是专门用于管理和构建Java项目的工具，它的主要功能有
    + 提供了一套标准化的项目结构
    	+ Maven提供了一套标准化的项目结构，所有IDE使用Maven构建的项目结构完全一样，所有IDE创建的Maven项目可以通用
    + 提供了一套标准化的构建流程(编译，测试，打包，发布.)
    	+ Maven提供了一套简单的命令来完成项目构建
    + 提供了一套依赖管理机制
    	+ 依赖管理其实就是管理你项目所依赖的第三方资源(jar包、插件...)

### Maven介绍  
1. Apache Maven是一个项目管理和构建工具，它基于项目对象模型(POM)的概念，通过一小段描述信息来管理项目的构建、报告和文档

2. 官网:http://maven.apache.org/

3. ![image-20250804212519870](image-20250804212519870.png)

4. 仓库分类
+ 本地仓库:自己计算机上的一个目录
+ 中央仓库:由Maven团队维护的全球唯一的仓库: https://repo1.maven.org/maven2/
+ 远程仓库(私服):一般由公司团队搭建的私有仓库

5. 当项目中使用坐标引入对应依赖jar包后，首先会查找本地仓库中是否有对应的jar包:如果有，则在项目直接引用；  如果没有，则去中央仓库中下载对应的jar包到本地仓库。
6. 还可以搭建远程仓库，将来jar包的查找顺序则变为:本地仓库 →远程仓库 → 中央仓库

### Maven的安装配置  
1. 解压 apache-maven-3.6.1.rar 既安装完成
2. 2.配置环境变量 MAVEN HOME 为安装路径的bin目录配置本地仓库
3. 修改 conf/settings.xml中的< localRepository >为一个指定目录配置阿里云私服
4. 修改 conf/settings.xml中的< mirrors >标签，为其添加如下子标签:

```html
<mirror>
<id>alimaven</id>
<name>aliyun maven</name><url>http://maven.aliyun.com/nexus/content/groups/public/</url><mirrorOf>central</mirrorOf>
</mirror>
```

### Maven 常用命令  
1. compile :编译
2. clean:清理
3. test:测试
4. package:打包
5. install:安装

### Maven的生命周期  
+ Maven 构建项目生命周期描述的是一次构建过程经历经历了多少个事件
+ Maven 对项目构建的生命周期划分为3套
    + clean:清理工作
    + default:核心工作，例如编译，测试，打包，安装等
    	+ 同一生命周期内，执行后边的命令，前边的所有命令会自动执行
    + site:产生报告，发布站点等
    
    ![image-20250804215821898](image-20250804215821898.png)

### IDEA 配置 Maven  
#### IDEA 配置 Maven 环境  
![image-20250804220156450](image-20250804220156450.png)

#### Maven 坐标详解  
1. 什么是坐标?
+ Maven 中的坐标是资源的唯一标识
+ 使用坐标来定义项目或引入项目中需要的依赖
2. Maven 坐标主要组成
+ groupld:定义当前Maven项目隶属组织名称(通常是域名反写，例如:com.itheima)
+ artifactld:定义当前Maven项目名称(通常是模块名称，例如 order-service、goods-service)
+ version:定义当前项目版本号

#### IDEA 创建 Maven 项目  

![image-20250804220318494](image-20250804220318494.png)

#### IDEA 导入 Maven 项目  

![image-20250804220551856](image-20250804220551856.png)

![image-20250804221154690](image-20250804221154690.png)


### 依赖管理  

![image-20250804221324381](image-20250804221324381.png)

1. 使用坐标导入jar包 
+ 在 pom.xml 中编写< dependencies >标签

+ 在< dependencies >标签中 使用<dependency>引入坐标

+ 定义坐标的 groupld，artifactld，version

+ 点击刷新按钮，使坐标生效

![image-20250804221650947](image-20250804221650947.png)

2. 依赖范围 
+ 通过设置坐标的依赖范围(scope)，可以设置 对应jar包的作用范围:编译环境、测试环境、运行环境

![image-20250804224834718](image-20250804224834718.png)

## MyBatis  
+ MyBatis 是一款优秀的持久层框架，用于简化 JDBC 开发
1. 持久层
+ 负责将数据保存到数据库的那一层代码
+ JavaEE三层架构:表现层、业务层、持久层
2. 框架
+ 框架就是一个半成品软件，是一套可重用的、通用的、软件基础代码模型
+ 在框架的基础之上构建软件编写更加高效、规范、通用、可扩展

### MyBatis 简单介绍  
1. JDBC 缺点
+ 硬编码
	+ 注册驱动，获取连接
	+ SQL 语句
+ 操作繁琐
	+ 手动设置参数
	+ 手动封装结果集

2. MyBatis的好处
+ MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作

### MyBatis 快速入门  
1. 创建user表，添加数据
2. 创建模块，导入坐标
3. 编写 MyBatis 核心配置文件 -->替换连接信息 解决硬编码问题
```xml

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
<!--                数据库的连接信息----- 以下信息需要修改-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql:///mybatis?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="2000Ysy1028"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
    <!--        加载sql的映射文件-->
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```
4. 编写 SQL映射文件 -->统一管理sql语句，解决硬编码问题
```xml

```
5. 编码
	1. 定义P0J0类
	2. 加载核心配置文件，获取 SqlSessionFactory 对象
	3. 获取 SqlSession 对象，执行 SQL语句
	4. 释放资源
```java
package org.javaweb.Mybatis;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.javaweb.pojo.User;

import java.io.InputStream;
import java.util.List;

/**
 * @version 1.0
 * @auther Ryan
 */
public class Demo {
    //Mybaits快速入门 
    public static void main(String[] args) throws Exception {
        //1.加载mybatis的核心配置文件，获SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);


        //2.获取Sqlsession对象，用它执行
        SqlSession sqlSession = sqlSessionFactory.openSession();

        //3.执行sql
        List<User> users = sqlSession.selectList("test.selectAll");

        System.out.println(users);

        //4。释放资源
        sqlSession.close();


    }
}
```


### Mapper 代理开发  
+ 解决原生方式中的硬编码
+ 简化后期执行SQL

1. 定义与SQL映射文件同名的Mapper接口，并且将Mapper接口和SQL映射文件放置在同一目录下（要用斜杠/作为分隔符）
2. 设置SQL映射文件的namespace属性为Mapper接口全限定名
3. 在 Mapper 接口中定义方法，方法名就是SQL映射文件中sql语句的id，并保持参数类型和返回值类型一致
4. 编码
	1. 通过 SqlSession 的 getMapper方法获取 Mapper接囗的代理对象
	2. 调用对应方法完成sql的执行

```JAVA
package org.javaweb.Mybatis;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.javaweb.mapper.UserMapper;
import org.javaweb.pojo.User;

import java.io.InputStream;
import java.util.List;

/**
 * @version 1.0
 * @auther Ryan
 */
//代理开发
public class Demo2 {
    public static void main(String[] args) throws Exception{
        //1.加载mybatis的核心配置文件，获SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);


        //2.获取Sqlsession对象，用它执行
        SqlSession sqlSession = sqlSessionFactory.openSession();

        //3.执行sql
//        List<User> users = sqlSession.selectList("test.selectAll");
        //3.1 获xUserMapper按日的代理对象
        UserMapper userMapper =sqlSession.getMapper(UserMapper.class);
        List<User> users = userMapper.selectAll();
        System.out.println(users);

        //4。释放资源
        sqlSession.close();
    }
}
```

+ 细节:如果Mapper接口名称和SQL映射文件名称相同，并在同一目录下，则可以使用包扫描的方式简化SQL映射文件的加载

```xml
<mappers>
<!--        加载sql的映射文件-->
<!--        <mapper resource="org/javaweb/mapper/UserMapper.xml"/>-->
<!--        Mapper代理方式  推荐-->
        <package name="org.javaweb.mapper"/>
    </mappers>
```

### MyBatis 核心配置文件   
+ environments:配置数据床连按环境信息。可以配置多个environment，通过default属性切换不同的environment
+ typeAliases 起别名 写在文件前边
+ https://mybatis.p2hp.com/configuration.html
```xml
<typeAliases>
<package name="com.itheima.pojo"/></typeAliases>
```

### 配置文件完成增删改查   
+ 安装MyBatisX 
####  查询所有  
1. 编写接口方法: Mapper接口 参数:无 结果:List< Brand >
2. 编写 SQL语句: SQL映射文件
3. 执行方法，测试
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!--
    namespace:名称空间
-->

<mapper namespace="org.javaweb.mapper.BrandMapper">

<!--    数据库表的字段名称和实体类的属性名称不一样，则不能自动封装数据 -->
<!--    -->
<!--    <select id="selectAll" resultType="brand">-->
<!--        select *-->
<!--        from tb_brand;-->
<!--    </select>-->

    <!-- 起别名 对不一样的列名起别名，让别名和实体类的属性名一样 缺点:每次查询都要定义一次别名 -->
<!--        <select id="selectAll" resultType="brand">-->
<!--            select id, brand_name as brandName, company_name as companyName, ordered,  description,status-->
<!--            from tb_brand;-->
<!--        </select>-->

    <!--  sql片段  不灵活 -->
<!--    <sql id="brand_column" >-->
<!--        id, brand_name as brandName, company_name as companyName, ordered,  description, status-->
<!--    </sql>-->
<!--    <select id="selectAll" resultType="brand">-->
<!--        select-->
<!--            <include refid="brand_column"/>-->
<!--        from tb_brand;-->
<!--    </select>-->


    <!--    resultMap: 最常用-->
    <resultMap id="brandResultMap" type="brand">
        <result column="brand_name" property="brandName"/>
        <result column="company_name" property="companyName"/>
    </resultMap>
    <!--    id:完成主键字段的映射-->
    <!--    column:表的列名-->
    <!--    property:实体类的属性名-->
    <!--    -->
    <!--    result:完成一般字段的映射-->
    <!--    column:表的列名-->
    <!--    property:实体类的属性名-->
    <select id="selectAll" resultMap="brandResultMap">
        select
        *
        from tb_brand;
    </select>
</mapper>
```

#### 查看详情   
1. 编写接口方法:Mapper接口 参数:id 结果:Brand
2. 编写 SQL语句: SQL映射文件
3. 执行方法，测试

```xml
    <!--    参数占位符-->
    <!--    #{}: 会将其替换为 ? ，为了防止SQL注入-->
    <!--    ${}: 拼sql。 会存在SQL注入问题-->
<!--    使用时机:-->
<!--        参数传递的时候使用#{}-->
<!--        表明或者列名不固定的时候用${} 会存在sql注入问题-->
<!--    特殊字符处理:-->
<!--        1. 转义:-->
<!--        2. CDATA区: 输入 CD-->
    <select id="selectById" resultMap="brandResultMap">
        select
        *
        from tb_brand
        where id = #{id};
    </select>
```

#### 条件查询  
1. 多条件查询
    1. 编写接口方法: Mapper接口 参数:所有 查询条件结果:List< Brand >
    2. 编写 SQL语句: SQL映射文件
    3. 执行方法，测试

+ SQL语句设置多个参数有几种方式
+ 散装参数:需要使用@Param(“sal中的参数占位符名称”)
+ 实体类封装参数只需要保证SQL中的参数名 和 实体类属性名对应上，即可设置成功 
+ map集合只需要保证SQL中的参数名 和 map集合的键的名称对应上，即可设置成功

```xml
<select id="selectByCondition" resultMap="brandResultMap">
        select *
        from tb_brand
        where status = #{status}
        and company_name like #{companyName}
        and brand_name like #{brandName};
    </select>
```


2. 多条件查询-动态条件查询
+ SQL语句会随着用户的输入或外部分条件的变化而变化，我们称为动态SQL

```xml
<!--    动态条件查询
        * if:条件判断
        * test:逻辑表达式
            * 问题：
            * 恒等式 where 1 = 1
            * <where>替换 where 关键字

-->
    <select id="selectByCondition" resultMap="brandResultMap">
        select *
        from tb_brand
<!--        where 1 = 1-->
        <where>
            <if test="status != null">
                and where status = #{status}
            </if>
            <if test="companyName != null and companyName != '' ">
                and company_name like #{companyName}
            </if>
            <if test="brandName != null and brandName != '' ">
                and brand_name like #{brandName};
            </if>
        </where>
    </select>
```

3. 单条件查询-动态条件查询
+ choose(when,otherwise):选，类似于Java 中的 switch 语句
```xml
<!--    <select id="selectByConditionSingle" resultMap="brandResultMap">-->
<!--        select-->
<!--        *-->
<!--        from tb_brand-->
<!--        where-->
<!--        <choose> &lt;!&ndash; switch &ndash;&gt;-->
<!--            <when test="status != null">&lt;!&ndash; case &ndash;&gt;-->
<!--                status = #{status}-->
<!--            </when>-->
<!--            <when test="companyName != null and companyName != '' ">&lt;!&ndash; case &ndash;&gt;-->
<!--                company_name like #{companyName}-->
<!--            </when>-->
<!--            <when test="brandName != null and brandName != '' ">&lt;!&ndash; case &ndash;&gt;-->
<!--                brand_name like #{brandName};-->
<!--            </when>-->
<!--            <otherwise>&lt;!&ndash; deflaut &ndash;&gt;-->
<!--                1 = 1-->
<!--            </otherwise>-->
<!--        </choose>-->
<!--    </select>-->

    <select id="selectByConditionSingle" resultMap="brandResultMap">
        select
        *
        from tb_brand
        <where>
            <choose> <!-- switch -->
                <when test="status != null"><!-- case -->
                    status = #{status}
                </when>
                <when test="companyName != null and companyName != '' "><!-- case -->
                    company_name like #{companyName}
                </when>
                <when test="brandName != null and brandName != '' "><!-- case -->
                    brand_name like #{brandName};
                </when>
            </choose>
        </where>
    </select>
```

#### 添加  
1. 编写接口方法: Mapper接口  参数:除了id之外的所有数据  结果:void
2. 编写 SQL语句: SQL映射文件
3. 执行方法，测试
MyBatis事务:
openSession(:默认开启事务，进行增删改操作后需要使用sqlSession.commit0; 手动提交事务openSession(true):可以设置为自动提交事务(关闭事务)

```xml
<insert id="add">

        insert into tb_brand(brand_name, company_name, ordered, description, status)
        values (#{brandName},#{companyName},#{ordered},#{description},#{ status})
        
</insert>
```

#### 添加 --- 主键返回  
+ 在数据添加成功后，需要获取插入数据库数据的主键的值>比如:添加订单和订单项
1. 添加订单
2. 添加订单项，订单项中需要说置所属订单的id

```xml
<insert useGeneratedKeys="true" keyProperty="id">
```

#### 修改全部字段   
1. 编写接口方法: Mapper接口 参数:所有数据  结果:void
2. 编写 SQL语句: SQL映射文件
3. 执行方法，测试

```xml
<update id="update">
        update tb_brand
        set
            brand_name = #{brandName},
            company_name = #{companyName},
            ordered = #{ordered},
            description = #{description},
            status = #{status}
        where id = #{id};
    </update>
```

####   修改动态字段  
1. 编写接口方法: Mapper接口  参数:部分数据，封装到对象中结果:void 
2. 编写 SQL语句: SQL映射文件
3. 执行方法，测试

```xml
<update id="update2">
        update tb_brand
        <set>
            <if test="brandName != null and brandName != '' ">
                brand_name = #{brandName},
            </if>
            <if test="companyName != null and companyName != '' ">
                company_name = #{companyName},
            </if>
            <if test="ordered != null">
                ordered = #{ordered},
            </if>
            <if test="description != null and description != ''">
                description = #{description},
            </if>
            <if test="status != null">
                status = #{status}
            </if>
        </set>
        where id = #{id};
    </update>
```
#### 删除一个  
1. 编写接口方法: Mapper接口  参数:id  结果:void
2. 编写 SQL语句: SQL映射文件
3. 执行方法，测试

```xml
<delete id="delete">
        delete from tb_brand where id = #{id};
    </delete>
```

#### 批量删除  
1. 编写接口方法: Mapper接口  参数:id  数组结果:void
2. 编写 SQL语句: SQL映射文件
3. 执行方法，测试

```xml
<!--mybatis会将数组参数，封装为一个Map集合:
    *默认:array =数组
    *使用@Param注解改变map集合的默认key的名称-->
    <delete id="deleteByIds">
        delete from tb_brand where id
        in
            <foreach collection="ids" item="id" separator="," open="(" close=")">
                #{id}
            </foreach>
            ;
    </delete>
```

### 参数传递  
+ MyBatis 接口方法中可以接收各种各样的参数，MyBatis底层对于这些参数进行不同的封装处理方式
    + 单个参数:
        1. POJO类型: 直接使用，属性名 和 参数占位符名称一致
        2. Map集合:  直接使用，属性名 和 参数占位符名称一致
        3. Collection： 封装为Map集合 可以使用@Param注解，替换Map集合中默认的arg键名
        	map.put("argo",collection集合);
        	map.put("collection",collection集合);
        4. List:  封装为Map集合 可以使用@Param注解，替换Map集合中默认的arg键名
        	map.put("arg0",list集合)
        	map.put("collection",list集合);
        	map.put("list".list集合)
        5. Array:  封装为Map集合 可以使用@Param注解，替换Map集合中默认的arg键名
        	map.put("arg",数组);
        	map.put("array",数组);
        6. 其他类型:  直接使用，属性名 和 参数占位符名称一致
    + 多个参数: 封装为Map集合  可以使用@Param注解，替换Map集合中默认的arg键名  
    	map.put("arg0",参数值1)
    	map.put("param1",参数值1)
    	map.put("agr1",参数值2)
    	map.put("param2",参数值2)
    	——————————————@Param("username") 建议使用 
    	map.put("username",参数值1)
    	map.put("param1",参数值1)
    	map.put("agr1",参数值2)
    	map.put("param2",参数值2)
    
+ MyBatis提供了 ParamNameResolver  类来进行参数封装
+ 建议:将来都使用@Param注解来修改Map集合中默认的键名并使用修改后的名称来获取值，这样可读性更高!

### 注解完成增删改查  
+ 使用注解开发会比配置文件开发更加方便
+ ![image-20250807004031265](image-20250807004031265.png)

## HTTP   
+ HyperText Transfer Protocol，超文本传输协议，规定了浏览器和服务器之间数据传输的规则。
+ HTTP 协议特点:
    1. 基于TCP协议:面向连接，安全
    2. 基于请求-响应模型的:一次请求对应一次响应
    3. HTTP协议是无状态的协议:对于事务处理没有记忆能力。每次请求-响应都是独立的。
        缺点:多次请求间不能共享数据。
        优点:速度快

### HTTP-请求数据格式  
+ 请求数据分为3部分
1. 请求行:请求数据的第一行。其中GET表示请求方式，/表示请求资源路径，HTTP/1.1表示协议版本
2. 请求头:第二行开始，格式为key:value形式。
3. 请求体: POST请求的最后一部分，存放请求参数

4. GET请求和 POST请求区别:
+ GET请求请求参数在请求行中，没有请求体。POST请求请求参数在请求体中
+ ![image-20250807133340589](image-20250807133340589.png)
+ ![image-20250807133431575](image-20250807133431575.png)
+  GET请求请求参数大小有限制，POST没有

### HTTP-响应数据格式  
+ 响应数据分为3部分:
1. 响应行:响应数据的第一行。其中HTTP/1.1表示协议版1.本，200表示响应状态码，OK表示状态码描述
2. 响应头:第二行开始，格式为key:value形式 
	常见的HTTP 响应头:
	+ Content-Type:表示该响应内容的类型，例如text/htmIimage/jpeg;
	+ Content-Length:表示该响应内容的长度(字节数)
	+ Content-Encoding:表示该响应压缩算法，例如gzip;
	+ Cache-Control:指示客户端应如何缓存，例如max-age=300表示可以最多缓存300秒
3. 响应体: 最后一部分。存放响应数据

+ 状态码大类 
| 状态码分类 | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| 1xx        | **响应中**——临时状态码，表示请求已经接受，告诉客户端应该继续请求或者如果它已经完成则忽略它 |
| 2xx        | **成功**——表示请求已经被成功接收，处理已完成                 |
| 3xx        | **重定向**——重定向到其它地方：它让客户端再发起一个请求以完成整个处理。 |
| 4xx        | **客户端错误**——处理发生错误，责任在客户端，如：客户端的请求一个不存在的资源，客户端未被授权，禁止访问等 |
| 5xx        | **服务器端错误**——处理发生错误，责任在服务端，如：服务端抛出异常，路由出错，HTTP版本不支持等 |

状态码大全：https://cloud.tencent.com/developer/chapter/13553 

+ 常见的响应状态码

| 状态码  | 英文描述                               | 解释                                                         |
| ------- | -------------------------------------- | ------------------------------------------------------------ |
| **200** | **`OK`**                               | 客户端请求成功，即**处理成功**，这是我们最想看到的状态码     |
| 302     | **`Found`**                            | 指示所请求的资源已移动到由`Location`响应头给定的 URL，浏览器会自动重新访问到这个页面 |
| 304     | **`Not Modified`**                     | 告诉客户端，你请求的资源至上次取得后，服务端并未更改，你直接用你本地缓存吧。隐式重定向 |
| 400     | **`Bad Request`**                      | 客户端请求有**语法错误**，不能被服务器所理解                 |
| 403     | **`Forbidden`**                        | 服务器收到请求，但是**拒绝提供服务**，比如：没有权限访问相关资源 |
| **404** | **`Not Found`**                        | **请求资源不存在**，一般是URL输入有误，或者网站资源被删除了  |
| 428     | **`Precondition Required`**            | **服务器要求有条件的请求**，告诉客户端要想访问该资源，必须携带特定的请求头 |
| 429     | **`Too Many Requests`**                | **太多请求**，可以限制客户端请求某个资源的数量，配合 Retry-After(多长时间后可以请求)响应头一起使用 |
| 431     | **` Request Header Fields Too Large`** | **请求头太大**，服务器不愿意处理请求，因为它的头部字段太大。请求可以在减少请求头域的大小后重新提交。 |
| 405     | **`Method Not Allowed`**               | 请求方式有误，比如应该用GET请求方式的资源，用了POST          |
| **500** | **`Internal Server Error`**            | **服务器发生不可预期的错误**。服务器出异常了，赶紧看日志去吧 |
| 503     | **`Service Unavailable`**              | **服务器尚未准备好处理请求**，服务器刚刚启动，还未初始化好   |
| 511     | **`Network Authentication Required`**  | **客户端需要进行身份验证才能获得网络访问权限**               |

## Web 服务器-Tomcat   
+ Web服务器是一个应该程序(软件)，对HTTP协议的操作进行封装，使得程序员不必直接对协议进行操作，让web开发更加便捷。主要功能是“提供网上信息浏览服务“。
+ 概念: Tomcat是Apache 软件基金会一个核心项目，是一个开源免费的轻量级Web服务器，支持Servlet/JSP少量JavaEE规范。
+ Tomcat 也被称为 Web容器、Servlet容器。Servlet 需要依赖于 Tomcat才能运行

### Tomcat基本使用  
1. Web 服务器作用?
+ 封装HTTP协议操作，简化开发
+ 可以将web项目部署到服务器中，对外提供网上浏览服务
2. Tomcat是一个轻量级的Web服务器，支持Servlet/JSP少量JavaEE规范，也称为Web容器，Servlet容器

![image-20250807150520990](image-20250807150520990.png)

![image-20250807151252082](image-20250807151252082.png)![image-20250807151259639](image-20250807151259639.png)

### IDEA中创建 Maven Web项目  






