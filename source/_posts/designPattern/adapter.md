---
title: 适配器模式
date: 2016-11-03 14:43:53
tags: 结构型模式
categories: 设计模式
---
>将一个类(对象)的接口(方法或属性)转化为另外一种接口，以满足用户需求，是类(对象)之间接口的不兼容问题通过适配器解决。

<!--more-->
# 代码库适配
```javascript
//如果是和jQuery很相似的类库
window.A = A = jQuery;      //假设有A类库与jQuery类库
//---------------------------------------------------------------------------------------------------//
//如果是异类类库
var A = A || {};
A.g = function(id){           //通过id获取元素
    return document.getElementById('id');
}
A.on = function(id,type,fn){                //为元素绑定事件
    var dom = typeof id === 'string' ? this.g(id) : id;         //传入字符串用g(id)获取对象，否则按对象处理
    if(dom.addEventListener){                           //addEventListener
        dom.addEventListener(type,fn,false);
    }else if(dom.attachEvent){                          //attachEvent
        dom.attachEvent(on+'type',fn);
    }else{                                                          //other
        dom[on+'type'] = fn;
    }
}
//应用实例
A.on(window,'load',function(){
    A.on('mybutton','click',function(){
        //action...
    })
});
//引入jQuery进行适配处理
A.g = function(id){
    return $(id).get(0);                //通过jQuery对象返回第一成员
}
A.on = function(id,type,fn){
    var dom = typeof id === 'string' ? $('#'+id) : $(id);             //传入字符串以id处理，否则按对象处理
    dom.on(type,fn);                   //利用jQuery的事件处理程序
}
/**
 * 通过和jQuery适配可简化封装，但如果两个库的差异太大，还是尽量不要引入。
 */
```
 
# 参数适配
```javascript 
function doSTH(name,title,age,color,size,prize){
    //需要传递多个个参数，而且顺序也不好记忆
    console.log('logic...');        //业务逻辑代码
}
//基于适配器的改造（以对象方式传入，且可以检查参数的完整性或设置默认值）
function doSTH(obj){
    var _adapter = {                //适配对象
        name:'lisi',
        tilte:'adapter',
        age:24,
        color:'pink',
        size:100,
        prize:50
    }
    for(var i in _adapter) {
        _adapter[i] = obj[i] || _adapter[i];        //得到传入的值及赋初值
    }
    console.log('logic...');                //业务逻辑代码
}
```
 
# 数据适配
```javascript
var arr = ['js','book','program','8.1'];        //数组对象
//把数组转换成对象
function arrToObjAdapter(arr){
    return {                //把数组元素提取出来返回对象的形式处理
        name:arr[0],
        type:arr[1],
        title:arr[2],
        time:arr[3]
    }
}
var data = arrToObjAdapter(arr);
console.log(data);              //{ name: 'js', type: 'book', title: 'program', time: '8.1' }
```
 
# 服务器端适配
```javascript
function ajaxAdapter(data){                 //为简化模型，理想数据是一个一维数组
    return [data['key1'],data['key2'],data['key3']];
}
$.ajax({
    url:'actionurl.action',
    success:function(data,status){
        if(data){
            var _data = ajaxAdapter(data);      //转成一维数组
            //对数组进行操作，这样在后端数据结构发生变化时，只需要修改适配器则可
        }
    }
})
``` 
# 应用场景
        适配几个代码库及前后端数据。

# 优缺点
        在解耦的同时，也增加了一些开销，但基本上算是微乎其微的。 
