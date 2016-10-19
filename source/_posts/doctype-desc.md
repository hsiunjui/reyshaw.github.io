---
title: 文档声明DOCTYPE
date: 2016-10-19 22:28:30
tags: HTML概述
categories: HTML
---
>DOCTYPE告知浏览器在向用户显示文档时应使用何种规则.这些规则被称为"模式".

<!--more-->
# DOCTYPE的模式
- 标准模式: 浏览器按照w3c标准解析执行代码，DTD声明定义了标准文档类型(标准模式解析)文档类型，会使浏览器使用相应的方式加载网页并显示。
- 怪异模式: 浏览器按照自己的方式解析执行代码.
 + 触发怪异模式的主要原因
		1. 没写DOCTYPE
		2. 在DOCTYPE之前加XML声明，会在IE7下触发
		3. 在XML声明和XHMTL的DOCTYPE之间加入HTML注释，可在IE7下触发
		4. <!---->放在<!DOCTYPE>前面
		5. 写了DOCTYPE,但不在文档的第一行。

# DOCTYPE声明类型
- HTML5
```html
	<!DOCTYPE html>
```
- HTML4
 + HTML 4.01 Strict(严格型)-该DTD包含所有HTML元素和属性,但不包含展示性和弃用的元素(如font).不允许使用框架集(framesets)
```html
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd"> 
```
 + HMTL 4.01 Transitional(过渡型)-该DTD包含所有HTML元素和熟悉,包括展示性和弃用元素。不允许使用框架集. 
```html 
	<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
```
 + HTML 4.01 Frameset(框架型)-该DTD等同于HTML 4.01 Transitional,但允许框架集内容.
```html
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">  
```
- XHTML4
 + XHTML 1.0 Strict-该DTD包含HTML元素和属性,但不包含展示性和弃用的元素,不允许使用框架集.必须以格式正确的XML来编写标记.
```html
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  
```
```html
 + XHTML 1.0 Transitional-该 DTD 包含所有 HTML 元素和属性,包括展示性的和弃用的元素,不允许框架集.必须以格式正确的 XML 来编写标记.
```html
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-      ;            transitional.dtd">
```
```html
 + XHTML 1.0 Frameset-该 DTD 等同于 XHTML 1.0 Transitional，但允许框架集内容。
```html
	<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
```
- XHTML 1.1 - 该 DTD 等同于 XHTML 1.0 Strict，但允许添加模型（例如提供对东亚语系的 ruby 支持）    
```html
	<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd"> 
```
