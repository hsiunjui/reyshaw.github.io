---
title: 两列等高布局
date: 2016-11-01 15:51:44
tags: CSS应用
categories: CSS
---
>css两列等高布局的方案

<!--more-->
# 布局要求
	1.两列布局
	2.左边固定宽度
	3.右边自适应
	4.两列等高
# 方案一
####利用容器左边框和右边元素的左外边距，容器左边框是一色可看成左边一列，右边一列自成一色。实际是容器和右边元素的等高布局。
 ```html
<style type="text/css">
* { margin: 0;padding: 0;}
html {height: auto;}
body {margin: 0;padding: 0;}
#container {background: #ffe3a6;}
#wrapper {display: inline-block;position: relative;vertical-align: bottom;border-left: 200px solid #d4c376;/*此值等于左边栏的宽度值*/}
#sidebar {float: left;width: 200px;position: relative;margin-left: -200px;/*==此值等于左边栏的宽度值==*/}
#main {float: left;}    
#main,#sidebar{min-height: 200px;height: auto !important height: 200px;}
</style>
<div id="container">
    <div id="wrapper">
        <div id="sidebar">Left Sidebar</div>
        <div id="main">Main Content</div>
    </div>
</div>
```
 
# 方案二
####利用margin和padding对冲，左右元素各自成色，相同模式。
 ```html
<style type="text/css">
*{margin: 0;padding: 0;}
#container {overflow: hidden;}
#left {background: #ccc;float: left;width: 200px;margin-bottom: -99999px;padding-bottom: 99999px;}
#content {background: #eee;margin-bottom: -99999px;padding-bottom: 99999px;margin-left: 200px;/*此值等于左边栏的宽度值*/}
#left,#content {min-height: 200px;height: auto !important;height: 200px;}
</style>
<div id="container">
    <div id="left" class="aside">Left Sidebar</div>
    <div id="content" class="section">Main Content</div>
</div>
 ```

# 方案三
###利用浮动和左外边距，左右元素均右浮动自成一色，通过边距来调整位置。
```html 
<style type="text/css">
*{margin: 0;padding: 0;}
#container{background-color:#0ff;overflow:hidden;padding-left:220px; /* 宽度大小等与边栏宽度大小*/}
* html #container{height:1%; /* So IE plays nice */}
#content{background-color:#0ff;width:100%;border-left:220px solid #f00;/* 宽度大小等与边栏宽度大小*/
               float:right;margin-left:-220px;/* 宽度大小等与边栏宽度大小*/}
#sidebar{background-color:#f00;width:220px;float:right;margin-left:-220px;/* 宽度大小等与边栏宽度大小*/}
#content,#sidebar {min-height: 200px;height: auto !important;height: 200px;}
</style>
<div id="container">
        <div id="content">Main Content</div>
        <div id="sidebar">Left Sidebar</div>
</div>
 ```

# 方案四：
####容器错开和相对定位，两容器自成一色，通过定位和边距确定内容的位置。
 ```html
<style type="text/css">
*{padding: 0;margin:0;}
#container2 {float: left;width: 100%;background: orange;position: relative;overflow: hidden;}
#container1 {float: left;width: 100%;background: green;position: relative;left: 220px;/* 宽度大小等与边栏宽度大小*/}
#col2 {position: relative;margin-right: 220px;/* 宽度大小等与边栏宽度大小*/}
#col1 {width: 220px;float: left;position: relative;margin-left: -220px;/* 宽度大小等与边栏宽度大小*/}
#col1,#col2 {min-height: 200px;height: auto !important;height: 200px;}
</style>
<div id="container2">
    <div id="container1">
        <div id="col1">Left Sidebar</div>
        <div id="col2">Main Content</div>
    </div>
</div>
 ```

# 方案五：
####利用盒子嵌套综合浮动定位边距的运用，稍微有点多此一举。
```html 
<style type="text/css">
*{padding: 0;margin: 0;}
#container1 {float: left;width: 100%;overflow: hidden;position: relative;background-color: #dbddbb;}
#container {background-color: orange;width: 100%;float: left;position: relative;left: 220px;/* 宽度大小等与边栏宽度大小*/}
#left {float: left;width: 220px;margin-right: -100%;margin-left: -220px;/* 宽度大小等与边栏宽度大小*/}
#content {float: left;width: 100%;margin-left: -220px;/* 宽度大小等与边栏宽度大小*/}
#contentInner {overflow: hidden;margin-left: 220px;/* 宽度大小等与边栏宽度大小*/}
#left,#content {min-height: 200px;height: auto !important;height: 200px;}
</style>
<div id="container1">
    <div id="container">
        <div id="left">Left Sidebar</div>
        <div id="content">
            <div id="contentInner">Main Content</div>
        </div>
    </div>
</div>
```
 
 # 参考资料
 [八种创建等高列布局](http://www.w3cplus.com/css/creaet-equal-height-columns)