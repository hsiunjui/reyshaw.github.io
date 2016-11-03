---
title: 备忘录模式
date: 2016-11-03 15:46:50
tags: 行为型模式
categories: 设计模式
---
>在不破坏对象的封装性的前提下，在对象之外捕获并保存该对象内部的状态以便日后对象使用或者对象恢复到以前的某个状态。

<!--more-->
# 对象缓存器
```javascript
var Page = (function(){
    var cache = {};                         //信息缓存对象
    return function(page,fn){
        if(cache[page]){                //如果该页数据在缓存中
            console.log('from cache');
            showResult(page,cache[page]);         //恢复该页状态，显示该页内容
        }else{
            console.log('from server');
            var data = ajaxPost();                             //服务端获取数据
            showResult(page,data.newContent);     //显示内容
            cache[page] = data.newContent;           //缓存数据
        }
        fn && fn();                             //执行回调
    }
})();
function showResult(page,content){  //模拟内容展示
    console.log(page,content);
}
function ajaxPost(){                           //模拟ajax发送数据
    return  {
        newContent:'content from server'
    };
}
Page(1,function(){
    console.log('ok');
});                               //from server    1 'content from server'    ok
Page(1);                    //from cache     1 'content from server'
```

# 主要功能
        对现有的数据或状态做缓存，为将来某个时候使用或恢复做准备。
        同时，备忘录对象对数据的封装，方便管理数据。

# 缺点
        当数据量过大时，会严重占用系统提供的资源，会极大地降低系统的性能，因此要有选择性地缓存。 
