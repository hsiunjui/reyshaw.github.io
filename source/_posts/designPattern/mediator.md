---
title: 中介者模式
date: 2016-11-03 15:43:33
tags: 行为型模式
categories: 设计模式
---
>通过中介者对象封装一系列对象之间的交互，是对象之间不再相互引用，降低他们之间的耦合。有时中介者对象也可以改变对象之间的耦合。

<!--more-->
# 中介者对象
```javascript
var Mediator = (function () {
    var _msg = {};                      //消息对象
    return {
        rigister: function (type, action) {
            if (_msg[type]) {                   //如果该消息类型存在
                _msg[type].push(action);        //存入回调函数
            } else {                                 //如果消息类型不存在
                _msg[type] = [];                      //创建消息容器
                _msg[type].push(action);        //存入新消息回调函数
            }
        },
        send:function(type){
            if(_msg[type]){                     //如果该消息已经被订阅
                for(var i = 0,len = _msg[type].length;i<len;i++){       //遍历已存储的消息回调函数
                    _msg[type][i] && _msg[type][i]();                          //执行该回调函数
                }
            }
        }
    }
})();
Mediator.rigister('demo',function(){            //注册消息
    console.log('first...');
});
Mediator.rigister('demo',function(){            //再次注册消息
    console.log('second...')
});
Mediator.send('demo');      //first...  second...
```

# 主要应用
        中介者模式同观察者模式一样，其主要是通过模块间或者对象间的复杂通信(消息传递)，
        来解决模块间或对象间的耦合。

# 中介者模式与观察者模式的区别
         观察者模式的订阅者是双向的，既可以是消息的发布者，也可以是消息的订阅者。
         而中介者模式中，订阅者是单向的，只能是消息的订阅者。
         而消息统一由中介者对象发布，所有的订阅者对象间接地被中介者管理。

# 中介者模式与外观模式的封装特性区别
         中介者模式对多个对象交互地封装，且这些对象一般处于同一层面，并且封装的交互在中介者内部。
         而外观模式的封装目的是为了提供更简单的易用接口，而不会添加其他功能。