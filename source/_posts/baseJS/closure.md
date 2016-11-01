---
title: JavaScript闭包
date: 2016-11-01 17:41:03
tags: js高级
categories: 客户端JS
---
>闭包是指可以访问函数局部变量的函数，其本质上还是是一个函数。

<!--more-->

# 函数的作用域链：
- 当调用一个函数的时候，会为这个函数创建一个SCOPE属性，这个属性保存了所有的上下文使用的变量。当在某个函数中调用新函数时，这个新函数依然会有一个作用域来保存原有函数的SCOPE和自己新增的SCOPE,这样便形成了一个链式结构。
```javascript
var color = 'red';                      // 全局变量
var showColor = function(){             // 全局函数,打印变量值
    console.log(this.color);    
}
function changeColor(){                 // 全局函数
    var anotherColor = 'blue';          // 局部变量
    function swapColor(){               // 局部函数（闭包,交换变量值）
        var tempColor = anotherColor;
        anotherColor = color;
        color = tempColor;
    }
    swapColor();                        // 局部调用
}
changeColor();                          // 全局调用，内存图从这里开始，保存了全局的变量环境和自身的变量环境
showColor();                            // 全局调用，保存了全局，上级和自身的变量环境
```
- 内存示意
	![闭包内存](/images/closure-1.png)

# 闭包的影响
- 扩大了某些局部变量的作用域范围。
```javascript
function compareObjectFunction(prop){               //  按照属性大小返回不同的值,可结合sort排序
     return function(obj1,obj2){
         if(obj1[prop] > obj2[prop]){
             return 1;
         }else if(obj1[prop] < obj2[prop]){
             return -1;
         }else{
             return 0;
         }
     }
}
var o1 = {name:'Leon',age:23};
var o2 = {name:'Ada',age:28};
/*
    var arr = [o1,o2];
    arr.sort(compareObjectFunction('name'));
*/
var compare = compareObjectFunction('age');  // 在java等语言中，prop已经被释放了不能再访问, 此时的compare是一个函数
var rel = compare(o1,o2);                                   // 在这里prop依然可以范围,可见扩大了prop局部变量的作用范围
console.log(rel);
```
- 内存示意
 ![闭包内存](/images/closure-2.png)

# 创建闭包函数的问题
```javascript
function fn1(){
    var fns = new Array();
    for(var i = 0; i<10; i++){
        fns[i] = function(){                //  定义后fns中每个函数都是return i,而不是具体的值
            return i;
        }
    }
    return fns;
}
var fs = fn1();                             // 这里调用后,i既然存在其值是10
for(var i = 0; i<fs.length;i++){
    console.log(fs[i]());                   // 输出10行10，而不是0-9
}
```
- 原因：这是因为每次 return i 的时候都是取的fun1中for时定义的i,此i的值为10
- 解决方案：是再定义闭包
```javascript
   for(var i = 0; i<10;i++){
       var tf = function(num){
           fns[num] = function(){
               retrun num;
            }
        }
        tf(i);
    }
```
- 缺陷：但这样内存消耗是巨大的，所以闭包要慎用

# 闭包中this的问题
```javascript
var name = 'zhangshan';
var Person = {
    name:'lisi',
    say:function(){
        return function(){
            return this.name;
        }
    }
}
var resultSay = Person.say();           // 此时 resultSay是一个函数
var resultName =  resultSay();          // 相当于 Person.say()();    输出zhangsan
```
- 原因：Person.say() 调用后this 就 指向 了window，而resultSay()相当于是执行了return window.name;
- 解决方案：用临时变量保存上下文信息，在类中的say方法改为
```javascript
   say:function(){
       var that = this;
       return function(){
           return that.name;
        }
    }
 ```

# 块级作用域(匿名函数自执行)
```javascript
(function(){
    for(var i = 0; i<10;i++){
    // 如果不加匿名函数自执行,在外部是可以访问到i的值的,因为JavaScript中只有函数作用域,没有块级作用域(ES6之前)
    }    
})();
console.log(i);     // 此时已经无法访问i的值了
``` 

# 私有成员变量
```javascript
var Person;
(function(){
    var name = '';                      // 定义局部变量，封装到内部而不用this索引
    Person = function(value){
        name = value;
    }
    Person.prototype.setName = function(value){     // 通过set,get方法提供对外访问接口
        name = value;
    }
    Person.prototype.getName = function(){
        return name;
    }
})();
var person = new Person('Leon');        // 构造
person.setName('Lucy');                 // 设值
console.log(person. getName());         // 取值
``` 

