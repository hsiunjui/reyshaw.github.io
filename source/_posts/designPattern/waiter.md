---
title: 等待者模式
date: 2016-11-03 16:31:11
tags: 技巧型模式
categories: 设计模式
---
>通过对多个异步进程监听，来触发未来发生的动作。等待者模式或等待者对象是用来解决那些不确定先后完成的异步逻辑的。

<!--more-->
# 等待者对象
```javascript
var Waiter = function () {
    var dfd = [],                           //注册了等待对象的容器
        doneArr = [],                     //成功回调方法容器
        failArr = [],                        //失败回调方法容器
        slice = Array.prototype.slice,   //缓存数组的slice方法
        that = this;                              //保存当前等待者对象
    var Primise = function () {           //监控对象
        this.resolved = false;                      //监控对象是否解决成功状态
        this.rejected = false;                       //监控对象是否解决失败状态
    }
    Primise.prototype = {
        constructor: Primise,
        resove: function () {       //解决成功
            this.resolved = true;       //设置当前对象解决成功
            if (!dfd.length) {              //如果没有监控对象，则返回
                return;
            }
            for (var i = dfd.length - 1; i >= 0; i--) {     //遍历监控对象
                if (dfd[i] && !dfd[i].resolved || dfd[i].rejected) {    //如果任意一个监控对象未被解决或解决失败则返回
                    return;
                }
                dfd.splice(i, 1);       //清除已解决对象
            }
            _exec(doneArr);             //执行解决成功回调函数(要dfd为空时这里才能执行到)
        },
        reject: function () {       //解决失败
            this.rejected = true;       //设置当前对象解决失败
            if (!dfd.length) {              //如果没有监控对象则取消执行
                return;
            }
            dfd.splice(0);               //清除所有监控对象
            _exec(failArr);             //执行解决失败的回调函数
        }
    }
    that.Deferred = function () {         //创建监控对象
        return new Primise();                   //返回监控实例
    }
    function _exec(arr) {          //回调执行方法
        var i = 0,
            len = arr.length;
        for (; i < len; i++) {          //遍历回调数组并执行
            try {
                arr[i] && arr[i]();
            } catch (e) {
                conosle.log('回调失败..');
            }
        }
    }
    that.when = function () {      //监控异步方法 参数 监控对象
        dfd = slice.call(arguments);       //设置监控对象
        var i = dfd.length;
        for (--i; i >= 0; i--) {
            //如果不存在监控对象，或者监控对象已解决，或者不是监控对象则移除
            if (!dfd[i] || dfd[i].resolved || dfd[i].rejected || !dfd[i] instanceof Primise) {
                dfd.splice(i, 1);
            }
        }
        return that;
    }
    that.done = function () {     //解决成功回调函数添加方法
        doneArr = doneArr.concat(slice.call(arguments)); //向成功回调数组中添加回调方法
        return that;
    }
    that.fail = function () {     //解决失败回调函数添加方法
        failArr = failArr.concat(slice.call(arguments)); //向失败回调数组中添加回调方法
        return that;
    }
}
var waiter = new Waiter();      //创建等待者对象
var first = (function () {
    var dtd = waiter.Deferred();
    setTimeout(function () {
        console.log('first finish');
        dtd.resove();
    }, 2000);
    return dtd;
})();
var second = (function () {
    var dtd = waiter.Deferred();          //创建监听
    setTimeout(function () {
        console.log('second finish');
        dtd.resove();                             //发布解决成功消息
    }, 5000)
    return dtd;                                     //返回监听对象
})();
waiter.when(first, second).done(function () {
    console.log('success');
}, function () {
    console.log('success again');
}).fail(function () {
    console.log('fail');
})
/*输出
first finish        //2s后输出
second finish       //5s后输出
success
success again
*/
```

# 框架中的等待者
```javascript
$.ajax('xxx.php')
.done(function(){           // 成功
    conosle.log('success');
})
.fail(function(){           // 失败
    conosle.log('fail');
})
```

# 封装异步请求
```javascript
var ajaxGet = function (url, success, fail) {
    var xhr = new XMLHttpRequest();
    var dtd = waiter.Deferred();            //创建检测对象
    xhr.onreadystatechange = function (event) {
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
            success && success();
            dtd.resove();               //解决成功
        } else {
            dtd.reject();                 //解决失败
            fail && fail();
        }
    }
    xhr.open('get', url, true);
    xhr.send(null);
}
```
# 应用场景
        处理耗时比较长的操作及异步逻辑。
        如canvas遍历并操作大图的像素点、定时器操作、异步请求及轮询等。 
 