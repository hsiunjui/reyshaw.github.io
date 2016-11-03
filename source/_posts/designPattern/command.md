---
title: 命令模式
date: 2016-11-03 15:36:34
tags: 行为型模式
categories: 设计模式
---
>将请求与实现解耦并封装成独立对象，从而使不同的请求对客户端的实现参数化。命令模式将创建模块的逻辑封装在一个对象里，这个对象提供一个参数化的请求接口，通过调用这个接口并传递一些参数实现调用命令对象内部中的一些方法。

<!--more-->
# 命令对象
```javascript
var viewCommand = (function () {
    var tpl = {
            product: [              //展示图片结构模板
                '<div>',
                '<img src="{#src#}"/>',
                '<p>{#text#}</p>',
                '</div>'
            ].join(''),                         //最终是字符串<div><img src="{#src#}"/><p>{#text#}</p></div>
            title: [                 //展示标题结构模板
                '<div class="title">',
                '<div class="main">',
                '<h2>{#title#}</h2>',
                '<p>{#tips#}</p>',
                '</div>',
                '</div>'
            ].join('')                      //join('')表示按空字符串链接数组
        },
        html = '';                  //格式化字符串缓存字符串
    function formatString(str, obj) {                   //替换'{#'与'#}'之间的字符
        return str.replace(/\{#(\w+)#\}/g, function (match, key) {      //key相当于对象的一个属性{#title#} -->title
            return obj[key];
        })
    }
    var Action = {
        create: function (data, view) {                 //创建方法,data是数据，view是模板(title/product)
            if (data.length) {                          //如果是数组
                for (var i = 0, len = data.length; i < len; i++) {
                    html += formatString(tpl[view], data[i]);       //用数据去格式化模板，并缓存
                }
            } else {                              //如果不是数组,可能是对象
                html += formatString(tpl[view], data);              //格式化并缓存
            }
        },
        display: function (container, data, view) {         //展示方法, 参数为页面节点,数据,视图
            if (data) {                         //如果传入数据则先去创建，不传数据就直接去拿原来的html直接展示。
                this.create(data, view);        //创建视图
            }
            document.getElementById(container).innerHTML = html;        //插入页面
            html = '';                              //清空缓存
        }
    };
    return function excute(msg) {
        msg.param = Object.prototype.toString.call(msg.param) === '[object Array]' ? msg.param : [msg.param];//若非数组转化为数组
        Action[msg.command].apply(Action, msg.param);           //执行命令
    };
})();
//测试数据
var productData = [
        {
            src: 'command/02.jpg',
            text: '绽放的桃花'
        },
        {
            src: 'command/03.jpg',
            text: '阳光下的温馨'
        },
        {
            src: 'command/04.jpg',
            text: '镜头前的绿色'
        }
    ],
    titleData = [
        {
            title: '夏日里的一片温馨',
            tips: '乱乱的温情带给人们家的感受。'
        }
    ];
//调用
viewCommand({               //展示一个标题模块
    command: 'display',        //去调用Action的display方法，因param是数组，直接将'titlebox',titleData,'title'传给了display
    param: ['titlebox', titleData, 'title']    //有数据，会调用create方法，把数据和模板给它组装赋给html,最后又display展示
});
viewCommand({               //创建一个图片,这里只是纯粹创建了一个模块，并不会展示到页面上。
    command: 'create',
    param: [
        {
            src: 'command/01.jpg',
            text: '迎着朝阳的野菊花'
        }, 'product']
});
viewCommand({               //创建多个图片，调用display会先拿到原来的html在拼接，完成展示后才清空
    command: 'display',
    param: ['productbox', productData, 'product']
});
``` 
# Canvas解耦(屏蔽canvas上下文引用的耦合)
```javascript
var CanvasCommand = (function () {
    var canvas = document.getElementById('canvas'),         //获取canvas
        ctx = canvas.getContext('2d');                      //获取上下文，缓存在了对象内部，只能通过接口访问
    var Action = {
        fillStyle: function (c) {                               //填充颜色
            ctx.fillStyle = c;
        },
        fillRect: function (x, y, width, height) {      //绘制矩形
            ctx.fillRect(x, y, width, height);
        }
    }
    return {
        excute: function (msg) {
            if (!msg) {                     //没有指令则返回
                return;
            }
            if (msg.length) {           //命令是一个数组
                for (var i = 0, len = msg.length; i < len; i++) {
                    arguments.callee(msg[i]);           //将数组元素作为参数重新调用，将走else的流程
                }
            } else {                         //执行单个命令
                msg.param = Object.prototype.toString.call(msg.param) === '[object Array]' ? msg.param : [msg.param];//非数组转化为数组
                Action[msg.command].apply(Action, msg.param);               //内部调用执行
            }
        }
    }
})()
//调用，传入数组
CanvasCommand.excute([
    {command: 'fillStyle', param: 'red'},
    {command: 'fillRect', param: [20, 20, 100, 100]}
]);
``` 
# 主要特征与功能
        封装功能，并提供简单而高效的API以提高团队开发的效率。 
        其将执行的命令封装，解决命令的发起者与执行者之间的耦合。

# 缺点
        没执行一次操作都要调用一次命令对象，增加了系统的复杂度，需酌情使用。 
 
