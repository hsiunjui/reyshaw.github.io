---
title: 访问控制
date: 2016-11-01 17:42:32
tags: js扩展
categories: 客户端JS
---
>JavaScript中没有定义相关访问控制的关键字，是通过模拟来完成的。

<!--more-->
# 访问控制模拟
```javascript
var Book = function(id,name,price){
    var num = 0;                                //私有属性,相当于private num = 0;每个对象各执一份
    function checkId(){                      //私有方法,相当于private checkId();
        console.log('私有方法checkId...');
    }
    this.id = id;                                   //公开属性,相当于public id
 /*
  * 【可以通过如下方法设置或获取私有变量的值】
  */
    this.getNum = function(){
        return num;
    }
    this.setNum = function(_num){
        num+=_num;
    }
    //特权方法
    this.getName = function(){
        return this.name;
    }
    this.getPrice = function(){
        return this.price;
    }
    this.setName = function(name){
        this.name = name;
    }
    this.setPrice = function(price){
        this.price = price;
    }
    //构造器
    this.setName(name);
    this.setPrice(price);
}
Book.isChinese = '是';                 //类的静态公有属性（对象不能访问）,相当于public static isChinese;
Book.pubTime = function(){        //类的静态公有方法（对象不能访问）,相当于public static pubTime();
    return 'pub time...';
}
Book.prototype = {
    constructor:Book,
    isJsBook:'是',                         //公有属性
    display:function(){                 //公有方法
        return '原型方法display...';
    }
}
var book = new Book(11,'JavaScript设计模式',50);
//通过this索引的基本都通过对象点属性或方法的形式访问,因为它是public的
console.log('book.id-->'+book.id);                    //11
console.log('book.name-->'+book.name+',或book.getName()-->'+book.getName());              //JavaScript设计模式
console.log('book.price-->'+book.price+',或book.getPrice()-->'+book.getPrice());                  //50
//私有变量不能通过对象点属性来访问，必须使用对外公开的方法来访问，num属性在每个对象中均有一份，所以设置或修改的是自己的
console.log('book.num-->'+book.num);                           //undefined
book.setNum(10);
console.log('book.getNum()-->'+book.getNum());            //10
//不可以用对象访问类的静态公有属性和方法
console.log('book.isChinese-->'+book.isChinese);              //undefined
//console.log('book.pubTime()-->'+book.pubTime());          //报错,方法未定义
console.log('Book.isChinese-->'+Book.isChinese);            //是
console.log('Book.pubTime()-->'+Book.pubTime());           //pub time...
//对象可以访问原型属性及方法
console.log('book.isJsBook-->'+book.isJsBook);            //是
console.log('book.display()-->'+book.display());             //原型方法display...
/**
 *   可以简单地总结为：用var可以定义类的private属性,用this可以定义类的public属性。
 *                  -- 方法一般定义在prototype中
 */
```
# 利用闭包实现的静态私有变量
```javascript
var Book = (function(){              //用匿名函数自执行的方式来返回一个闭包函数，用于创建对象
    var bookNum = 0;                 //私有静态变量，各个对象共享一份，这个变量可以通过对象的公开接口方法访问
    function checkBook(){           //私有静态方法
    }
    return function(newId,newName){         //返回闭包，但bookNum的作用域还在
        var name;
        function checkID(id){
        }
        this.getNum = function(){           //用于对外提供访问接口，该方法不能写在原型中，因为原型中没有指向
            return bookNum;                    //这里不能是this.bookNum,因为bookNum是共有的，不属于特定的某个对象
        }
        this.id= newId;
        bookNum++;                             //内部使用静态私有变量，每创建一个对象，该值会加1
        if(bookNum>2){
            throw new Error('超出限制...');
        }
        this.setName(newName);
    }
})();
/**
 * 上面一段话执行完后，相当于Book =function(newId,newName){}；该函数保存了上级的作用域，即bookNum,checkBook还在内存中
 * 每次new的时候，不会再经过var bookNum = 0; function checkBook(){}的步骤了，因此它只有一份。
 */
Book.prototype = {
    constructor:Book,
    isJsBook:'是',
    display:function(){
        console.log('原型中的display...');
    },
    getName :function(){
        return this.name;
    },
    setName : function(newName){
        this.name = newName;
    }
}
var book1 = new Book(1,'java');
var book2 = new Book(2,'javaScript');
//var book3 = new Book(3,'javaScript设计模式');       //会抛超出限制的异常，因为只允许最大2个对象
//依然不能通过对象点属性的方式直接访问静态私有变量，但可以通过公开的接口
console.log(book1.bookNum);         //undefined
console.log(book1.getNum());        //2
console.log(book2.getNum());        //2
```

# 用闭包定义完整的类
```javascript
var Book = (function(){
    var bookNum = 0;                        //静态私有变量
    functioncheckBook(){                  //静态私有方法
    }
    function _book(newId,newName){          //定义类
        var name;                                           //私有变量
        function checkId(){                             //私有方法
        }
        this.id = newId;                                    //公有属性
        this.getNum = function(){                    //公有方法
            return bookNum;
        }
        bookNum++;
        if(bookNum > 2){
            throw new Error('limited...');
        }
        this.setName(newName);              //构造器
    }
    _book.prototype = {                           //构建原型
        constructor:_book,
        //特权方法
        setName : function(newName){
            this.name = newName;
        },
        getName : function(){
          return this.name;
        },
        idJsBook:false,                         //静态公有属性
        display:function(){                     //静态公有方法
            return 'display in prototype...';
        }
    }
    return _book;                               //返回类
})();
```

# 创建对象的安全模式
```javascript
var Book = function(name){
    if(this instanceof Book){            //如果是通过new关键字创建的
        this.name = name;
    }else {                                       //不是通过new创建的
        return new Book(name);      //这里又会去判断if..else了，这下是new的了，所以会有this.name = name;
    }
}
var b1 = new Book('java');           //new创建
console.log(b1.name);                //java
var b2 = Book('Javascript');        //直接调用创建
console.log(b2.name);               //javascript
```