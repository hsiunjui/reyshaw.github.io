---
title: 用hexo搭建github博客
date: 2016-10-16 14:39:30
tags: github
---

# 介绍
hexo是一个基于Node.js的静态博客程序，可以方便的生成静态网页托管在github和Heroku上。

# 环境
操作系统 ： windows 10
git版本  : git version 2.8.0.windows.1
node版本 : v4.3.1
npm版本  : 2.14.12
hexo版本 : 3.2.2

# 步骤
1.在github上新建一个空仓库[gnoixs.github.io]
```bash
	https://github.com/new
```
2.在windows桌面上新建文件夹[gnoixs.github.io]
```bash
	mkdir gnoixs.github.io
	cd gnoixs.github.io
```
3.生成hexo本地预览 
```bash
	npm install hexo
	hexo init
	npm install
	npm install hexo-deployer-git --save
	hexo generate
	hexo server
	http://localhost:4000/
```
4.更改部署配置项/_config.yml
```bash
deploy:
	type: git   #注意冒号后面有空格
	repo: https://github.com/gnoixs/gnoixs.github.io.git
	branch: master
```
5.初始化github仓库
```bash
	git init
	.gitignore  #将.npmignore修改为.gitignore
	git add -A
	git commit -m"master"
	git remote add origin https://github.com/gnoixs/gnoixs.github.io.git
	git push -u origin master
```
6.建立分支管理源代码
```bash
	git branch hexo
	git checkout hexo
	git push origin hexo
```
7.在github上设置hexo为默认分支
```bash
	https://github.com/gnoixs/gnoixs.github.io/settings
	Branches
	Default Branch
	update
```
8.生成master分支项目并发布到服务器
```bash	
	hexo deploy
```
	
# 更换主题[nexT](http://theme-next.iissnan.com/)
1.更改
```bash
	 cd gnoixs.github.io
	 git clone https://github.com/iissnan/hexo-theme-next themes/next
	 theme: next #/confit.yml
	 scheme: Pisces	#/theme/next/_config.yml
```
2.预览
```bash
	 hexo generate
	 hexo server
	 http://localhost:4000/
```
3.提交到hexo
```bash
	git add -A
	git commit -m"theme"
	git push orgin hexo
```
4.发布
```bash
	hexo deploy
```

# 项目结构
	 [gnoixs.github.com]
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
    	 ├── .gitignore
		 ├── .swp[生成]  		
    	 ├── _config.yml
		 ├── .db.json[生成] 
    	 └── package.json
				
# 写博客
1.克隆项目
 ```bash
     git clone https://github.com/gnoixs/gnoixs.github.io.git
     npm install hexo -g
     npm install
     npm install hexo-deployer-git --save
 ```
2.添加或修改文章
```bash
	hexo new "title"
	hexo generate
	hexo server
```
3.提交代码到hexo
```bash
	 git add --all
	 git commit -m"update"
	 git push origin hexo
```
4.发布到master
```bash
	hexo deploy -g
```

--------------------------------------------------------------------------------------------------------

# 参考文档
[使用Hexo & Github,搭建属于自己的博客](https://segmentfault.com/a/1190000006749038)
[hexo博客更换主题](http://www.tuicool.com/articles/zeIZJzv)
[官方说明](http://theme-next.iissnan.com/getting-started.html)			


		
		
		

