---
title: 块级元素与行内元素
date: 2016-10-20 23:34:58
tags: 标签
categories: HTML
---
>块级元素和行内元素的异同

<!--more-->
# block(块级)元素
- 描述
		每个块级元素默认占一行高度。
		一行内添加一个块级元素一般无法添加其他元素（除开浮动和定位）。
		两个块级元素连续编辑时，会在页面自动换行显示。块级元素一般可嵌套块级元素或行内元素。
- 特点
		A、总是在新行上开始
		B、宽高，行高以及内外边距均可控制
		C、宽度缺省是它容器的100%
		D、它可以容纳内联元素和其他块元素
- 常见块级元素
```html
<div> <h1~h6> <p> <form> <ul> <ol> <li> <dl> <dt> <dd>
<table> <thead> <th> <tbody> <tr> <td> <tfoot>    
<address> <caption> <fieldset> <legend> <pre> <hr> <noframes> <noscript>
以下h5标签本质上也是块级元素
<header> <section> <article> <footer> <figure> <dialog> <video> <canvas>等
```
- 注意
		a.块级元素可以包含内联元素或某些块级元素
		b.块级元素不能放在<p>标签里面
		c.有几个特殊的块级元素只能包含内联元素，不能包含块级元素：<h1~h6>  <p>  <dt>等
		d.块级元素与块级元素并列，内联元素与内联元素并列
			（错误：<div><h2></h2><span></span></div>）

# inline(行内)元素
- 描述
		行内元素一般都是基于语义级的基本元素，只能容纳文本或其他内联元素。
- 特点
		A、和其他元素都在一行上
		B、高，行高及内外边距不可完全控制
		C、宽度就是自身文字/图片的宽度，不可改变
		D、内联元素只能容纳文本或其他内联元素 	
- 常见行内元素
```html
<a> <img> <input> <textarea> <select> <label> <span> <br> 
<strong> <small> <b> <em> <i> <big> 
<code> <abbr> <acronym> <q> <samp> <sub> <sup>
<kbd> <tt> <cite> <dfn> <bdo> <var> 
```
- 注意
	a.设置width/hieght无效，但是可以设置行高line-height
	b.margin/padding只有左右有效，上下无效，若设置padding是元素范围增大了，对周边内容没影响

# 可变元素：
- 描述：
		根据上下语境决定该元素是块级元素还是内联元素。
- 常见可变元素：
```html
	<button>   <del>   <ins>   <map>  <object>  <iframe>  <script>
```
- 注意：
```html
	<input>  <img>  <textarea> <select>等有时也可看做可变元素。  
```