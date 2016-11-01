---
title: JavaScript函数
date: 2016-11-01 16:19:17
tags: js高级
categories: 客户端JS
---
>在JavaScript中，函数就是对象,具体来说它是Function'类'的实例,但它又有一些不同于普通对象的特殊性。

<!--more-->
# 函数的定义方式
 ```javascript
// 方式一
 function func1(){
    console.log('func1 is defined...');     
}
// 方式二
 var func2 = function(){
     console.log('func2 is defined...');
 }
 // 方式三
var func3 = new Function("console.log('func3 is defined')");
// 函数定义后会在栈中分配局部变量(func1,func2,func3)，并指向堆中相应的函数
 ```

# 函数对象与普通对象的区别
####对象是通过引用的指向来完成对象的赋值的，而函数确实通过对象的拷贝来完成的。 
 ```javascript
// 函数的拷贝赋值
function func1(){
    console.log("func1");
}
var func2 = func1;                  //  拷贝了func1, 两个变量分别指向两块不同的堆空间
func2();                                  //  输出func1
func1 = function(){
    console.log("func1 changed");
}
func2();                                 // 不受func1变化的影响， 输出func1
func1();                                // 函数改变，输出func1 changed
 
//对象的引用赋值
var o1 = new Object();
var o2 = o1;                         // 两个变量指向同一块内存
o1.name = 'hw';                  // 改变其中的一个值,其他的对象相应的值会相应改变
console.log(o2.name);       // 输出hw
```

# 函数的属性和方法
	length属性   ---  表示函数所期望的参数个数
	pototype属性  --- 该属性指向函数的原型
	call(context,arg1,arg2...)/apply(context,argrments)方法  ----代替另一个对象调用一个方法
	bind(args)方法   ---  ES5新增，该方法会创建一个函数的实例，其this值会被绑定到传给bind函数的值

# 函数的重载
####在JavaScript中，函数不能重载，只能重写。
```javascript 
var sum = function(a,b){
    return a+b;     
}
var sum = function(a){
    a+1;
}
console.log(sum(10))        // 调用第二个函数输入11
console.log(sum(10,10))     // 依然调用第二个函数输入11
```

# 函数的传递及返回
####由于函数是对象，所以可以直接通过参数的形式传递进来，同时，也可以通过函数返回函数。
```javascript 
//函数作为参数传入
function say(str){
    console.log('hello '+ str);
}
function sum(num){
    return num + 10;
}
function callFunc(func,arg){                    // 传入函数
    func(arg);
}
callFunc(say,'world');                           // 会调用say方法输入hello world
callFunc(sum,10);                               // 调用sum方法输出20,还可定义其他函数实现灵活调用
// 函数作为结果返回
function func(arg){
    var result = function(num){             // 内部闭包函数。 
        return arg + num;
    }
    return result;
}
 var fn =  func(10);                            //  此时fn是一个函数
 fn(10);                                             //  函数调用后为20
 fn(20);                                            //  返回30,注意该闭包扩大了func参数的作用范围     
 
 arguments对象的属性：
            length属性   ----   实际传入的参数个数
            callee属性   ----     正在执行函数本身的引用，一般用在递归调用解耦。
```

# 函数中this指针：
- 在用函数创建类的时候，设置类的属性和方法需要用this关键字来引用
- 注意：this关键字在调用时会根据不同的调用对象变得不同。一般谁调便指谁。     
