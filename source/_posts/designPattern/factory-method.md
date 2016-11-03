---
title: 工厂方法模式
date: 2016-11-03 14:11:06
tags: 创建型模式
categories: 设计模式
---
>通过对产品类的抽象使其创建业务主要负责用于创建多类产品的实例。其本意是说将实际创建对象的工作推迟到子类中。

<!--more-->
# 简单工厂的主要缺陷
        对于创建多类对象时,当需求变动时，可能需要添加一个类和修改工厂方法，这样就会造成两次修改。

# 安全的工厂方法
```javascript
// 定义工厂
var Factory = function (type, content) {
//      console.log(this);                                      //global
    if (this instanceof Factory) {                        //如果是通过new关键字创建的工厂实例
//      console.log('Factory instance aready...');
        var s = new this[type](content);              //如果是Java,相当于new (FactoryInstance.Java('--Java--'))
        return s;                                                 //返回实例
    } else {                                                      //如果不是通过new的方式创建，而是直接调用工厂方法
//      console.log('new Factory...');
        return new Factory(type, content);
    }
}
// 挂载子类
Factory.prototype = {
    construtor: Factory,
    Java: function (content) {
        //java学科类
        console.log(content);
    },
    JavaScript: function (content) {
        //JavaScript学科类
        console.log(content);
    },
    php: function (content) {
        //php学科类
        console.log(content);
    },
    UI: function (content) {
        //UI学科类
        console.log(content);
    }
}
/**
 *执行顺序：
 *    1.首先是输入global对象
 *    2.因为global对象不是Factory类型所以会走else分支 new Factory的实例
 *    3.在new Factory的过程中又调用了构造方法，这次是通过new来创建的，会走if分支，这个时候的this其实是Factory的实例了
 *    4.Factory实例化完成，输出Factory instance aready...
 *    5.初始化相应的实例
 *    6.返回实例
 */
var js = Factory('JavaScript','--JavaScript--');                //--JavaScript--
console.log(js.constructor.toString());                           // function (content) {//JavaScript学科类     console.log(content);}
//模拟的数据
var data = [
    {type:'JavaScript',content:'--JavaScript--'},
    {type:'Java',content:'--Java--'},
    {type:'php',content:'--php--'},
    {type:'UI',content:'--UI--'},
    {type:'UI',content:'--UI--'},
    {type:'JavaScript',content:'--JavaScript--'},
    {type:'JavaScript',content:'--JavaScript--'}
];
//测试数据
/*for(var i = 6; i >= 0; i--){
 Factory(data[i].type,data[i].content);
 }*/
```

# 优势
        可以直接在工厂的原型的挂载子类而不必修改其他。同时也实现了具体子类对象的屏蔽。

# 适用场景
        创建多类对象。