---
title: 简单工厂
date: 2016-11-03 14:02:15
tags: 创建型模式
categories: 设计模式
---
>简单工厂又称为静态工厂方法，由一个工厂对象决定创建某一种产品对象类的实例。主要用来创建同一类对象。

<!--more-->
# 基于面向对象的登录检测
```javascript
/**
 *   用户名密码输入格式检测类
 */
var LoginAlert = function(text){
    this.content = text;
}
LoginAlert.prototype.show =function() {
    console.log(this.content);
}
//检测用户名--当用户输入超出16位时调用
var loginAlert = new LoginAlert('用户名不能超出16位...');
loginAlert.show();                                  //输出用户名不能超出16位...
//检测密码（可以实现复用）-- 当密码输入错误的时候调用
var passwordAlert = new LoginAlert('密码不正确...');
passwordAlert.show();                               //输出密码不正确...
//以上代码可以做到复用，但是在用户名不存在，在提示框中添加一个注册按钮便不能复用了，这时需要定义一个新的类
/**
 *   用户名不存在创建注册按钮类 
 */
var LoginConfirm = function(text){
    this.content = text;
}
LoginConfirm.prototype.show = function(){
    console.log(this.content);
    console.log('在这里创建和显示注册按钮...');           //额外的功能
}
var loginFailConfirm = new LoginConfirm('用户名不存在...');
loginFailConfirm.show();                    // 输出用户名不存在...并创建和显示出注册的按钮（功能需要封装）
//但登录成功后需要显示提示语，以上代码又不可以复用，又需要定义个新的类
/**
 *    登录成功后显示提示信息类
 */ 
var LoginPrompt = function(text){
    this.content = text;
}
LoginPrompt.prototype.show = function(){
    console.log(this.content);
    console.log('在这里创建显示欢迎信息...');           // 额外功能
}
var loginSuccessPrompt = new LoginPrompt('登录成功...');
loginSuccessPrompt.show();                  //输出登录成功...并显示相应的欢迎功能（功能需要封装）
/**
 *   缺点：每调用一个功能都要找到相应的类及方法。
 */
```
 
# 简单工厂(返回类的实例)
```javascript
/**
 *   登录检测工厂类，可以很好地实现复用
 */ 
var LoginFactory = function(type,content){
    switch(type){
        case 'alert':
            return new LoginAlert(content);
        case 'confirm':
            return new LoginConfirm(content);
        case 'prompt':
            return new LoginPrompt(content);
    }
}
//基于工厂的调用,这样不用关心里面实现的细节
var loginAlert1 = new LoginFactory('alert','用户名不能超出16位...');
loginAlert1.show();
var loginConfirm1 = new LoginFactory('confirm','用户名不存在...');
loginConfirm1.show();
var loginPrompt1 = new LoginFactory('prompt','登录成功...');
loginPrompt1.show();
```

# 简单工厂(类似寄生式继承的方式创建)
```javascript
/**
 *  定义简单工厂创建方法
 */ 
var createPop =  function(type,text){
    var o = new Object();       //创建一个对象，并对对象拓展属性和方法
    o.content = text;
    o.show = function(){
        console.log(o.content);
        if(type == 'alert'){        // 方法里面区别对待
            //警示框
            console.log('alert...');
        }
        if(type == 'confirm'){
            //用户名不存在的时候
            console.log('confirm...');
        }
        if(type == 'prompt'){
            //登录成功欢迎语
            console.log('prompt...');
        }
    }
    return o;
}
//基于工厂方法的调用
var loginAlert2 = createPop('alert','用户名不能超过16位...');
loginAlert2.show();
var loginConfirm2 = createPop('confirm','用户名不存在...');
loginConfirm2.show();
var loginPrompt2 = createPop('prompt','登录成功...');
loginPrompt2.show();
``` 
 
# 两种工厂方式的区别
            通过类实例化对象创建的工厂中，通过类创建的对象，如果这些类继承同一父类，那么他们的父
        类的原型上的方法是可以共用的。而通过寄生方式创建的对象都是一个新的个体，所以他们的方法就
        不能共用了。 
 
# 简单工厂模式适用的场景
        通常是创建单一对象时适用。 