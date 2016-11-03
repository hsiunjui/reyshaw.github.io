---
title: 原型模式
date: 2016-11-03 14:27:08
tags: 创建型模式
categories: 设计模式
---
>用原型实例指向创建对象的类，使用于创建新的对象的类共享原型对象的属性以及方法。 

<!--more-->
# 基于原型模式的轮播图示例
```javascript
//图片轮播基类
var LoopImages = function(imgArr,container){
    this.imagesArray = imgArr;                  //轮播图片数组
    this.container = container;                   //轮播图片容器
}
LoopImages.prototype = {
    constructor:LoopImages,
    createImage:function(){                       //创建轮播图
        console.log('LoopImages createImage function...');
    },
    changeImage:function(){                     //轮播图的切换方式
        console.log('LoopImages changeImage function...');
    }
}
//上下滑动切换类
var SlideLoopImg = function(imgArr,container){
    LoopImages.call(this,imgArr,container);                     //构造继承图片轮播基类
}
SlideLoopImg.prototype = new LoopImages();              //类式继承父类方法
SlideLoopImg.prototype.changeImage = function(){     //重写父类切换方法
    console.log('SlideLoopImg changeImage function...');
}
//渐隐切换类
var FadeLoopImg = function(imgArr,container,arrow){
    LoopImages.call(this,imgArr,container);                 //构造继承图片轮播基类
    this.arrow = arrow;
}
FadeLoopImg.prototype = new LoopImages();          //类式继承父类方法
FadeLoopImg.prototype.changeImage = function(){  //重写父类切换方法
    console.log('FadeLoopImg changeImage function...');
}
//扩展父类方法
LoopImages.prototype.getImageLength = function(){
    return this.imagesArray.length;
}
//扩展子类方法
FadeLoopImg.prototype.getContainner = function(){
    return this.container;
}
//test
var fadeImg = new FadeLoopImg(['01.jpg','02.jpg','03.jpg','04.jpg','05.jpg'],'container',['leftarrow','rightarrow']);
console.log(fadeImg.container);                     //container         继承自父类构造
fadeImg.changeImage();                               //FadeLoopImg changeImage function...  覆盖父类的方法后得到
console.log(fadeImg.getImageLength());      //5         继承自父类方法
console.log(fadeImg.getContainner());         //container 自身扩展
``` 
# 原型的另类继承
        创建一个实例对象的构造函数比较复杂，或者耗时比较长，或者通过创建过个对象来实现时，这时
    最好不要用new关键字去复制这些基类，但可以通过对这些对象属性或者方法进行复制来实现创建。这
    里便可以利用浅复制或深复制来实现。

# 优点
        原型模式可以让多个对象分享同一个原型对象的属性与方法，这也是一种继承方式，不过不需要创
     建，而是将原型对象分享给那些继承的对象。

# 使用场景
        原型对象适用于创建复杂对象，相对于那些需求一直在变化而导致对象的结构不停地改变时，通过
    将那些比较稳定的属性和方法共用而提取的继承的实现。