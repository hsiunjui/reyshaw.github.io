---
title: github ssh key的配置
date: 2016-10-17 13:31:43
categories: github
tags: sshkey
---
>用于无密码输入的git提交,验证github的需要

<!--more-->
# 设置步骤
```bash
 cd ~/.ssh  #检查是否配置ssh
 rm *       #删除.ssh下所有文件

 cd ~       #切换到用户目录下    
 ssh-keygen -t rsa -C "pheaco@gmail.com"  #生成新的ssh key
 enter   #可选填
```

# 登录到github主页
     1.右侧图标点击settings
     2.选择SSH and GPG keys
     3.New SSH key
     4.自定义title
     5.打开~/.ssh/id_rsa.pub,复制公钥并粘贴到key栏
     6.点击add key,输入github密码确认
    
# 配置账户
```bash
 git config --global user.name "gnoixs"      #设置用户名
 git config --global user.email "pheaco@gmail.com"  #设置邮箱地址
```
    
# 测试
```bash
 ssh -T git@github.com    #无密码push需要重启电脑
```
        
# 参考
[window下配置SSH连接GitHub、GitHub配置ssh key](http://jingyan.baidu.com/article/a65957f4e91ccf24e77f9b11.html)