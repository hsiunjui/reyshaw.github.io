---
title: 多继承
date: 2016-11-01 17:43:13
tags: js扩展
categories: 客户端JS
---
>通过对象复制实现多继承。

<!--more-->
# 浅复制
- 将一个对象拷贝到另外一个对象中，但不拷贝该对象中的子对象。也就是是说子对象是拷贝的引用地址。
```javascript
var extend = function(target,source){            //复制属性
    for(var property in source){                       //遍历源对象中的属性
        target[property] = source[property];      //将源对象中的属性复制到目标对象中
    }
    return target;                                  //返回目标对象
}
var book = {                    //源对象
    name:'JavaScript',
    alike:['html','css']        //数组类引用类型
}
var obook = {                 //目标对象
    color:'blue'
}
extend(obook,book);                         //完成源对象到目标对象的复制
console.log(obook.name);                //JavaScript
console.log(obook.alike.length);      //2
console.log(obook.color);                //blue
obook.alike.push('jQuery');         //由于是浅复制，将同时改变源对象和目标对象的值
console.log(obook.alike.length);     //3
console.log(book.alike.length);       //3
```

# 深复制
- 不仅复制对象的基本属性，同时也复制原对象中的对象，就是说完全是新对象产生，新对象所指向的不是原来的地址。
```javascript
var deepExtend = function(target,source){               //复制属性和对象（复制的对象是全新的对象）
    for(var property in source){                              //遍历源中的属性和对象
        if(typeof source[property] === 'object'){       //如果源中有子对象
            target[property] = {};                                //为目标子对象设置一个空对象
            if(source[property].constructor == Array){      //若为数组
                target[property] = [];                                   //则置为数组
            }
            target[property] = arguments.callee(target[property],source[property]);     //再递归子对象的属性
        }else{                                                               //如果是基本类型的属性值
            target[property] = source[property];            //直接复制过来
        }
    }
    return target;          //返回对象
}
var book = {                //源对象
    name:'JavaScript',
    alike:['html','css',{a:10,b:20}]            //数组类引用类型
}
var obook = {               //目标对象
    color:'blue'
}
deepExtend(obook,book);          //完成源对象到目标对象的复制
console.log(book);                     //{ name: 'JavaScript', alike: [ 'html', 'css', { a: 10, b: 20 } ] }
console.log(obook);                   //{ color: 'blue',name: 'JavaScript', alike: [ 'html', 'css', { a: 10, b: 20 } ]}
obook.alike[2].price = 50;          //由于是深复制，故不会造成源对象值的改变
console.log(obook.alike[2]);      //{ a: 10, b: 20, price: 50 }
console.log(book.alike[2])        //{ a: 10, b: 20 }
```
- 这种方式如果源对象和目标对象之间有相同属性或子对象的时，可能会发生目标对象属性或子对象被覆盖的情况


# 基于浅复制的多继承
```javascript
Object.prototype.mix = function(){      //将方法直接挂载到Object的原型上
    var i = 0,
        len = arguments.length,             //实际传递的参数个数
        arg;
    for(; i < len; i++){
        arg = arguments[i];                     //每一个复制的对象
        for(var property in arg){
            this[property] = arg[property];     //浅复制
        }
    }
}
var book = {                            //对象1
    name:'JavaScript',
    alike:['html','css',{a:10,b:20}]
}
var obook = {                          //对象2
    color:'blue'
}
var obj = new Obj();
obj.mix(book,obook);            //这里直接可以实现多继承
console.log(obj);                   //{id: 0,name: 'JavaScript',alike: [ 'html', 'css', { a: 10, b: 20 } ],mix: [Function],color: 'blue' }
function Obj(){             //定义函数对象
    this.id = 0;
}
```
- 基于深复制的原理也一样，只是改变成不同的复制方法。