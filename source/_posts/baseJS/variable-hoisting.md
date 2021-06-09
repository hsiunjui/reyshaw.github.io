---
title: 变量提升
date: 2021-06-09 17:33:22
tags: js基础
categories: 客户端JS
---
>什么是Javascript的变量提升

<!--more-->
# 变量提升
- JavaScript引擎的工作方式是，先解析代码，获取所有被声明的变量，然后再一行一行地运行。这造成的结果，就是所有的变量的声明语句，都会被提升到代码的头部，这就叫做变量提升（hoisting）
```javascript 
	console.log(a) // undefined
	var a = 1
	function b() {
		console.log(window.a)
	}
	b() // 1
```
- 上面的代码实际执行顺序是这样的: 第一步： 引擎将var a = 1拆解为var a = undefined和 a = 1，并将var a = undefined放到最顶端，a = 1还在原来的位置
```javascript
	var a = undefined
	console.log(a) // undefined
	a = 1
	function b() {
		console.log(window.a)
	}
	b() // 1
```
- 直接打印a而不声明汇报错的
```javascript
	console.log(a) // Uncaught ReferenceError: a is not defined
```
# 参考
- [什么是变量提升](https://segmentfault.com/a/1190000023301058)

# 创建及修改时间
> 创建时间: 2021-06-09 17:52:13  