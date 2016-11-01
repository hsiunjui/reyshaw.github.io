---
title: 多态
date: 2016-11-01 17:43:35
tags: js扩展
categories: 客户端JS
---
>多态就是同一种方法(不同参数)不同的调用方式。由于JavaScript中不能实现重载所以只有通过判断arguments的参数来模拟。

<!--more-->
# 多态模拟
```javascript
function Addition(){                 //定义加法类
    function zero(){                   //无参调用
        return 0;
    }
    function one(num){                  //一个参数调用
        return num + 10;
    }
    function two(num1,num2){       //两个参数调用
        return num1 + num2;
    }
    this.add = function(){
        var arg = arguments.length;         //arg表示实际传入参数的个数
        switch (arg){
            case 0:
                return zero();
            case 1:
                return one(arguments[0]);
            case 2:
                return two(arguments[0],arguments[1]);
        }
    }
}
var ads = new Addition();
console.log(ads.add());                     //0
console.log(ads.add(10));                //20
console.log(ads.add(10,20));           //30
```
