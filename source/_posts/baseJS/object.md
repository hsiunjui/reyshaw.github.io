---
title: JavaScript对象
date: 2016-11-01 17:18:37
tags: js高级
categories: 客户端JS
---
>在JavaScript中，一切皆对象。对象有以下创建方式。

<!--more-->

# 基于Object来创建
```javascript     
var obj = new Object();         // 创建一个对象
obj.name = 'Ada';               // 创建对象属性
obj.print = function(){           // 创建对象方法
    console.log(this.name);
}
```
 - 缺陷
  + 没有类的约束
  + 不能实现对象的重复利用
  + 也没有完成很好的封装
 	
# 通过json形式来创建
 ```javascript
var obj = {                             // 以属性值的形式保存，并以逗号分隔
    name:'Ada',
    print:function(){
        console.log(this.name);
    }
}
```
 *  缺陷：该形式可以看作是一个静态类, 同样不利于重用
 
# 通过工厂方式创建
```javascript
function createObj(name){                    //在内部创建对象并返回
    var obj =  new  Object();
    obj.name = name;
    obj.print = function(){
        console.log(this.name);
    }
    return obj;
}
```
* 缺陷：该方法解决了类的封装性等问题，但无法检测对象的类型（因为全都是Object类型）
 
# 基于构造函数创建
```javascript
function Person(name){
    this.name = name;
    /*this.say = function(){        // 该种定义方法的缺陷在于每一个对象 都有一个方法的拷贝，浪费存储空间
        console.log(this.name);
    }*/
    this.say = say;
}
function say(){
    console.log(this.name);
}
var person = new Person('Ada');     // 通过new关键字来创建
```
* 缺陷：将所有方法设置为全局函数，如果用window来调用则会破坏对象的封装性

# 基于原型来创建
```javascript
function Person(){
    // 通过在原型上挂载属性和方法
}
/*
    Person.prototype.name = 'Ada';                // 单个赋值方法
    Person.prototype.say = function(){
        console.log(this.name);
    }
*/
Person.prototype = {                            // json方式赋值
    constructor:Person,
    name:'Ada',
    say:function(){
        console.log(this.name);
    }
}
var person = new Person();                      // 通过new关键字创建
```
- 缺陷：
 + 该方法无法通过构造函数来设置属性值，
 + 如果属性值为引用类型，修改一个对象的引用类型的属性值可能引起其他对象的变化
```javascript
        Person.prototype = {                            
            constructor:Person,
            friends:['Ada','john'],
            say:function(){
                console.log(this.friends.length);
            }
        }
        var person1 = new Person();
        person1.friends.push('susan');      // person1.friends会指向原型中的friends, 因此会修改原型的值
        var person2 = new Person();
        person2.say();                      // 会输出3
        
```
 
# 组合构造函数和原型来实现
```javascript
function Person(name,friends){                              // 属性在构造函数中定义
    this.name = name;
    this.friends = friends;                                 // 引用类型的属性会存储在自己空间而不是原型中，修改不影响原型
}
Person.prototype = {                                        // 方法在原型中定义
    constructor:Person,
    say:function(){
        console.log(this.name+' '+ this.friends.length);
    }
}
var person = new Person('Ada',['john','lucy']);             // 通过new方式创建
```
* 这是创建对象最常见的一种方式
 
# 动态原型
```javascript 
function Person(name,friends){                          
    this.name = name;
    this.friends = friends;                                
    if(!Person.prototype.say){              // 如果原型不存在则创建，存在则不再重复创建
        Person.prototype.say:function(){
            console.log(this.name+' '+ this.friends.length);
        }     
    }
}
var person = new Person('Ada',['john','lucy']);             // 通过new方式创建
```
 * 该方法只是把原型代码放置在类里面，并判断创建条件，更符合Java等面向对象封装的特性
 * 但若有大量方法,则需要大量条件判断