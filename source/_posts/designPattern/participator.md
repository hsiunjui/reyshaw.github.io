---
title: 参与者模式
date: 2016-11-03 16:25:38
tags: 技巧型模式
categories: 设计模式
---
> 在特定的作用域中执行给定的函数，并将参数原封不动地传递。

<!--more-->
# 事件绑定缺陷
```html
<body>
<div id="btn">点击</div>
<script>
    var A = A || {};
    var t1 = 'test1', t2 = 'test2';
    A.on = function (dom, type, fn) {       //事件绑定，以标准浏览器为例
        dom.addEventListener(type, fn, false);
    }                                                     //缺陷：无法向回调函数中传递自定义参数
    function Alert() {
        console.log('test');
    }
    A.on(document.getElementById('btn'),'click',Alert);    //点击输出test
</script>
</body>
 
向回调函数中传递自定义参数：
<div id="btn">点击</div>
<script>
    var A = A || {};
    A.on = function (dom, type, fn, data) {
        dom.addEventListener(type, function (e) {
            fn.call(dom, e, data);
        }, false);
    }                                 //缺陷：无法移除事件回调函数
    function Alert() {                            //此处不定义形参数，传递参数通过arguments接收
        console.log(arguments[1]);      //默认的arguments[0]是事件对象
    }
    A.on(document.getElementById('btn'), 'click', Alert, 'hello world'); //点击输出hello world
</script>
```
 
# 函数绑定
```html
<div id="btn">点击</div>
<script>
    function bind(fn,context){      //函数绑定就是让一个函数在一个作用域中执行
        return function(){                                           //闭包返回新函数
            return fn.apply(context,arguments);         //对fn装饰并返回
        }
    }                                            //高版本浏览器可以直接用fn.bind(ofn)
    var btn = document.getElementById('btn');
    function test(){
        console.log(arguments,this);
    }
    var bindTest = bind(test);                   //未设置提供参与对象，默认为window
    btn.addEventListener('click',bindTest);             //[MouseEvent] window
    //btn.removeEventListener('click',bindTest);     //正常移除    
    bindTest = bind(test,btn);                  //设置参与对象btn
    btn.addEventListener('click',bindTest);             //[MouseEvent] <div id="btn">点击</div>
    btn.removeEventListener('click',bindTest);       //正常移除
</script>
```

# 函数柯里化
```html
<div id="btn">点击</div>
<script>
    var btn = document.getElementById('btn');
    function bind(fn, context) {        //函数柯里化似面向对象中的多态，其思想是对函数参数的分割
        var args = Array.prototype.slice.call(arguments, 2);          //从第三个参数开始截取(包括第三个参数)
        return function () {
            var addArgs = Array.prototype.slice.call(arguments),    //将参数转化为数组
                    allArgs = args.concat(addArgs);                             //拼接参数
            return fn.apply(context, allArgs);                                     //装饰fn并返回
        }
    }
    function test() {
        console.log(arguments,this);
    }
    var testData = {
        text: '这是一组测试数据'
    }
    var bindTest = bind(test, btn, testData);
    btn.addEventListener('click',bindTest,false);           //[Object, MouseEvent] <div id="btn">点击</div>   Object就是testData
</script>
```

# 全局添加
```javascript
if (Function.prototype.bind === undefined) {
   Function.prototype.bind = function(context){
       var args = Array.prototype.slice.call(arguments,1),  //从第二个参数开始截取
               that = this;                                                         //保存当前函数引用
       return function(){
           var addArgs = Array.prototype.slice.call(arguments),     //将参数数组化
                   allArgs = args.concat(addArgs);                              //拼接参数
           return that.apply(context,allArgs);                                    //对当前函数进行装饰返回
       }
   }
}
```
 
# 反柯里化思路
```javascript
Function.prototype.uncurry = function () {
    var that = this;                                         //保存当前对象
    return Function.prototype.call.apply(that, arguments);   //在元素本身执行
}
// 未测试通过
```
 
# 参与者模式的实质
        其是两种技术的结晶，即函数绑定与函数柯里化。对于函数绑定，它将函数以函数指针
        (函数名)的形式传递，使函数在被绑定的对象作用域中执行(因函数绑定构造复杂，
        执行耗内存，速度稍慢；常用于事件及setTimeout/setInterval) 。
        对于函数柯里化即是将接受多个参数的函数转化为接受一部分参数的新函数，余下的参数保存下来，
        当函数调用时，返回传入参数与保存参数共同执行的结果(由于采用闭包，固也需要消耗一定内存)。
 
