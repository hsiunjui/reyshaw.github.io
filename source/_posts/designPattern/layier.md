---
title: 惰性模式
date: 2016-11-03 16:22:20
tags: 技巧型模式
categories: 设计模式
---
>减少每次代码执行时的重复性的分支判断，通过对对象重定义来屏蔽原对象中的分支判断。

<!--more-->
# 执行事件时的重复分支判断
```javascript
var A = {};
A.on = function(dom,type,fn){               //事件绑定
    if(dom.addEventListener){
        dom.addEventListener(type,fn,false);
    }else if(dom.attachEvent){
        dom.attachEvent('on'+type,function(){
            fn.call(this);
        })
    }else{
        dom['on'+type] = fn;
    }
}               //每次执行事件的时候都要重复判断分支
```

# 加载即执行
```javascript
var A = {};
A.on = (function (dom, type, fn) {               //通过匿名只执行重新定义函数
    if (dom.addEventListener) {
        return function (dom, type, fn) {
            dom.addEventListener(type, fn, false);
        }
    } else if (dom.attachEvent) {
        return function (dom, type, fn) {
            dom.attachEvent('on' + type, function () {
                fn.call(this);
            })
        }
    } else {
        return function (dom, type, fn) {
            dom['on' + type] = fn;
        }
    }
})();               //只在页面加载时执行一次，但首次加载会消耗一定的性能
```

# 惰性执行
```javascript
var A = {};
A.on = function (dom, type, fn) {
    if (dom.addEventListener) {
        A.on = function (dom, type, fn) {           //显示重定义,调用一次后自身被重写
            dom.addEventListener(type, fn, false);
        }
    } else if (dom.attachEvent) {
        A.on = function (dom, type, fn) {
            dom.attachEvent('on' + type, function () {
                fn.call(this);
            })
        }
    } else {
        A.on = function (dom, type, fn) {
            dom['on' + type] = fn;
        }
    }
    A.on(dom,type,fn);      //执行重定义方法，A.on被重写
}                                       //函数第一次调用时对其重新定义
A.on(document.body,'click',function(){  //第一次绑定后才会被重写 此时A.on已经是一个固定的事件模式了，在标准模式下，相当于
    console.log('111');                             /*A.on = function (dom, type, fn) {     //下次调用会走这个分支
                                                                dom.addEventListener(type, fn, false);
                                                             }*/
});
```

# 其他应用
        在创建XHR对象时通常也可以采用该模式，以及在其他用到浏览器功能检测的方法中常常使用。

# 主要功能
        解决每次使用浏览器提供的功能方法时对浏览器重复性的功能校验问题。
 
# 两种方式
        其一是通过闭包的方式在文件加载时即执行(在文件加载时会耗用一定的资源);
        其二是当第一次使用时重新定义对象(第一次执行的时间会增加)。 