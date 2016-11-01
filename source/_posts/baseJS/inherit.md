---
title: JavaScript继承
date: 2016-11-01 17:40:18
tags: js高级
categories: 客户端JS
---
>JavaScript实现继承的方式。

<!--more-->
# 类式继承(通过原型链实现继承)
```javascript
function Parent(){                              // 定义父类自有属性
    this.pv = 'parent';    
}
Parent.prototype.pp = 'ok';                     // 定义父类原型属性和方法
Parent.prototype.showParentValue = function(){
    console.log(this.pv);
}
function Child(){                               // 定义子类自有属性
    this.cv = 'child';
}
Child.prototype = new Parent();                 // 继承父类
Child.prototype.showChildValue = function(){    // 添加子类方法
    console.log(this.cv);
}
/*
    Child.prototype.showParentValue = fucntion(){   // 重写继承来的方法
        console.log('overwrite parent'); 
    }
*/    
var  c = new Child();                           // 创建子类
c.showChildValue();                             // 自身方法 
c.showParentValue();                            // 继承来的方法
console.log(c.pp);                              // 继承来的属性
```
- 注意：不能在设定原型值后再重写（Json方式）原型
- 缺陷
	+ 无法从子类构造器中调用父类构造器
	+ 如果父类中有引用类型， 这个引用类型会添加到子类的原型中, 会在子类对象之间发生混淆（见上节原型创建对象）
- 内存图示意
	![类式继承](/images/inherit-1.png)
	

# 构造函数继承(基于函数伪造的方式实现继承)
```javascript
function Parent( name){
    this.color = ['red','blue'];
    this.name = name;
}
function Chlid( name){                   //  可以把子类的构造参数传递到父类中
    Parent.call(this,name);              // 当new出对象后this便指向了Child的对象, 从而改变了call的上下文
}
var child1 = new Chlid('Leon');
child1.color.push('green');         // 仅仅是修改child1下color数组，不会修改原型, 避免了原型继承的问题
var child2 = new Child('Lucy');
console.log(child2.length)          // 输出2
```
- 该方式可以实现参数的向上传递及解决父类引用类型属性的问题,但依然有缺陷： 
```javascript
 //如果父类中有方法的话，不会完成Child的原型指向Parent, 如以下定义后Child中并没有say方法
            Parent.prototype.say = function(){
               console.log(this.name);
            }
//        但可以使用this来创建:
            function Parent(name){
               this.name = name;
               this.say =function(){
                   console.log(this.name);
                }
             }
//        这样又会出现每一个Child 对象中都有say方法的一个拷贝,浪费空间。
```
 
# 组合继承(基于原型和伪造的组合方式)
```javascript
function Parent(name){                                    //该方式子类不是父类的实例，子类原型才是
    this.name = name;
    this.color = ['red','blue'];
}
Parent.prototype.ps = function(){
    console.log(this.name+' '+this.color.length);
}
function Child(name,age){
    Parent.call(this,name);                              // 属性通过伪造继承
    this.age = age;
}
Child.prototype = new Parent();                         // 方法通过原型继承
Child.prototype.say = function(){
    console.log(this.name+' '+this.age+' '+this.color.length);
}
var c1 = new Child('Leon',22);
cq.color.push('green');                             //  存储在对象空间，不会修改原型
var c2 = new Child('Ada',23);
c2.say();                                           // color的长度依然是2
```
- 组合方式有效解决了参数传递及控制内存等问题是常见的继承模式.但父类的构造器会被调用两遍
- 内存图示意
	![组合继承](/images/inherit-2.png)

# 原型式继承(基于封装的类式继承)
```javascript
function extend(o){
    function F(){           //声明一个过渡函数对象
    }
    F.prototype = o;        //过渡对象的原型继承父对象
    return new F();         //返回过渡对象的实例，该实例的原型继承了父对象
}
//与类式继承同样的缺点：
var book = {                //父类对象
    alike:['html','css']
}
var book1 = extend(book);               //继承自book
book1.alike.push('javascript');         //为属性添加项
//console.log(book1.alike.length);
var book2 = extend(book);
console.log(book2.alike.length);        //3  改变了其他对象的值
console.log(book.alike.length);          //3   改变了父类对象的值
```
 
# 寄生式继承(对原型式继承的第二次封装)
```javascript
function extend(o){
    function F(){
    }
    F.prototype = o;
    return new F();
}
var book = {
    alike:['html','css']
}
function create(obj){               //只能说是原型式继承的升级版
    var o = new extend(obj);    //通过原型继承方式创建新对象
    o.getName = function(){     //拓展新对象
        console.log(name);
    }
    return o;
}
var book1 = create(book);       //创建继承后的新对象，该方式只是拓展了继承后的功能，同样会有改变引用变量的问题
```

# 寄生组合式继承(基于寄生式继承和构造函数)
```javascript
function extend(o){
    function F(){
    }
    F.prototype = o;
    return new F();
}
function extendCreate(subClass,superClass){    //拿到父类原型new出一个对象，修正构造器指向在赋给子类原型
    var p = extend(superClass.prototype);           //复制一份父类原型的副本在extend函数中相当于F.prototype = o.prototype
    p.constructor = subClass;                       //因为重写原型导致constructor指向发生了修改
    subClass.prototype = p;                         //设置子类原型
}
var SuperClass = function(name){            //定义父类
    this.name = name;                               //构造属性
    this.colors = ['red','blue','green']           //引用类型属性
}
SuperClass.prototype.getName = function(){  //定义父类原型方法
    return this.name;
}
var SubClass = function(name,time){          //定义子类
    SuperClass.call(this,name);                    //构造函数式继承
    this.time = time;                                      //子类新增属性
}
extendCreate(SubClass,SuperClass);
SubClass.prototype.getTime = function(){       //子类新增原型方法，不能放在extendCreate之前，因为extendCreate会重写子类原型
    return this.time;
}
var sub1 = new SubClass('java',2014);
var sub2 = new SubClass('javascript',2013);
sub1.colors.push('black');
console.log(sub2.colors.length);                     //3   改变sub1的引用类型不会影响sub2,这是因为基于构造函数的继承
console.log(sub1.getName());                                //java    继承自父类的方法
console.log(sub1.getTime());                                 //2014    自己的原型方法
console.log(sub1 instanceof SubClass);                //true
console.log(sub1 instanceof SuperClass);             //true
console.log(SubClass instanceof SuperClass);     //false
console.log(SubClass.prototype instanceof SuperClass);             //true,因为extend return new F();
```
- 这样父类构造函数就只会执行一次，这种方式是比较理想的。
 
 
