---
title: 桥接模式
date: 2016-11-03 14:58:25
tags: 结构型模式
categories: 设计模式
---
> 在系统沿着多个维度变化的同时，不增加其复杂度并达到解耦。其实方式是通过先抽象提取共用部分，然后将实现与抽象通过桥接方法链接在一起，以实现解耦。

<!--more-->
# 主要特点
    将实现成(如元素绑定事件)与抽象层(如修饰页面UI逻辑)解耦分离，使两部分可以独立变化。

# 事件特效的绑定
```javascript
var spans = document.getElementsByTagName('span');      //获取需要绑定事件的元素
//第一个元素
spans[0].onmouseover = function(){      //鼠标悬停
    this.style.color = 'red';
    this.style.background = '#ddd';
}
spans[0].onmouseout = function(){       //鼠标离开
    this.style.color = '#333';
    this.style.background = '#f5f5f5';
}
//第二个元素
spans[1].onmouseover = function(){
    this.getElementsByTagName('strong')[0].style.color = 'red';             //元素下的strong元素变化
    this.getElementsByTagName('strong')[0].style.background = '#ddd';
}
spans[1].onmouseover = function(){
    this.getElementsByTagName('strong')[0].style.color = '#333';             //元素下的strong元素变化
    this.getElementsByTagName('strong')[0].style.background = '#f5f5f5';
}
/**
 *  1.事件均相同
 *  2.处理逻辑也相似(改变字体颜色和背景颜色)
 *  3.不同在于处理逻辑的主体不一样
 */
``` 
 
# 抽离与桥接
```javascript
//抽离
function changeColor(dom, color, bg) {
    dom.style.color = color;                  //设置字体
    dom.style.background = bg;          //设置颜色
}
//使用匿名函数进行桥接
var spans = document.getElementsByTagName('span');          //获取需要绑定事件的元素
spans[0].onmouseover = function(){                   //使用匿名函数，而不是直接spans[0] = changeColor【因为这样还是耦合在一起】
    changeColor(this, 'red', '#ddd');
}
spans[1].onmouseout = function(){
    changeColor(this.getElementsByTagName('strong')[0],'#333','#f5f5f5');
}
``` 
# 多维度桥接
```javascript
//---多维变量类----//
//运动单元
function Speed(x, y) {
    this.x = x;
    this.y = y;
}
Speed.prototype.run = function () {
    console.log('Speed run function...');
}
//着色单元
function Color(cl) {
    this.color = cl;
}
Color.prototype.draw = function () {
    console.log('Color draw function...');
}
//变形单元
function Shape(sp) {
    this.shape = sp;
}
Shape.prototype.change = function () {
    console.log('Shape change function...');
}
//说话单元
function Speak(wd) {
    this.word = wd;
}
Speak.prototype.say = function () {
    console.log('Speak say function...');
}
//---创建球类----//
function Ball(x, y, c) {
    this.speed = new Speed(x, y);        //实现运动单元
    this.color = new Color(c);               //实现着色单元
}
Ball.prototype.init = function(){
    this.speed.run();                   //实现运动
    this.color.draw();                  //实现着色
}
//---创建人类----//
function People(x, y, f) {
    this.speed = new Speed(x, y);        //实现运动单元
    this.font = new Speak(f);                //实现说话单元
}
People.prototype.init = function(){
    this.speed.run();                   //实现运动
    this.font.say();                      //实现说话
}
//---创建精灵类----//
function Spirite(x, y, c,s) {
    this.speed = new Speed(x, y);        //实现运动单元
    this.color = new Color(c);               //实现着色单元
    this.shape = new Shape(s);           //实现变形单元
}
Spirite.prototype.init = function(){
    this.speed.run();                           //实现运动
    this.color.draw();                          //实现着色
    this.shape.change();                   //实现变形
}
//创建实体
var p = new People(10,12,16);
p.init();                                           //有运动和说话的功能了。
/**
 *  每个形象类的init方法其实起到的就是一个桥接的作用。
 *  这体现面向对象对拓展的开放及对修改的关闭原则
 */
``` 