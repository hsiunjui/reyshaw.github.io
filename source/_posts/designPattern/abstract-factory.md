---
title: 抽象工厂
date: 2016-11-03 14:16:02
tags: 创建型模式
categories: 设计模式
---
>通过对类的工厂抽象使其业务用于对产品类族的创建，而不负责创建某一类产品的实例。抽象工厂其实是一个类实现继承父类的方法，所以它的本质是一个方法，一般不需要实例化对象，故只需要一份。

<!--more-->
# JavaScript抽象类
- 在JavaScript中，抽象类一般不用来创建具体对象。抽象类中定义的方法只是显性的定义一些功能，但没有具体实现，也不允许直接调用。而一个对象是要具有一套完整的功能的，所有用抽象创建的对象也是"抽象的"。所以不能用它来创建一个真实的对象。一般用抽象类作为父类创建一些子类。
```javascript
var Car = function(){}          // 定义抽象类
Car.prototype = {               // 方法不能直接调用
    constructor:Car,
    getPrice : function(){
        return new Error('抽象方法不能直接调用');
    },
    getSpeed : function() {
        return new Error('抽象方法不能直接调用');
    }
}                                // var car = new Car(); 也是抽象的
```
 
# 抽象工厂方法
- 其实质是在抽象方法内部实现子类到父类的继承，在其属性上挂载不同的抽象类 。
```javascript
//抽象工厂方法
var VehicleFactory = function(sbuType,superType){
    if(typeof VehicleFactory[superType] === 'function'){          //判断抽象工厂中是否有该抽象类
        function F(){}                                                                  //定义缓存类, 寄生式继承
        F.prototype = new VehicleFactory[superType]();          //这里是父类实例,而不是prototype,因为要继承父类属性和方法
        subType.constructor = subType;                                  //修正子类构造指向
        subType.prototype = new F();                                     //子类原型继承父类
    }else{
        throw Error('未创建该抽象类');                                  //不存在该抽象类抛错
    }
}
//小汽车抽象类
VehicleFactory.Car = function(){
    this.type = 'car';
}
VehicleFactory.Car.prototype = {
    constructor:VehicleFactory.Car,
    getPrice:function(){
        return new Error('抽象方法不能直接调用');
    },
    getSpeed : function() {
        return new Error('抽象方法不能直接调用');
    }
}
//公交车抽象类
VehicleFactory.Bus = function(){
    this.type = 'bus';
}
VehicleFactory.Bus.prototype = {
    constructor:VehicleFactory.Bus,
    getPrice:function(){
        return new Error('抽象方法不能直接调用');
    },
    getSpeed : function() {
        return new Error('抽象方法不能直接调用');
    }
}
//货车抽象类
VehicleFactory.Truck = function(){
    this.type = 'truck';
}
VehicleFactory.Truck.prototype = {
    constructor:VehicleFactory.Truck,
    getPrice:function(){
        return new Error('抽象方法不能直接调用');
    },
    getSpeed : function() {
        return new Error('抽象方法不能直接调用');
    }
}
``` 
# 抽象工厂方法应用
```javascript
//宝马汽车子类
var BMW = function(price,speed){
    this.price = price;
    this.speed = speed;
}
VehicleFactory(BMW,'Car');      //抽象工厂实现对Car抽象类的继承
BMW.prototype.getPrice = function () {          //覆写方法
    return this.price;
}
BMW.prototype.getSpeed = function () {          //覆写方法
    return this.speed;
}
//宇通汽车子类
var YUTONG = function(price,passenger){
    this.price = price;
    this.passenger = passenger;
}
VehicleFactory(YUTONG,'Bus');      //抽象工厂实现对Bus抽象类的继承
YUTONG.prototype.getPrice = function () {          //覆写方法
    return this.price;
}
YUTONG.prototype.getPassengerNum = function () {
    return this.passenger;
}
//奔驰汽车子类
var BenzTruck = function(price,trainload){
    this.price = price;
    this.trainload = trainload;
}
VehicleFactory(BenzTruck,'Truck');      //抽象工厂实现对Truck抽象类的继承
BenzTruck.prototype.getPrice = function () {          //覆写方法
    return this.price;
}
BenzTruck.prototype.getTrainload = function () {
    return this.trainload;
}
var bmw = new BMW(1000000,1000);
console.log(bmw.type);              //car
console.log(bmw.getPrice());        //1000000
``` 

# 优势
    可以判断子类的类别。通过重写父类的方法正确地得到结构。 适用于多分类多之类的状况。
 
# 应用情况
        由于JavaScript中不支持抽象化创建于虚拟方法（ES6之前），这种模式引用较少。 