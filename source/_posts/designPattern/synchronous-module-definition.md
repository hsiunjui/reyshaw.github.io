---
title: 同步模块模式
date: 2016-11-03 16:35:23
tags: 架构型模式
categories: 设计模式
---
>请求发出后，无论模块是否存在，立即执行后续的逻辑，实现模块开发中对模块的立即引用。

<!--more-->
# 模块化
- 将复杂的系统分解成高内聚、低耦合的模块，使系统开发变得可控、可维护、可拓展，提高模块复用率。

# 模块管理器对象
```javascript
var F = F || {};                        //定义模块管理器单体对象
//定义模块方法（模块路由,模块方法） --通常模块方法应该放在闭包中实现，这里简化了
F.define = function (str, fn) {
    var parts = str.split('.'),             //解析模块路由
            old = parent = this,          //old表示当前模块的祖父模块，parent表示当前模块的父模块,初始均为F
            i = len = 0;                       //i模块层级，len模块层级长度
    if (parts[0] === 'F') {                   //如果第一个模块是模块管理器单体对象，则移除
        parts = parts.slice(1);
    }
    if (parts[0] === 'define' || parts[0] === 'moudle') {    //屏蔽对define与module模块方法的重写
        return;
    }
    for (len = parts.length; i < len; i++) {                  //遍历路由模块并定义每层模块
        if (typeof parent[parts[i]] === 'undefined') {            //如果父模块中不存在当前模块，则声明当前模块
            parent[parts[i]] = {};
        }
        old = parent;                       //缓存下一层级的祖父模块 从this(F)->old->parent一层层接下去
        parent = parent[parts[i]];     //缓存下一层级父模块
    }
    if (fn) {                     //如果给定模块方法则定义该模块方法
        old[parts[--i]] = fn();
    }
    return this;               //返回模块管理器单体对象
}
```
 
# 模块创建于测试
```javascript
//定义模块--F.string模块
    F.define('string', function () {
        return {
            trim: function (str) {
                return str.replace(/^\s+|\s+$/g, '');
            }
        }
    })
    //测试--通常开发中不允许这样直接调用，因为模块通常保存在闭包内部的私有变量里，而不会保存在F上，因此是获取不到的。
    //开发中是不符合规范的，这里只是方便测试
        var test = '      测试   ';
     console.log('[' + test + ']');                  //[      测试   ]
     console.log('[' + F.string.trim(test) + ']');   //[测试]
    //<div id="test"></div>下测试
    F.define('dom', function (id) {
        var $ = function (id) {
            $.dom = document.getElementById(id);
            return $;
        }
        $.html = function (html) {
            if (html) {
                this.dom.innerHTML = html;
                return this;
            } else {
                return this.dom.innerHTML;
            }
        }
        return $(id);
    });
    console.log(F.dom('test').html());              //test
    //先声明，后创建,这种方式是把模块添加到F对象上了，模块化开发中几乎不用
    F.define('dom.addClass');
    F.dom.addClass = (function (type, fn) {
        return function (className) {
            if (!~this.dom.className.indexOf(className)) {
                this.dom.className = ' ' + className;
            }
        }
    })();
    F.dom('test').addClass('test');     //<div id="test" class=" test">test</div>
```
 
# 模块调用
```javascript
F.module = function () {
    var args = [].slice.call(arguments),                                                  //获取参数转化为数组
            fn = args.pop(),                                                                        //获取回调执行函数
            parts = args[0] && args[0] instanceof Array ? args[0] : args,   //获取依赖模块
            modules = [],                                                                            //依赖模块列表
            modIDs = '',                                                                             //模块路由
            i = 0,                                                                                        //依赖模块索引
            ilen = parts.length,                                                                   //依赖模块长度
            parent, j, jlen;                                                                          //父模块，模块路由层级索引，模块路由层级长度
    while (i < ilen) {                      //遍历依赖模块
        if (typeof parts[i] === 'string') {           //如果是模块路由
            parent = this;                                 //设置当前模块父对象(F)
            modIDs = parts[i].replace(/^F\./, '').split('.');           //解析模块路由，并屏蔽掉模块父对象,其实就是模块名(xx.xx)解成数组
            for (j = 0, jlen = modIDs.length; j < jlen; j++) {       //遍历模块路由层级
                parent = parent[modIDs[j]] || false;                    //重置父模块,从F向下一层层递进
            }
            modules.push(parent);       //将模块添加到依赖模块列表中
        } else {
            modules.push(parts[i]);     //直接加入依赖模块列表中
        }
        i++;                                       //取下一个依赖模块
    }
    fn.apply(null, modules);             //执行回调函数
}
//<div id='test'></div> 环境下测试
F.module(['dom', document], function (dom, doc) {       //其实现原理是首先遍历并获取所有的模块依赖，并一次性保存在 
    dom('test').html('new add');                                        //依赖模块列表中，然后将这些依赖模块作为参数传入执行函数中执行
    doc.body.style.background = 'red';                    //执行结果 文本被改为new add，body背景变为了红色
})         //注意：在调用模块时，这些模块必然是创建好的，对于未加载文件中的模块需要异步处理
```

# 特点 
        同步模块模式是模块化开发的一种最简单形式，这种模式使得依赖的模块无论加载，
        无论有无，模块创建即执行，这就要求依赖的模块必然是创建过的。
        模块化开发是一种对系统的分解，其使用更像是组合模式的模块组合，具有极大地灵活性。

# 缺点
        实现模块化开发需要建立大量的闭包，会占用大量的系统的资源。
        同时随着模块数量的增加(好处是开发成本减少)，但接口数量却随之增加，
        接口的使用成本和开发维护的成本也随之增加，所以要合理模块分割。 
 
