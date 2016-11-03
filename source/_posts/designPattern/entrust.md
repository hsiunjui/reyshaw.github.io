---
title: 委托模式
date: 2016-11-03 16:00:49
tags: 技巧型模式
categories: 设计模式
---
>多个对象接收并处理同一请求，他们将请求委托给另外一个对象统一处理请求。

<!--more-->
# 事件的重复绑定
```javascript
    var ul = document.getElementsByTagName('ul')[0],
            li = document.getElementsByTagName('li');
    i = li.length - 1;
    for (; i >= 0; i--) {                                        //遍历元素
        li[i].onclick = function () {
            this.style.backgroundColor = 'gray';     //这里的this不绑定bind的话指向了window
        }.bind(li[i]);
    }                                                //缺陷：内存消耗巨大
```
 
# 委托于父元素
- 事件委托是将子元素的事件委托给父元素，然后通过事件冒泡传递，再通过判断事件源的某一特性来执行某一业务逻辑。
```javascript
   var ul = document.getElementsByTagName('ul')[0];
    ul.onclick = function (e) {
        var e = e || window.e,                          //事件对象
                tar = e.target || e.srcElement;     //目标对象
        if (tar.nodeName.toLowerCase() == 'li') {           //判断标签名称
            tar.style.backgroundColor = 'gray';
        }
    }                                       //其原理是通过事件冒泡实现的
    var li = document.createElement('li');          //可以实现对后来添加元素事件的绑定
    li.innerHTML = 'test4';
    ul.appendChild(li);
```
        
# 防止内存泄漏
```html
<div id="test">
    <div id="demo">demo</div>
</div>
<script>
    var g = function (id) {
        return document.getElementById(id);
    }
    /*
         g('demo').onclick = function(){         //点击会删除整个元素，事件绑定在子元素上
            g('demo').onclick = null;                   //如果有事件，则先清除事件，但有的浏览器不兼容该种写法
            g('test').innerHTML = 'test';
         }
     */
    //使用委托模式
    g('test').onclick = function (e) {             //事件绑定在父元素上
        var e = e || e.window;
        var tar = e && e.target || window.event.srcElement;
        if (tar.id === 'demo') {                            //判断其id
            g('demo').innerHTML = 'test';
        }
    }
</script>
```

# 数据分发
```javascript
$.get('./deal.php?q=banner',function(res){
    //处理banner模块逻辑
});
$.get('./deal.php?q=aside',function(res){
    //处理aside模块逻辑
})
/**缺陷：需要发两次请求(如有其它请求数据，会造成更多次请求，浪费资源)**/
//委托对象
var Deal = {
    banner:function(){
        //处理banner模块逻辑
    },
    aside:function() {
        //处理aside模块逻辑
    }
}
$.get('./deal.php?',function(res){
    for(var i in res){          //数据拆包分发
        Deal[i] && Deal[i](res[i]);
    }
});                    //节省了流量和时间开销
```

# 应用场景：
        事件委托(包括对后来添加到页面的元素添加事件);
        处理内存泄漏问题以及用于请求与委托者之间的解耦。 