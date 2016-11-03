---
title: 装饰者模式
date: 2016-11-03 14:55:21
tags: 结构型模式
categories: 设计模式
---
>在不改变原有对象的基础上，同过对其进行包装扩展(添加属性或方法)，使原有对象可以满足用户更复杂的需求。

<!--more-->
# 扩展输入框的功能
```javascript
var tel = document.getElementById('tel_input');
tel.onclick = function(){
    tel.style.background='red';
    //新增需求写在下面
}
/**
 *  传统方式扩展input的功能需要找到其业务代码，并在适当的位置添加逻辑。
 */ 
```
 
# 使用装饰者
```javascript
//装饰者模式
var decorator = function(input,fn){                               //传入事件源的id和新增功能函数
    var input = document.getElementById(input);         //获取事件源
    if(typeof input.onclick === 'function'){                //若已经绑定过事件
        var oldClick = input.onclick;                           //缓存事件原有回调函数
        input.onclick = function(){                              //为事件源重新定义新的事件
            oldClick();                             //调用原有回调函数
            fn();                                      //执行事件源新增回调函数
        }
    }else{                              //如果未绑定过事件
        input.onclick = fn;       //直接绑定事件
    }
}
//调用
decorator('tel_input',function(){
    //新功能
})
``` 
# 优点
        可以不了解原有功能的前提下拓展新功能。

# 装饰者模式与适配器模式的区别
         适配器进行拓展很多时候是对对象内部结构的重组，因此了解其自身结构是必须的。
         而装饰者对对象的拓展是一种良性的拓展，不用了解其具体实现，只是在外部进行一次封装拓展，
         这也是对原有功能完整性的一种保护。
 
