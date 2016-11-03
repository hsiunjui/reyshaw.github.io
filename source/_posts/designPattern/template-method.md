---
title: 模板方法模式
date: 2016-11-03 15:11:40
tags: 行为型模式
categories: 设计模式
---
>父类中定义一组操作算法骨架，而将一些实现步骤延迟到子类中，使得子类可以不改变父类的算法结构的同时可以重新定义算法中某些实现步骤(个人感觉更有继承的韵味)。

<!--more-->
# 创建弹框
```javascript
//模板类  基础提示框 data渲染数据
var Alert = function (data) {
    if (!data) {                                          //没有传入数据，就返回
        return;
    }
    this.content = data.content;                                             //设置内容
    this.panel = document.createElement('div');                 //创建提示面板
    this.contentNode = document.createElement('p');        //创建提示内容组件
    this.confirmBtn = document.createElement('span');     //创建确定按钮组件
    this.closeBtn = document.createElement('b');             //创建关闭按钮组件
    this.panel.className = 'alert';                                         //为提示框按钮添加样式
    this.confirmBtn.className = 'a-confirm';                        //为确定按钮添加样式
    this.closeBtn.className = 'a-close';                               //我关闭按钮添加样式
    this.confirmBtn.innerHTML = data.confirm || '确定';             //为确定按钮添加文案
    this.contentNode.innerHTML = this.content;                       //为提示内容添加文本
    this.success = data.success || function () {
        };                                                                    //点击确定按钮执行的方法
    this.fail = data.fail || function () {
        };                                                                   //点击关闭按钮执行的方法
}
//模板类原型方法
Alert.prototype = {
    constructor: Alert,
    init: function () {
        this.panel.appendChild(this.contentNode);           //生成提示框
        this.panel.appendChild(this.closeBtn);
        this.panel.appendChild(this.confirmBtn);
        document.body.appendChild(this.panel);              //插入页面中
        this.bindEvent();                                       //绑定事件
        this.show();                                              //显示提示框
    },
    bindEvent: function () {
        var that = this;                                                //缓存对象
        this.closeBtn.onclick = function () {                     //点击关闭按钮事件
            that.fail();                                    //执行关闭取消方法
            that.hide();                                 //隐藏弹层
        }
        this.confirmBtn.onclick = function () {                  //点击确认按钮事件
            that.success();                          //执行关闭确认方法
            that.hide();                                //隐藏弹层
        }
    },
    hide: function () {                                        //隐藏弹层
        this.panel.style.display = 'none';
    },
    show: function () {                                      //显示弹层
        this.panel.style.display = 'block';
    }
}
/****拓展弹层类型******/
//右侧按钮提示框
var RightAlert = function (data) {
    Alert.call(this, data);                              //继承提示框构造函数
    this.confirmBtn.className = this.confirmBtn.className + ' right';
}
RightAlert.prototype = new Alert();           //继承基本提示框方法
//标题提示框
var TitleAlert = function (data) {
    Alert.call(this, data);                 //继承提示框构造函数
    this.title = data.title;                  //设置标题内容
    this.titleNode = document.createElement('h3');      //创建标题组件
    this.titleNode.innerHTML = this.title;                       //将标题内容写入标题组件
}
TitleAlert.prototype = new Alert();              //继承基本提示框方法
TitleAlert.prototype.init = function(){                  //重写init方法
    this.panel.insertBefore(this.titleNode,this.panel.firstChild);      //添加标题组件
    Alert.prototype.init.call(this);                                                    //继承基本提示框的init方法
}
//用继承类作为模板类，创建带有取消按钮的标题提示框
var CancelAlert = function(data){
    TitleAlert.call(this,data);                         //继承标题提示框构造函数
    this.cancel = data.cancel;                                                   //取消按钮文案
    this.cancelBtn = document.createElement('span');            //创建取消组件
    this.cancelBtn.className = 'cancel';                                 //取消按钮样式
    this.cancelBtn.innerHTML = this.cancel || '取消';               //取消按钮文案
}
CancelAlert.prototype = new Alert();                //继承基本提示框原型方法
CancelAlert.prototype.init = function(){
    TitleAlert.prototype.init.call(this);                   //继承标题提示框的init方法
    this.panel.appendChild(this.cancelBtn);       //添加到提示面板末尾
}
CancelAlert.prototype.bindEvent = function(){
    var that = this;
    TitleAlert.prototype.bindEvent.call(this);              //继承bindEvent方法
    this.cancelBtn.onclick = function(){
        that.fail();                           //执行取消回调函数
        that.hide();                         //隐藏提示框
    }
}
//实例调用
new CancelAlert({                   //传入对象，并初始化
    title:'提示标题',
    content:'提示内容',
    success:function(){
        console.log('ok');
    },
    fail:function(){
        console.log('cancel');
    }
}).init();
```

# 创建多类型导航
```html
<div id="content"></div>
<script>
    function formatString(str, data) {                                             //格式化字符串方法
        return str.replace(/\{#(\w+)#\}/g, function (match, key) {
            return typeof data[key] === 'undefined' ? '' : data[key];
        })
    }
    var Nav = function (data) {                                     //基础导航
        this.item = '<a href="{#href#}" title="{#title#}">{#name#}</a><br/>';        //基础导航样式模板
        this.html = '';
        for (var i = 0, len = data.length; i < len; i++) {                                          //格式化数据
            this.html += formatString(this.item, data[i]);
        }
        return this.html;
    }
    var NumNav = function (data) {                   //带有消息提醒信息导航
        var tpl = '<b>{#num#}</b>';                             //消息提醒信息组件模板
        for (var i = data.length - 1; i >= 0; i--) {            //装饰数据
            data[i].name += formatString(tpl, data[i]);
        }
        return Nav.call(this, data);
    }
    var LinkNav = function (data) {                                   //带有链接地址的导航
        var tpl = '<span>{#href#}</span>';                                  //链接地址模板
        for (var i = data.length - 1; i >= 0; i--) {
            data[i].name += formatString(tpl, data[i]);
        }
        return Nav.call(this, data);                                             //继承基础导航并返回字符串
    }
    var nav = document.getElementById('content');
    nav.innerHTML = LinkNav([
        {
            href: 'http://www.baidu.com',
            title: '百度一下，你就知道',
            name: '百度',
            num: '10'
        },
        {
            href: 'http://www.taobao.com',
            title: '淘宝商城',
            name: '淘宝',
            num: '2'
        },
        {
            href: 'http://www.qq.com',
            title: '腾讯首页',
            name: '腾讯',
            num: '3'
        }
    ]);
</script>
```
 
# 实现原理
        其核心是方法的重用。将核心方法封装在基类中以实现共享，
        这列方法通常是不变的的算法或者具有稳定的调用方式。 
