---
title: JavaScript原型
date: 2016-11-01 16:27:21
tags: js基础
categories: 客户端JS
---
>原型是一个特殊的对象，当一个函数定义后，便会随之产生一个原型对象。函数定义后会有一个prototype属性指向产生的原型对象。而原型对象中会有一个constructor属性，该属性指向函数。

<!--more-->
# 原型的内存图
- 定义函数时
```javascript
	function Person(){
    	//内存模型图如下    
	}  
```
![图片](layout_html5.jpg)