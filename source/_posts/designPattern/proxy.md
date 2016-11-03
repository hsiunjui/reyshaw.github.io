---
title: 代理模式
date: 2016-11-03 14:47:41
tags: 结构型模式
categories: 设计模式
---
>有时一个对象不能直接引用另外一个对象，所以需要通过代理对象在这两个对象之间其到中介的作用。

<!--more-->
# 统计代理 
- 基于img标签的src属性
```javascript 
//统计代理
var Count = (function () {
    var _img = new Image();                         //缓存图片
    return function (param) {                         //返回统计函数
        var str = 'http//:www.count.com?';       //统计请求字符串
        for (var i in param) {                            //拼接请求参数
            str += i + '=' + param[i];
        }
        _img.src = str;                 //请求完整路径
    }
})();
Count({name:10});           //相当于发送了http://www.count.com?name=10的请求
```
 
# JSONP代理
- 基于script标签的src属性
```html
<!--前端浏览器页面-->
<script>
    function jsonpCallback(res,req){        //回调函数，打印响应和请求数据
        console.log(res,req);
    }
</script>
```
```html
<!--另一域下服务器请求接口-->
<script type="text/javascript" src="http://localhost/jsonp.php?callback=jsonpCallback&data=getJsonpData"></script>
```
```php
//后端获取请求字段数据，并生成返回内容
<?php
     $data = $_GET["data"];                     //getJsonpData
     $callback = $_GET['callback'];             //jsonpCallback
     echo $callback."('success', '".$data."')"; //返回jsonpCallback('sucess','getJsonpData'),之后会在前端打印输出结果
?>
```
 
# 代理模板
- 基于内嵌框架(iframe)和服务器端中转
```html
<!--X域中被代理页面A,发送代理地址及回掉函数在服务器端进行组装-->
<script>
    function callback(data) {                               //回调函数
        console.log('成功接收数据：', data);
    }
</script>
<iframe name='proxyIframe' id='proxyIframe' src=''></iframe>                <!--用来加载子页面-->
<form action='http://localhost/test/proxy.php' method='post' target='proxyIframe'>          <!--发送请求-->
    <input type='text' name='callback' value='callback'/>                                                   <!--回调参数-->
    <input type='text' name='proxy' value='http://localhost:8080/proxy.html'/>                   <!--代理地址-->
    <input type='submit' value='提交'/>
</form>
<!--X域中代理页面B,proxy.html，进入页面后对参数进行分析并在顶层框架中执行-->
<script>
    window.onload = function () {           //页面加载后执行
        if (top == self) {                              //如果不在A页面中，则返回
            return;
        }
        var arr = location.search.substr(1).split('&'),        //获取并解析search中的数据 ?callback=callback&arg=success
                fn, args;                                                      //预定义函数名称及参数集
        for (var i = 0, len = arr.length, item; i < len; i++) {
            item = arr[i].split('=');                       //解析search中的每组数据
            if (item[0] == 'callback') {                //判断是否为回调函数 callback=callback
                fn = item[1];                               //设置回调函数
            } else if (item[0] == 'arg') {             //判断是否是参数 arg=success
                args = item[1];                          //设置参数集
            }
        }
        try {
            eval('top.' + fn + '("' + args + '")');         //执行A页面中预设的回调函数
        } catch (e) {
            console.log('eval出错...');
        }
    }
</script>
```
```php
//Y域中被请求的接口文件C(服务器端)
<?php
     $proxy = $_POST["proxy"];                  //http://localhost:8080/proxy.html
     $callback = $_POST['callback'];          //callback
     header("Location: ".$proxy."?callback=".$callback."&arg=success");    
             //重定向到http://localhost:8080/proxy.html?callback=callback&arg=sucess
?>
```
 
# 其他应用场景
        页面加载初期加载文件(特别是图像)较多，可以延迟加载一些图片。页面图片较多时，
        通常是当用户点击某张图片时再加载。图片资源文件很大，则代理加载一张预览图，
        然后再用原图片替换的虚拟代理方式。

# 优点
    可以有效解决系统之间的耦合性以及系统资源开销大的问题，通过代理对象可以保护被代理对象。

# 缺点
    可能会构建一个较为复杂的代理对象，增加系统复杂度，同时也会增加一定的开销，
    当然，这种开销往往是可接受的。