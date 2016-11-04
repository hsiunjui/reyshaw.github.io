---
title: DAO查询
date: 2016-11-04 15:56:08
tags: 数据
categories: 案例
---
>DAO查询示例。

<!--more-->
# 数据库脚本
```mysql
    DROP TABLE IF EXISTS user ;
    CREATE TABLE user(
    id	 int(4) primary key,
    name	 VARCHAR(10)
    ) ;
    INSERT INTO user (id,name) VALUES (1,'test01') ;
    INSERT INTO user (id,name) VALUES (2,'test03') ;
    INSERT INTO user (id,name) VALUES (3,'test03') ; 
```

# java Bean设计(User.java)
```java
    package cn.xr.vo ;
    public class User {
        private int id ;
        private String name ;
        public void setId(int id){
            this.id = id ;
        }
        public void setName(String name){
            this.name = name ;
        }
        public int getId(){
            return this.id ;
        }
        public String getName(){
            return this.name ;
        }
    }
```   

# 数据库了连接创建(DBConnection.java)
```java
    package cn.xr.dbc ;
    import java.sql.Connection ;
    import java.sql.DriverManager ;
    public class DBConnection {
        private static final String DBDRIVER = "org.gjt.mm.mysql.Driver" ; 
        private static final String DBURL = "jdbc:mysql://localhost:3306/test" ;
        private static final String DBUSER = "root" ;
        private static final String DBPASSWORD = "" ;
        private Connection conn ;
        public DBConnection() throws Exception {
            Class.forName(DBDRIVER) ;
            this.conn = DriverManager.getConnection(DBURL,DBUSER,DBPASSWORD) ;
        }
        public Connection getConnection(){
            return this.conn ;
        }
        public void close() throws Exception {
            if(this.conn != null){
                try{
                    this.conn.close() ;
                }catch(Exception e){
                    throw e ;
                }
            }
        }
    }
```
# DAO接口设计(IUserDAO.java)
```java
    package cn.xr.dao ;
    import java.util.* ;
    import cn.xr.vo.* ;
    public interface IUserDAO {
	    public List<User> findAll(String keyWord) throws Exception ;
    }
```

# DAO实现类(UserDAOImpl.java)
```java
    package cn.xr.dao.impl ;
    import java.util.* ;
    import java.sql.* ;
    import cn.xr.dao.* ;
    import cn.xr.vo.* ;
    public class UserDAOImpl implements IUserDAO {
        private Connection conn = null ;
        private PreparedStatement pstmt = null ;
        public UserDAOImpl(Connection conn){
            this.conn = conn ;
        }
        public List<User> findAll(String keyWord) throws Exception{
            List<User> all = new ArrayList<User>() ;
            String sql = "SELECT id,name FROM user WHERE id LIKE ? OR name LIKE ?" ;
            this.pstmt = this.conn.prepareStatement(sql) ;
            this.pstmt.setString(1,"%"+keyWord+"%") ;
            this.pstmt.setString(2,"%"+keyWord+"%") ;
            ResultSet rs = this.pstmt.executeQuery() ;
            User user = null ;
            while(rs.next()){
                user = new User() ;
                user.setId(rs.getInt(1)) ;
                user.setName(rs.getString(2)) ;
                all.add(user) ;
            }
            this.pstmt.close() ;
            return all ;
        }
    } 
 ```
 
# 代理类(UserDAOProxy.java)
```java
    package cn.xr.dao.proxy ;
    import java.util.* ;
    import java.sql.* ;
    import cn.xr.dao.* ;
    import cn.xr.dbc.* ;
    import cn.xr.dao.impl.* ;
    import cn.xr.vo.* ;
    public class UserDAOProxy implements IUserDAO {
        private DBConnection dbc = null ;
        private IUserDAO dao = null ;
        public UserDAOProxy() throws Exception {
            this.dbc = new DBConnection() ;
            this.dao = new UserDAOImpl(this.dbc.getConnection()) ;
        }
        public List<User> findAll(String keyWord) throws Exception{
            List<User> all = null ;
            try{
                all = this.dao.findAll(keyWord) ;
            }catch(Exception e){
                throw e ;
            }finally{
                this.dbc.close() ;
            }
            return all ;
        }
    } 
```

# 工厂方法(DAOFactory.java)
```java
     package cn.xr.factory ;
    import cn.xr.dao.IUserDAO ;
    import cn.xr.dao.proxy.UserDAOProxy ;
    public class DAOFactory {
        public static IUserDAO getIUserDAOInstance() throws Exception{
            return new UserDAOProxy() ;
        }
    }
```

# 展示页面(test.jsp)
```jsp
<%@ page contentType="text/html" pageEncoding="GBK"%>
<%@ page import="cn.xr.factory.*,cn.xr.vo.*"%>
<%@ page import="java.util.*"%>
<html>
	<head>
		<title>DAO Example</title>
	</head>
<%		request.setCharacterEncoding("GBK") ;	%>
	<body>
<%
		String keyWord = request.getParameter("kw") ;
		if(keyWord == null){
			keyWord = "" ;
		}
		List<User> all = DAOFactory.getIUserDAOInstance().findAll(keyWord) ;
		Iterator<User> iter = all.iterator() ;
%>
		<center>
			<form action="test.jsp" method="post">
				Please input keywords:<input type="text" name="kw">
				<input type="submit" value="CHECK">
			</form>
			<table border="1" width="50%"> 
			<tr>
				<td>ID</td>
				<td>NAME</td>
			</tr>
<%
			while(iter.hasNext()){
				User user = iter.next() ;
%>
			<tr>
				<td><%=user.getId()%></td>
				<td><%=user.getName()%></td>
			</tr>
<%
			}
%>
			</table>
		</center>
	</body>
</html>
```

# 展示效果
    Please input keywords: <input> <submit>
    ID	NAME
    1	test01
    2	test03
    3	test03
 
 # 注意
        要将.java文件编译后的package放到TOMCAT安装目录的lib目录下才能正常运行。
        要将数据库连接的jar包放入classpath之中，或放在jdk/jre的lib目录下。 

# 总结
       A、创建好数据库，存在必要的数据。 
       B、创建java Bean,其中每一个属性对应一个数据库的字段，设置好geter和seter方法。
       C、创建数据库连接类，在构造方法中得到连接，并对外提供获取方法，写好关闭数据库的方法。
       D、设计DAO接口，每个功能对应一个方法。
       E、实现DAO接口，完成业务逻辑的处理，构造时传入数据库连接，具体业务处理时操作数据。
       F、完成代理类实现DAO接口，构造方法是实例数据库连接，实例实现DAO接口的类，并调用该类方法完成功能，对数据进行关闭。代理类负责数据库连接的实例化以及DAO实现的实例化，调用相应逻辑进行相应的数据返回或者是判断操作。
       G、工厂方法负责代理类的实例化，提供一个静态方法返回DAO接口类型。 
       H、前台负责java Bean的实例化，并调用起相应方法获取数据。

