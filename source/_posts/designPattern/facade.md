---
title: 外观模式
date: 2016-11-03 14:38:40
tags: 结构型模式
categories: 设计模式
---
> 为一组复杂的子系统接口提供一个更高级的统一接口，通过这个接口使得对子系统的访问更加容易。

<!--more-->
# 事件的绑定的问题
```javascript
document.onclick = function(e){         //DOM0级事件，重新定义会被覆盖
    e.preventDefault();                         //e并不兼容所有浏览器
    if(e.target !== document.getElementById('myinput')){
        //执行业务代码
        console.log('do...');
    }
}
```

# 基于外观模式的封装
```javascript
//添加事件函数
function addEvent(dom,type,fn){
    if(dom.addEventListener){                   //支持DOM2级addEventListener方法的浏览器
        dom.addEventListener(type,fn,false);
    }else if(dom.attachEvent){                  //不支持addEventListener但支持attachEvent的浏览器
        dom.attachEvent('on'+type,fn);
    }else{                                                 //两个方法均不支持的浏览器
        dom['on'+type] = fn;
    }
}
//获取事件对象
var getEvent = function(event){
    return event || window.event;                         //标准浏览器返回event，IE浏览器返回window.event
}
//获取元素
var getTarget = function(event){
    var event = getEvent(event);                        //封装event
    return event.target || event.srcElement;        //标准浏览器中返回event.target,IE中返回event.srcElement;
}
//阻止默认行为
var preventDefault = function(event){
    var event = getEvent(event);
    if(event.preventDefault){           //标准浏览器
        event.preventDefault();
    }else{
        event.returnValue = false;      //IE浏览器
    }
}
//经过外观模式的改造
addEvent(document,'click',function(event){              //添加事件
    var event = getEvent(event);                                //获取事件对象
    preventDefault(event);                                          //阻止默认行为
    if(getTarget(event) !== document.getElementById('myinput'));        //判断元素
});
```
 
# 其他应用
    很多代码库中通过外观模式来封装多个功能，简化底层操作方法。
    因此可以用它来构建一些JavaScript类库。

# 优点
    外观模式封装的接口方法不需要接口的具体实现，只需要按照接口使用规则使用即可。
    这也是系统与客户(使用者)之间的一种松散耦合，简化操作。

# 缺点
    增加了一些资源开销以及程序的复杂性。但对于使用成本来说也可忽略不计。 
