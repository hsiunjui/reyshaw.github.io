---
title: 职责链模式
date: 2016-11-03 15:28:55
tags: 行为型模式
categories: 设计模式
---
>解决请求的发送者与请求的接收者之间的耦合，通过职责链上的多个对象对分解请求流程，实现请求在过个对象间的传递，直到最后一个对象完成请求的处理。

<!--more-->
# ajax请求分解
```javascript
var sendData, dealData, createSug, createValidateResult;
/**
 * 异步请求对象（简化版） ---请求模块
 *  data 请求数据
 *  dealType  响应数据处理对象
 *  dom  事件源
 */
sendData = function (data, dealType, dom) {
    var xhr = new XMLHttpRequest(),                         //XHR对象 简化版本 IE另行处理
        url = 'getData.php?mod=userInfo';                    //请求路径
    xhr.onload = function (event) {                         //请求返回事件
        if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
            dealData(xhr.responseText, dealType, dom);              //响应成功的处理函数
        } else {
            //请求失败
        }
    }
    for (var i in data) {                   //拼接请求字符串
        url += '&' + i + '=' + data[i];
    }
    xhr.open('get', url, true);        //发送异步请求
    xhr.send(null);
}
/**
 * 处理响应数据   ---响应数据适配模块
 * data  响应数据
 * dealType 响应数据处理对象
 * dom  事件源
 */
dealData = function (data, dealType, dom) {
    var dataType = Object.prototype.toString.call(data);        //将响应的数据转换为字符串
    switch (dealType) {
        case 'sug':                                                 //处理类型为sug(输入框提示)
            if (dataType === '[object Array]') {            //响应数据为数组
                return createSug(data, dom);
            }
            if (dataType === '[object Object]') {          //响应数据为对象
                var newData = [];
                for (var i in data) {                                      //把对象转换成数组
                    newData.push(data[i]);
                }
                return createSug(newData, dom);
            }
            return createSug([data], dom);                      //响应数据为其他对象则将响应数据转换成数组
            break;
        case 'validate':                                       //处理类型为validate(验证)
            return createValidateResult(data, dom);
            break;
    }
}
/**
 *  创建提示框组件   ---组件模块
 *  data 响应适配数据
 *  dom  事件源
 */
createSug = function (data, dom) {
    var i = 0,
        len = data.length,
        html = '';
    for (; i < len; i++) {                          //遍历数据
        html += '<li>' + data[i] + '</li>';   //增加节点
    }
    dom.parentNode.getElementsByTagName('ul')[0].innerHTML = html;          //插入页面
}
/**
 *  创建校验组件
 *  data  响应适配数据
 *  dom 事件源
 */
createValidateResult = function (data, dom) {
    dom.parentNode.getElementsByTagName('span')[0].innerHTML = data;      //显示校验结果
}
/**
 * 单元测试：
 dealData('用户名不正确','validate',input[0]);              //会在span中显示用户名不正确
 dealData(123,'sug',input[1]);                             //会在ul中添加一项
 dealData(['爱奇艺','爱电影','爱漫画'],'sug',input[1]);     //会在ul中添加三项
 dealData({iqy:'爱奇艺',ady:'爱电影',amh:'爱漫画'},'sug',input[1]);       //会在ul中添加三项
 */
/**
 *  实际项目运用
 */
var input = document.getElementsByTagName('input');
input[0].onchange = function (e) {
    sendData({value: input[0].value}, 'validate', input[0]);   //发送数据验证
}
input[1].onkeyup = function (e) {
    sendData({value: input[1].value}, 'sug', input[1]);        //发送数据获取数据项
}
```
 
# 实现过程
       将负责的需求颗粒化逐一实现每个对象分内的需求，并将请求顺序传递。
       对于职责链上的每一个对象，它都可以是请求的发送者，也可以是请求的接收者。

# 优点
       简化原有对象的复杂度，对原请求的发起者和原请求的接收者解耦。同时也方便了单元测试。
       另外在中途插入或减少的请求，实现起来也会比较方便。
       但是要合理运用请求的传递，避免资源的浪费。 
