---
title: html5布局变化
date: 2016-10-20 22:55:55
tags: 标签
categories: HTML
---
>HTML5的布局风格

<!--more-->
# 传统布局
```html
<body>
    <div id="header">
    	<div id="nav"></div>
    </div> 
    <div id="content">
         <div id="section">
                <div id="article">
                    <div id="_header"></div>
                    <p></p>
                    <div id="_footer"></div>
                </div>
         </div>
        <div id="aside"></div>
    </div>
   <div id="footer"></div>
</body>
```
- 传统布局图
![传统布局](/images/layout_before_html5.jpg)
# HTML5布局
```html
<body>
    <header>
        <nav></nav>
    </header> 
    <div id="content">
        <section>
            <article>
                <header></header>
                <p></p>
                <footer></footer>
            </article>
        </section>
        <aside></aside>
    </div>
   <footer></footer>
</body>
```
- HTML5布局图
![HTML5布局](/images/layout_html5.jpg)