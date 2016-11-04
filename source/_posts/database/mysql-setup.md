---
title: mysql的安装
date: 2016-11-04 15:42:50
tags: 数据库安装与配置
categories: 数据库
---
>windows下安装mysql数据库。

<!--more-->
# 环境
- 操作系统：window10
- mysql版本： mysql-5.6.27-winx64 (zip版)

# 步骤
     1. 下载mysql的压缩包：http://dev.mysql.com/downloads/mysql/
     2. 解压到硬盘，如C:\Program Files\
     3. 重命名mysql-5.6.27-winx64文件夹下my-default.ini为my.ini，
        配置内部参数(必配，其他可选配)
            #myslq安装路径
               basedir=C:\Program Files\mysql-5.6.27-winx64  
            #mysql数据存储路径 
               datadir=C:\Program Files\mysql-5.6.27-winx64\data
     4. 添加mysql的bin路径到path环境变量
     5. 以管理员身份开启一个命令行窗口
     6. 进入mysql的bin目录，键入mysqld - install回车后会提示服务安装成功或失败
        (若成功后才能在计算机管理->服务中找到mysql)
     7. net start mysql开启mysql服务
     8. mysql -u root -p后键入密码登入 
