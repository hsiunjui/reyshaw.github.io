---
title: 单例模式
date: 2016-11-03 14:32:49
tags: 创建型模式
categories: 设计模式
---
>也称为单体模式，是只允许实例化一次的对象类。有时也用一个对象来规划一个命名空间井井有条的管理对象上的属性与方法。

<!--more-->
# 单例的代码管理体现
- 通过命名空间来约束每个人定义的变量来解决在开发当中变量或方法重名的冲突。
```javascript
//创建小型代码库，通过统一的对象管理各自的方法
var A = {
    Util:{
        util_method1:function(){},
        util_method2:function(){}
    },
    Tool:{
        tool_method1:function(){},
        tool_method1:function(){},
    },
    Ajax:{
        ajax_method1:function(){},
        ajax_method1:function(){},
    },
    Others:{
        other_method1:function(){},
        other_method1:function(){},
    }
}
//调用方式
A.Util.util_method1();
A.Tool.tool_method1();
A.Ajax.ajax_method1();
```
# 利用单例管理静态变量
- 通过在匿名自执行函数中定义局部变量，之体统特权访问方法，不提供特权修改方法。
```javascript
var Conf = (function(){          //利用匿名函数自执行之初始化私有变量一次
    var conf = {                       //私有变量
        MAX_NUM:100,
        MIN_NUM:1,
        COUNT:1000
    };
    return {                        //返回对象或者函数的方式向外提供访问接口
        get:function(name){
            return conf[name] ? conf[name] : null;
        }
    }
})();                               //执行到此,Conf已然是一个对象了,而不需要其他任何调用，相当于Conf = {get:function(){...}};
//访问私有变量
console.log(Conf.get('MAX_NUM'));         //100
```
 
# 惰性单例(延迟创建)
- 也就是说需要调用或创建才会初始化静态私有变量。
```javascript
//惰性载入单例
var LazySingle = (function(){
    var _instance = null;                   //单例实例引用
    function Single(){                      //单例
        return {                            //定义私有方法和属性
            publicMethod:function(){},
            publicProperty:'1.0'
        }
    }
    return function(){                  //返回一个函数
        if(!_instance){                 //单例未创建者创建
            _instance = Single();       //执行这里会返回一个对象{ publicMethod:function(){},publicProperty:'1.0'}
        }
        return _instance;               //返回单例
    }
})();    //执行到此相当于 LazySingle = function(){ if(!_instance){ _instance = Single();} return _instance; }，它是一个函数并保留了闭包作用域
//访问方式：
console.log(LazySingle().publicProperty);          //1.0    _instance = {publicMethod:function(){},publicProperty:'1.0'}
//LazySingle.Single();                             //报错
var pb = LazySingle();                            //_instance还存在，return了 _instance，相当于 pb = _instance 同一引用
pb.publicProperty = 10;                           //_instance中属性改变
console.log(pb.publicProperty)                    //10
console.log(LazySingle().publicProperty);         //10,先是返回了_instance，后取属性，因为在前两步中属性值已改变，所以是10
/**
 * 由此可以看出_instance始终存在，并且只有一份。
 */
``` 

# 适用场景
      系统中只允许存在一个对象或管理命名空间以及模块化代码组织。 

