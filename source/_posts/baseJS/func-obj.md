---
title: Function与Object
date: 2016-11-01 17:41:32
tags: js高级
categories: 客户端JS
---
>Function不全是Object。

<!--more-->
# Function与Object的差异
- 差异 Function类型也可以看作是一种Object，但它与Object是有差异的。这种差异不仅表现在其各自的属性和方法不一样以外（Function和Object拥有不同的原型）,还表现在Function转化为Object是的状态。
- 示例 
```javascript
	Array  ---  它是Function类型的一个对象，它的prototype指向Function.prototype。typeof Array       //function
	new Array()   ---  它是一个Object类型的对象，它的prototype指向了Object.prototype。 typeof new Array()  //object
```
# 函数是基于拷贝赋值
```javascript
var sum1 = function(a,b){                     // 结果是栈中的一块内存指向了堆中的一块内存
    return a+b;
}
//var sum1 = new Function('a','b','return a+b');    该步骤是完成了定义sum1隐式操作
var sum2 = sum1;                               //  栈中两块儿内存指向堆中同一块儿内存
console.log(sum2(2,2));                     //4, 由于是指针指向，因此可以调用
sum2 = function(a){                           // sum2指向了堆中另外一块儿内存
    return a;
}
//sum2 = new Function('a','return a');       //  隐式转换
console.log(sum1(2));                             //NaN, 由于需要两个参数，故NaN
```

# Function向Object的转化
```javascript
// 在任何操作之前已经存在了Function和Object的prototype
var Person = function(name){
    this.name = name;
}
//var Person = new Function('name','this.name=name'); 隐式操作,Person的_proto_属性指向了Function的prototype
//Person.prototype = new Object();              隐式操作,产生了一个Object的对象,Person的prototpe属性指向了Object的实例
var p = new Person(' 小李');                        // p 拥有了_proto_属性指向了Object对象的prototype
```
- 内存图示意
	![function与obj的转化](/images/func-obj.png)
