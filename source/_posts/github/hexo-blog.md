---
title: 用hexo搭建github博客
date: 2016-10-16 14:39:30
categories: github
tags: hexo
---

>hexo是基于Node.js的静态博客程序，可以生成静态网页托管在github和Heroku上。

<!--more-->
# 软件环境
    windows10  git  node  hexo

# 步骤
1.在github上新建一个空仓库[feiker-xr.github.io]
2.在windows桌面上新建文件夹[feiker-xr.github.io]并cd切入
3.生成hexo本地预览并配置部署项
```bash
 npm install hexo -g
 hexo init
 npm install
 npm install hexo-deployer-git --save

 hexo generate                              #生成
 hexo server -p 4000                        #预览,如果端口被占用,可以修改端口
 http://localhost:4000/

 deploy:                        #路径:/_config.yml
    type: git                   #注意冒号后的空格
    repo: ssh://git@github.com/feiker-xr/feiker-xr.github.io  #一个ssh时可用原repo地址
    branch: master
```
4.github仓库配置
```bash
 git init
 .gitignore    #将.npmignore修改为.gitignore
 git add -A
 git commit -m"master"
 git remote add origin https://github.com/feiker-xr/feiker-xr.github.io.git
 git push -u origin master    #初始化仓库完毕

 git branch hexo              #建立分支
 git checkout hexo
 git push origin hexo
```
5.在github上设置hexo为默认分支settings->Branches->Default Branch->update
6.生成master分支项目并发布到服务器
```bash
	hexo deploy -g
```

# 更换主题[nexT](http://theme-next.iissnan.com/)
1.在github上fork [https://github.com/iissnan/hexo-theme-next](https://github.com/iissnan/hexo-theme-next)这个项目
2.用__fork + subtree__同步主题

```bash
 cd gnoixs.github.io
 git clone https://github.com/iissnan/hexo-theme-next themes/next  #克隆主题

 rm -rf themes/next                 #删除主题
 git add --all
 git commit -m"delete next"
 git push origin hexo               #push到远程

 git remote add -f next git@github.com:gnoixs/hexo-theme-next.git
 git add commit push    #需要提交一次
 git subtree add --prefix=themes/next next master --squash   #绑定子项目
 git add commit push    #需要提交一次

 git fetch next master
 git add commit push    #需要提交一次
 git subtree pull --prefix=themes/next next master --squash  #更新子项目

 git subtree push --prefix=themes/next next master #从子目录push到远程

 theme: next       #路径：/_config.yml
 scheme: Pisces	   #路径：/theme/next/_config.yml
```
3.本地预览
4.使用git提交到hexo
5.线上发布**hexo deploy -g**

# 项目结构
	 [feiker-xr.github.com]
             ├── .deploy_git[生成]
             ├── node_modules
             ├── public[生成]
             ├── scaffold
             │   ├── draft.md
             │   ├── page.md
             │   └── post.md
             ├── source
             │   ├── _posts
             │   └── uploads[手动创建]
             ├── themes
             │   ├── landscape
             │   └── next
             ├── .gitignore
             ├── .swp[生成]
             ├── _config.yml
             ├── .db.json[生成]
             └── package.json

# 写博客
1.克隆项目
```bash
 git clone https://github.com/feiker-xr/feiker-xr.github.io.git
 or git clone git@github.com:feiker-xr/feiker-xr.github.io.git
 npm install
```
2.添加**hexo new "title"**或修改文章并本地预览
3.提交代码到hexo
4.发布到线上master
```bash
 hexo clean
 hexo deploy -g
```
5.记得push代码到feiker-xr.github.io项目上去

--------------------------------------------------------------------------------------------------------

# 参考文档
[官方说明](http://theme-next.iissnan.com/getting-started.html)
[使用Hexo & Github,搭建属于自己的博客](https://segmentfault.com/a/1190000006749038)
[hexo博客更换主题](http://www.tuicool.com/articles/zeIZJzv)
[Hexo主题同步](http://w4lle.github.io/2016/06/06/Hexo-themes/)





