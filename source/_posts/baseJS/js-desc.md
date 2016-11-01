---
title: JavaScript概述
date: 2016-11-01 16:03:10
tags: js基础
categories: 客户端JS
---
>JavaScript是一种直译式脚本语言，是一种动态类型、基于原型的的语言，内置支持类。

<!--more-->
# JavaScript的分类
- 客户端JavaScript -- 用户dom操作等处理网页内容的脚本(本分类主要描述客户端JavaScript)
- 服务端JavaScript -- 用于处理后台业务逻辑等的脚本，入node.js

# 客户端JavaScript的组成部分
- ECMAScript,描述了该语言的语法和基本对象。
- 文档对象模型(DOM)，描述处理网页内容的方法和接口。
- 浏览器对象模型(BOM)，描述与浏览器进行交互的方法和接口。 

# 客户端JavaScript的基本特点
- 是一种解释性脚本语言(代码不进行预编译)。
- 主要用来向HTML页面添加交互行为。
- 可以直接嵌入HTML页面，但携程单独的js文件有利于结构和行为的分离。

# 客户端JavaScript的主要任务
1. 嵌入动态文本于HTML页面
2. 对浏览器事件作出响应
3. 读写HTML元素
4. 在数据被提交到服务器之前验证数据
5. 监测访客的浏览器信息
6. 控制cookies，包括创建和修改等 

# ECMAScript发展历史(JavaScript遵循该规范，其实现一般晚于ES标准)
	ECMAScript 1  -----  1997年06月首版
	ECMAScript 2  -----  1998年06月格式修正，以使得其形式与ISO/IEC 16262国际标准一致
	ECMAScript 3  -----  1999年12月，添加正则表达式、新的控制指令、异常处理、输出格式化等。
	ECMAScript 4  -----  未完成
	ECMAScript 5  -----  2009年12月发布
	ECMAScript 6  -----  2015年6月17日发布