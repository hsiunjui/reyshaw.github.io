---
title: 享元模式
date: 2016-11-03 15:06:36
tags: 结构型模式
categories: 设计模式
---
>运用共享技术有效的支持大量的细粒度的对象，避免对象间拥有相同内容造成多余的开销。

<!--more-->
# 新闻列表分页
```javascript
var dom = null,                     //缓存创建的新闻标题元素
    paper = 0,                       //当前页数
    num = 5,                         //每页显示的新闻条数
    i = 0,                               //迭代变量
    len = article.length;        //新闻数据长度
for (; i < len; i++) {
    dom = document.createElement('div');        //创建标题容器
    dom.innerHTML = article[i];                         //填充标题内容
    if (i >= num) {                               //默认显示第一页，第一页之后的都将隐藏
        dom.style.display = 'none';
    }
    document.getElementById('container').appendChild(dom);        //添加到页面中
}
document.getElementById('next').onclick = function () {              //点击下一页
    var div = document.getElementById('container').getElementsByTagName('div');      //获取所有新闻标题元素
    var j = k = n = 0;                                               //j,k循环变量，n当前页显示的第一个新闻序号
    n = ++paper % Math.ceil(len / num) * num;     //获取当前页显示的第一个新闻序号 math.ceil()执行向上舍入
    for (; j < len; j++) {
        div[j].style.display = 'none';          //隐藏所有的新闻
    }
    for (; k < 5; k++) {
        if(div[n+k]){                          //最后一页可能不足5条
            div[n+k].style.display='block';         //显示当前页
        }
    }
}
/**
 *  缺点：有多少条数据就需要生成多少个div,大量的dom添加操作
 */
```

# 享元操作
```javascript
//享元方法
var Flyweight = (function () {
    var created = [];                                               //已经创建的元素集合
    function create() {
        var dom = document.createElement('div');                                //创建div
        document.getElementById('container').appendChild(dom);      //添加到容器中
        created.push(dom);                  //缓存创建元素
        return dom;                               //返回创建元素
    }
    return {
        getDiv: function () {                      //创建新闻列表的方法
            if (created.length < 5) {           //如果创建个数小于每一页需要显示的个数，则创建
                return create();
            } else {                                  //如果大于或等于一页显示的个数
                var div = created.shift();          //删除第一个并返回元素
                created.push(div);                  //把返回的元素插入到末尾
                return div;                              //返回删除的元素
            }
        }
    }
})();
//改造后
var paper = 0,                                      //当前页
    num = 5,                                          //每页显示条数
    len = article.length,                         //总条数
    isfalse = false;                                //判断时候到最后部分
for (var i = 0; i < 5; i++) {                    //添加5条新闻
    if (article[i]) {
        Flyweight.getDiv().innerHTML = article[i];  
    }
}
document.getElementById('next').onclick = function () {
    if (article.length < 5 || isfalse) {                   //如果数据不足5条直接返回
        return;
    }
    var n = ++paper * num % len,                  //当前页的第一条新闻索引
        j = 0;
    for (; j < 5; j++) {                                //插入5条新闻
        if (article[n + j] && !isfalse) {                   //如果存在第n+j条则插入
            Flyweight.getDiv().innerHTML = article[n + j];
            // } else if (article[n + j - len]) {          //否则插入起始位置第n+j-len条,主要用于链接最后一页和首页使用
            //    Flyweight.getDiv().innerHTML = article[n + j - len];
            //   isfalse = true;
        } else {                            //如果都不存在插入空字符串
            Flyweight.getDiv().innerHTML = '';
            isfalse = true;
        }
    }
}
/**
 *  该种方式只是对5个dom元素进行操作，大大提高了性能。
 */
```

# 享元模式的主要特征
        享元模式主要是对其数据、方法共享分离，它将数据和方法分为内部数据、内部方法和外部数据、外部方法。
        内部数据指的就是相似或者共有的数据和方法，把这部分提取出来减少开销，以提高性能。(即是对相同数据结构的提取)

# 应用场景
        享元模式的目的是提高程序的执行效率和系统的性能，因此在大型系统开发中应用比较广泛。
        另外在一些面向对象的编程中，抽离一些有效的内部状态也是常用的场景之一。 
 