---
title: 节流模式
date: 2016-11-03 16:13:04
tags: 技巧型模式
categories: 设计模式
---
>对重复的业务逻辑进行节流控制，执行最后一次操作并取消其他操作，以提高性能。

<!--more-->
# 页面滚动
- scroll事件会一直触发滚动事件，重复执行事件函数。创建节流器进行函数的节流。
```html
<div style="width:800px; height: 4000px; background: red; margin:0 auto;"></div>
<script>
    function extend(obj, param) {      //初始化参数
        for (var i in param) {
            obj[i] = param[i] || param[i];
        }
        return obj;
    }
    //节流器
    var throttle = function () {
        var isClear = arguments[0], fn;         //获取第一个参数
        if (typeof isClear === 'boolean') {           //第一个参数时布尔类型，则表示是否清除定时器
            fn = arguments[1];                                      //获取第二函数参数
            fn.__throttleID && clearTimeout(fn.__throttleID);       //函数的计时器句柄存在则清除定时器
        } else {                                             //第一个参数不是布尔类型（是函数）
            fn = isClear;                                          //获取到函数
            var param = arguments[1];                           //获取到第二个参数
            var p = extend({                                    //对执行时的参数适配默认值
                context: null,                                            //执行函数执行作用域
                args: [],                                                    //执行函数执行时的相关参数
                time: 300                                                 //执行函数延迟执行时间
            }, param);
            arguments.callee(true, fn);                     //清除执行函数计时器句柄
            fn.__throttleID = setTimeout(function () {          //为函数绑定计时器句柄，延迟执行函数
                fn.apply(p.context, p.args);                                  //执行函数
            }, p.time);
        }
    }
    window.addEventListener('scroll', function (e) {           //滚动事件
        throttle(function () {
            console.log('test....');                                //节流执行滚动事件，延迟连续触发
        })
    }, false)
</script>
```

# 二维码等的鼠标事件应用(使鼠标划入划出不显突兀)
```html
<div id="icon" style="width:200px; height: 200px; background: red"></div>
<script>
    var icon = document.getElementById('icon');
    icon.addEventListener('mouseenter', function () {
        var that = this;
        throttle(true, function () {
            that.style.background = 'red';
        });                                     //清除显示红色背景方法计时器
        throttle(function () {
            that.style.background = 'green';
        });                                    //延迟显示绿色背景浮层方法
    }, false);
    icon.addEventListener('mouseleave', function () {
        var that = this;
        throttle(true, function () {
            that.style.background = 'green';
        });                                     //清除显示绿色背景方法计时器
        throttle(function () {
            that.style.background = 'red';
        });                                     //延迟显示红色背景方法
    }, false);
</script>
```
 
# 图片的延迟加载(只加载可视区的图片-延迟执行)
```html
<div id="container" style="width:800px;height: 1000px; position: relative;background: red;">
    <img src="images/load.gif" alt="" data-src="images/1.jpg" style="position:absolute;bottom:0;"/>
</div>
<script>
    function LazyLoad(id) {
        this.container = document.getElementById(id);       //图片容器
        this.imgs = this.getImgs();                                        //缓存图片
        this.init();                                                                   //初始化
    }
    LazyLoad.prototype = {
        construtor: LazyLoad,
        init: function () {
            this.update();            //加载当前视图图片
            this.bindEvent();       //绑定事件
        },
        getImgs: function () {
            var arr = [];
            var imgs = this.container.getElementsByTagName('img');
            for (var i = 0, len = imgs.length; i < len; i++) {      //将获取的图片转成数组（兼容IE操作）
                arr.push(imgs[i]);
            }
            return arr;
        },
        update: function () {
            if (!this.imgs.length) {        //如果图片都加载完成返回
                return;
            }
            var i = this.imgs.length;
            for (--i; i >= 0; i--) {        //遍历图片
                if (this.shouldShow(i)) {       //是否是可视区域的图片
                    this.imgs[i].src = this.imgs[i].getAttribute('data-src');     //加载源图
                    this.imgs.slice(i, 1);                                                        //清除缓存
                }
            }
        },
        shouldShow: function (i) {
            var img = this.imgs[i],                                                                                                     //当前图片
                    scrollTop = document.documentElement.scrollTop || document.body.scrollTop,   //页面滚动条top值
                    scrollBottom = scrollTop + document.documentElement.clientHeight,                  //可是范围内底部高
                    imgTop = this.pageY(img),                                                                                     //图片的顶部位置
                    imgBottom = imgTop + img.offsetHeight;          //图片的底部位置，offsetHeight一般是 内容+padding(+滚动条宽度)
            //console.log('[scrollTop:'+scrollTop+'][scrollBottom:'+scrollBottom+'][imgTop:'+imgTop+'][imgBottom:'+imgBottom+']');
            if (imgBottom > scrollTop && imgBottom < scrollBottom || (imgTop > scrollTop && imgTop < scrollBottom)) {
                return true;        //图片位于客户区下方且 图片的下方小于滚动页面下方  或在可视区内
            }
            return false;
        },
        pageY: function (element) {
            if (element.offsetParent) {                     //返回最近的祖先定位元素(position的)
                return element.offsetTop + this.pageY(element.offsetParent);
            } else {
                return element.offsetTop;                   //offsetTop一般获取相对于版面或有offsetTop属性指定的父元素的计算顶端位置
            }
        },
        on: function (element, type, fn) {          //简单事件绑定
            if (element.addEventListener) {
                element.addEventListener(type, fn, false);
            } else if (element.attachEvent) {
                element.attachEvent('on' + type, fn);
            }
        },
        bindEvent: function () {
            var that = this;
            this.on(window, 'resize', function () {         //窗口大小改变节流
                throttle(that.update, {context: that});
            });
            this.on(window, 'scroll', function () {         //滚动事件节流
                throttle(that.update, {context: that});
            })
        }
    }
    new LazyLoad('container');          //实例化
</script>
```
 
# 统计打包(当触发次数达到某一值时才发送请求)
```html
<button id="btn">test</button>
<script>
    var LogPack = (function () {
        var data = [],                          //请求缓存数组
                MaxNum = 10,              //请求缓存最大值
                itemSplitStr = '|',            //统计项统计参数间隔符
                keyValueSplitStr = '*',    //统计项统计参数键值对间隔符
                img = new Image();       //请求触发器，通过src属性实现简单的get请求
        function sendLog() {
            var logStr = '',
                    fireData = data.splice(0, MaxNum);
            //console.log(data);  []
            for (var i = 0, len = fireData.length; i < len; i++) {
                logStr += 'log' + i + '=';              //添加统计项顺序索引
                //console.log(logStr);
                for (var j in fireData[i]) {            //遍历统计项内统计参数
                    logStr += j + keyValueSplitStr + fireData[i][j];        //添加统计项键 + 分隔符 + 值
                    logStr += itemSplitStr;                                    //添加统计项统计参数间隔符
                }
                logStr = logStr.replace(/\|$/, '') + '&';                   //统计多个统计项间隔符
            }
            logStr += 'logLength=' + len;                   //添加统计项打包长度
            img.src = './images/1.jpg?' + logStr;           //请求触发器发送统计
        }
        return function (param) {
            if (!param) {           //无参数则发送统计
                sendLog();
                return;
            }
           // console.log(data);
            data.push(param);       //添加统计项
            data.length >= MaxNum && sendLog();  //统计项大于请求缓存最大值则发送统计请求包
            console.log(img.src);
        }
    })();
    btn.onclick = function () {
        LogPack({
            btnId: this.id,
            context: this.innerHTML,
            type: 'click'
        });
    }
/*    点击10下输出:
    http://localhost:63342/nodeproject/course/images/1.jpg
    ?log0=btnId*btn|context*test|type*click
    &log1=btnId*btn|context*test|type*click
    &log2=btnId*btn|context*test|type*click
    &log3=btnId*btn|context*test|type*click
    &log4=btnId*btn|context*test|type*click
    &log5=btnId*btn|context*test|type*click
    &log6=btnId*btn|context*test|type*click
    &log7=btnId*btn|context*test|type*click
    &log8=btnId*btn|context*test|type*click
    &log9=btnId*btn|context*test|type*click
    &logLength=10*/
</script>
```

# 主要功能
         解决在页面中的一些简单交互造成事件重复触发的问题，
         屏蔽一些无意间触发的事件以及统计打包发送。 
 
# 核心思想
        创建计时器，延迟程序执行。这也使得计时器中的回调函数异步执行。

# 优点
       程序是否执行时可控的。程序是异步的。  
