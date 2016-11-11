---
title: 服务器的搭建
date: 2016-11-11 18:10:39
tags: 服务器
categories: 运维
---
>阿里云ECS服务器的使用。

<!--more-->
# 服务器购买
- 平台:阿里云
	+ 华东1 
	+ 按量付费
	+ 设置密码

# 登录服务器
	1.下载Xshell
	2.新建会话[IP][username:password]

# 文档上传
	1.下载FlashFXP
	2.站点管理->新建站点
	3.填写基本信息[SFTP over SSH][120.26.165.239：22][username:password]
	
# 安装node服务
```bash
	//获取包
	wget https://nodejs.org/dist/v6.9.1/node-v6.9.1.tar.gz
	//解压包
	tar xvf node-v6.9.1.tar.gz
	cd node-v*
	//安装软件包
	sudo yum install gcc gcc-c++
	//配置和编译
	sudo ./configure
	sudo make
	//执行安装
	sudo make install
	//测试结果
	node -v && npm -v
```

# 安装mongodb数据库
```bash
	//获取软件包
	wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.0.2.tgz
	//解压到指定目录
	cd /usr/local
	cp -r ~/mongodb-linux-x86_64-3.0.2.tgz .
	tar -zxvf mongodb-linux-x86_64-3.0.2.tgz
	mv mongodb-linux-x86_64-3.0.2 mongodb
	//新建需要的文件(夹)
	cd mongodb
	mkdir data
	touch logs
	//添加配置
	touch mongodb.conf
	vi mongodb.conf
		port=27017
		logappend=true
		fork=true
		dbpath=/usr/local/mongodb/data
		logpath=/usr/local/mongodb/logs
	//启动服务
	 ./bin/mongod -auth -f mongodb.conf
	//启动客户端
	 ./bin/mongo [IP：port]
```
