---
title: JavaScript原型
date: 2016-11-01 16:27:21
tags: js高级
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
![定义函数](/images/prototype-1.png)

- 对原型赋值时
```javascript
	Person.prototype.name = "Leon";
	Person.prototype.age = 23;
	Person.prototype.say = function() {
	    alert(this.name+","+this.age);
	}
```
![原型赋值](/images/prototype-2.png)

- 创建对象时(新创建的对象会有一个隐藏的_prop_属性指向‘类’的原型)
```javascript
	var p1 = new Peron();
```
![创建对象](/images/prototype-3.png)

- 覆盖对象属性时(会在对象的空间中保存新值，不会修改原型的值)：
```javascript
	var p2 = new Person();
	p2.name = "Ada";
```
![覆盖对象](/images/prototype-4.png)

# 原型检查方法
	hasOwnProtopype(arg)  ---  如果实例本身存在该属性，返回true。
	in    ---  无论属性是存在是实例中还是原型中返回true,不存在返回false。
	isPrototypeOf(arg)   ---  用来判断指定对象是否存在于另外一个对象的原型链中，是返回true，否则返回false。

# 原型的重写(以下方式会生成一个新的prototype对象并指向它)
```javascript
Person.prototype = {              //注意该方式会将constructor属性指向Object，如果构造函数比较重要，需要手动指回
    name:'Leon',                        
    age:23,
    say:function(){
        alert(this.name+","+this.age);    
    }
}                                          //如果在该方式之前指定了prototype的一些属性，将会全部被覆盖                    
 
在new之后重写原型的问题：
function Person(){}
var p1 = new Person();                  // 实例化
Person.prototype.sayHi = function() {
    alert(this.name+":hi");
}
var proto = Person.prototype; 
Person.prototype = {                    //该方式生产了一个新的对象并指向它
    constructor:Person,                 //手动指定constructor
    name:"Leon",
    age:23,
    say:function() {
        alert(this.name+","+this.age);
    }
}
p1.sayHi();                                 //不会报错，但是没有this.name
var p2 = new Person();
console.log(Person.prototype.isPrototypeOf(p1));        // false,Person.prototype指向了新的对象
console.log(proto.isPrototypeOf(p1));                           // true,保持原指向
//p2.sayHi();                                           //此时p2没有sayHi,所以就会报错
p2.say();                                               //以下方法正常调用
p1.say();                                               //报错，p1中没有say方法
```
![重写原型](/images/prototype-4.png)