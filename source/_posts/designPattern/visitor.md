---
title: 访问者模式
date: 2016-11-03 15:39:31
tags: 行为型模式
categories: 设计模式
---
> 针对于对象结构中的元素，定义在不改变该对象的前提下访问结构中元素的新方法。

<!--more-->
# IE的attachEvent引起的this问题
```html
<div id="demo">click</div>
<script>
    var demo = document.getElementById('demo');
     demo.attachEvent('onclick',function(e){     //attachEvent不标准，会将this指向window,IE11已不支持该方法
        alert(this === window);                               //IE11以下均为true
        this.style.background = 'red';
     });                                            //用bind(demo)可以修复，但只在IE9,IE10中有效
</script>
```

# 访问操作元素(demo对象)
```javascript
 //为操作元素添加的新方法
function bindIEEvent(dom, type, fn, data) {
    var data = data || {};                                          //这里可以接收自定义数据，是对绑定事件的拓展
    dom.attachEvent('on' + type, function (e) {
        fn.call(dom, e, data);                                 //改变回调函数的作用域使其this指向demo对象
    })
}
bindIEEvent(demo, 'click', function (e, d) {
    //    alert(this === demo);                                                       //true
    this.innerHTML = e.type + d.text + this.tagName;                //click test DIV
}, {text: 'test'});                                                              //其实质是匿名函数执行了call方法，data数据照一般的方式处理            
```
 
# 基于访问者模式的事件模型
```javascript
var bindEvent = function (dom, type, fn) {                  //事件绑定封装
    if (dom.addEventListener) {
        dom.addEventListener(type, fn, false);
    } else if (dom.attachEvent) {
        dom.attachEvent('on' + type, function (e) {        //修正attachEvent的this问题:绑定this到dom对象
            fn.call(dom, e);               //用call或apply方法改变函数执行时的作用域，这是访问者模式的精髓          
        })
    } else {
        dom['on' + type] = fn;
    }
}
bindEvent(demo, 'click', function (e) {
    alert(this === window);                                 //false
});
```
 
# 对象访问器
```javascript
var Visitor = (function () {      // 访问者
    return {
        splice: function () {           //--splice方法向/从数组中添加/删除项，以数组的形式返回被删除的项目
            var args = Array.prototype.splice.call(arguments, 1);         //截取到对象数组或push过来的参数数组，返回被截取的数组
            return Array.prototype.splice.apply(arguments[0], args);    //获得对象或push过来的对象数组
        },
        push: function () {             //--追加元素到末尾
            var len = arguments[0].length || 0;               //缓存原有长度
            var args = this.splice(arguments, 1);           //返回截取后的数组
            arguments[0].length = len + arguments.length - 1;           //设置length属性
            return Array.prototype.push.apply(arguments[0], args);   //在对象上添加数组的项
        },
        pop: function () {               //--弹出末尾元素
            return Array.prototype.pop.apply(arguments[0]);             //在对象上弹出
        }
    }
})();
var a = new Object();
Visitor.push(a, 1, 2, 3, 4);
//Visitor.splice(a,1);
console.log(a);                         //{ '0': 1, '1': 2, '2': 3, '3': 4, length: 4 }
``` 
# 关于arguments
- arguments对象是一个类数组，但它不是真正的数组，它是Arguments的实例。函数点arguments的形式是只读的，但函数的arguments对象在传递过程中可能会被改变。
```javascript
function test01() {        //这里暂且把arguments看成数组，一下输出结果是在nodejs和浏览器环境下的不同输出
    //组装“数组” [test01->arguments,1],此arguments是test01的对象,非传进来的test02的arguments,是包含test02的arguments的关系
    console.log(arguments);                 //{ '0': { '0': {}, '1': 3, '2': 4 }, '1': 1 }  [Arguments[3], 1]
    //调用数组删除方法，返回的是一个被删除元素的数组。test01的arguments发生改变
    var args = Array.prototype.splice.call(arguments, 1);
    //由于只取数组的第一项，故此时的“数组”为 [test01->arguments],虽然只有一个元素，但它还是一个“数组”
    console.log(arguments);                 //{ '0': { '0': {}, '1': 3, '2': 4 } }  [Arguments[3]]
    //把元素从“数组”中取出来，取的test01的arguments[0]便是test02的arguments
    var temp = Array.prototype.splice.apply(arguments[0], args);
    //test01的arguments[0]的值改变，同时也引起了test02的arguments的改变，因为是同一引用，test01的arguments对象的值改变
    console.log(arguments);                 //{ '0': { '0': {} } }   [Arguments[1]]
    //其实test01的arguments[0]便是test01的arguments，一直都是
    console.log(arguments[0]);              //{ '0': {} }   [Object]
}
function test02() {
    //test02.arguments;                 //这种写法其内容是只读的
    console.log(arguments);         //{ '0': {}, '1': 3, '2': 4 }  [Object, 3, 4]
    test01(arguments, 1);
    console.log(arguments);         //{ '0': {} }  [Object]  ---明显test02的arguments已被改变
}
var a = new Object();
test02(a,3,4);
``` 
# 应用场景
        访问者模式解决数据与数据操作方法之间的耦合，适合于那些数据稳定，
        但数据的操作方法易变得环境下。 