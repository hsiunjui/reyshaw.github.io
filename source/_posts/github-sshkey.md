---
title: github ssh key的配置
date: 2016-10-17 13:31:43
tags: github
---
# SSH的作用
    用于验证github的需要
    
# 检查ssh key
```bash
    cd ~   #切换到根
    cd .ssh
    ls -al
    rm *       #删除所有文件
```

# 生成新的ssh key
```bash
    cd ~
    ssh-keygen -t rsa -C "pheaco@gmail.com"
    enter   #可选填
```

# 登录到github主页
    1.右侧图标点击settings
    2.选择SSH and GPG keys
    3.New SSH key
    4.自定义title
    5.打开~/.ssh/id_rsa.pub,复制公钥并粘贴到key栏
    6.点击add key,输入github密码，结束流程
    
# 配置账户
```bash
    git config --global user.name "gnoixs"  #设置用户名
    git config --global user.email "pheaco@gmail.com"  #设置邮箱地址
```
    
# 测试
    如果git push时不再用输入密码，证明配置成功
    
# 参考
[window下配置SSH连接GitHub、GitHub配置ssh key](http://jingyan.baidu.com/article/a65957f4e91ccf24e77f9b11.html)