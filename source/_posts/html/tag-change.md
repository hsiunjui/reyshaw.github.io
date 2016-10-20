layout: w
title: HTML5标签的变化
date: 2016-10-20 22:40:35
tags: 标签
categories: HTML
---
>HTML5相对于HTML4及以前标签的变化

<!--more-->
# 关于文档类型的说明（DTD）
- 声明方式
```html
	<!doctype html>
	<!DOCTYPE HTML> 	
	 注:编写HTML5文档时，要求指定文档类型，以确保浏览器在HTML5标准模式下进行渲染。
```

# 新增加的标签
- 结构化类(布局标签)
```html
	<header> <nav> <section> <article> <aside> <footer>                 
	<hgroup> <figure> <figcaption> <dialog>
```
- 多媒体标签
```html
	<video> <audio> <source> <embed> <canvas>
```
- web应用标签
```html
	<menu> <menuitem> <command> <meter> <progress> <datalist> <details>  
```
- 其他标签
```html
	<ruby>   <rp>   <rt> 
	<keygen>   <mark>   <output> 
	<time>
```

# 删除的标签
- 纯表现类
```html
    <basefont>   <big>   <center>   <font>   <s>   <strike>   <tt>   <u>
```
- 可用性类
```html
    <frame>   <frameset>   <noframes>
```
- 易混淆类
```html
    <acronym>   <applet>   <isindex>   <dir>	
```

# 改良的HTML标签
	<b>      ---> 内联文本，粗体，表示没有传递重要性
    <i>      ---> 内联文本，是斜体，没有传递表示重要的意思
    <small>  ---> 表示小字体，如打印注释或者法律条款
    <strong> ---> 表示重要性而不是强调符号    
    <hr>     ---> 表示主题结束，而不是水平线，但显示的还是一条水平线   
    <dd>     ---> 可以同details与figure一同使用，定义包含文本，dialog也可用
    <dt>     ---> 可以同details与figure一同使用，汇总细节，dialog也可用
    <menu>   ---> 重新定义用户界面的菜单，配合commond或者menuitem使用