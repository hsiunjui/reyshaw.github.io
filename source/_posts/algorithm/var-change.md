---
title: 不借助第三者实现两个变量值的互换
date: 2016-11-04 14:09:01
tags: 运算
categories: 算法
---
>利用与运算符实现的变量值互换。

<!--more-->
# 示例代码(javascript)
```html
<script type="text/javascript">
	var a = 11;
	var b = 22;
	console.log("a=" + a +"  ,b=" + b); //转换之前打印
	a = a ^ b;
	b = b ^ a;
	a = a ^ b;
	console.log("a=" + a +"  ,b=" + b);  //转换之后打印
</script>
```
# 后台打印
     a=11  ,b=22
     a=22  ,b=11 