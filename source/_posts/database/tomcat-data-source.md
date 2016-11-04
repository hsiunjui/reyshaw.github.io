---
title: TOMCAT数据源的配置
date: 2016-11-04 15:52:28
tags: 数据库安装与配置
categories: 数据库
---
>配置TOMCAT的数据源。

<!--more-->
# 环境
    操作系统：windows 10;
    服务器版本：apache-tomcat-7.0.65;
    数据库版本：mysql-5.6.27-winx64;
    开发语言版本：java1.8.0 (jdk1.8.0_65);
    集成开发工具：intellij IDEA 14.1.5;
    未引入开发框架

# 步骤

    1.下载mysql的数据库驱动包，并放在tomcat安装目录的lib目录下。
     
    2.找到tomcat安装目录的下的conf目录下的context.xml文件，在<Context>节点中添加如下代码：
            <Resource  
            name="jdbc/name"                                                 //数据库连接池名称，可以自定义，一般以jdbc/开头 
            type="javax.sql.DataSource"                                  //对象类型
            driverClassName="com.mysql.jdbc.Driver"           //数据库驱动名称
            password=""                                                           //数据库密码
            maxIdle="2"                                                           //最大空闲数，0表示无限制
            maxWait="5000"                                                   //最大等待毫秒数，超时会出错误信息
            username="root"                                                  //数据库用户名
            url="jdbc:mysql://localhost:3306/dbname"           //数据库地址
            maxActive="4"/>                                                  //最大连接数，0表示没有限制
    保存，退出。
    
    3. 找到javaweb项目的WEB-INF下的web.xml文件，做如下配置：
        <resource-ref>
            <description>DB Connection</description>                             //连接描述
            <res-ref-name>jdbc/supermarket</res-ref-name>                   //连接池名称，与上一步的name相对应
            <res-type>javax.sql.DataSource</res-type>                            //对象类型
            <res-auth>Container</res-auth>                                              //连接池管理权限，Container表示容器管理
      </resource-ref> 
    保存，退出。
    
    4.在jsp或servlet或javaBean中获取连接：
          public static final String JNDINAME = "java:comp/env/jdbc/name";                //后面的name与第二部配置的name一致
          Context initial = new InitialContext();                                                               //初始化上下文
      DataSource ds = (DataSource)initial.lookup(JNDINAME);                            
       Connection conn = ds.getConnection();                                                 //获得连接，后面执行正常jdbc操作
