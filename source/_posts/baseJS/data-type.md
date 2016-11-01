---
title: JavaScript的数据类型
date: 2016-11-01 16:13:32
tags: js基础
categories: 客户端JS
---
>Javascript的数据类型

<!--more-->
# 三类七种
	 A.基本数据类型：
	     1.字符串类型 (String)
	     2.数字类型 (Number)
	     3. 布尔类型 (Boolean)
	B.复合数据类型：
	     1.函数 (Function),严格来讲它也是Object.
	     2.对象 (Object)
	C.特殊数据类型：
	     1.空对象 (Null)
	     2.未定义 (Undefined)

# 数据类型检测
- 基本类型使用 typeof 操作符
- 引用类型使用 instanceof 操作符 

# 数据类型检测示例
```javascript
        typeof "1"                --  string
        typeof 1                  --  number
        typeof true              -- boolean
        typeof null               -- object
        typeof undefined     -- undefined
        typeof Array            -- function
        typeof Array()          --object
        typeof array            -- undefined
        typeof Function      -- function
        typeof Function()    --function
        typeof function        --报错
        typeof function()      --报错
        typeof Object          -- function
        typeof object           -- undefined  
        typeof Object()        --object
```
# 数据转换(运用包装类型)
	其他类型转换成数值型：
	    undefined --》NaN
	    null --》0
	    boolean  --》true转换为1，false转换为0 
	    string  --》内容为数字转成相应数字，否则转换成NaN
	    其他对象 --》NaN
	其他类型转换成逻辑型：
	    undefined  --》false
	    null  --》false
	    number  --》若0或NaN转换为false,否则为true
	    string  --》长度为0转换为false,否则转换为true
	    其他对象  --》true
	其他类型转换为字符串：
	    undefined  --》"undefined"
	    null  --》 "null"
	    number --》转成相应数字字符串
	    boolean  --》若为true转换为"true",若为false则转换为"false"
	    其他对象 --》若存在未toString()的值，不存在为"undefined"