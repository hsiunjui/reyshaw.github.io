---
title: 异步模块模式
date: 2016-11-03 16:40:25
tags: 架构型模式
categories: 设计模式
---
> 请求发出后，继续其他业务逻辑，知道模块加载完成后执行后续的逻辑，实现模块开发中对模块加载完成后的引用。

<!--more-->
# 异步模块管理器
```javascript
//创建模块管理器F
    ~(function (F) {                      //向闭包中传入模块管理对象F(~屏幕压缩文件时，前面漏写分号报错)
        var moduleCache = {},            //模块缓存器
                setModule = function (moduleName, params, callback) {       //调用模块(模块名称，依赖模块，回调函数)
                    var _module, fn;                                                                        //模块容器，模块文件加载完成后的回调函数
                    if (moduleCache[moduleName]) {                                                      //如果模块被调用过
                        _module = moduleCache[moduleName];                                                 //获取模块
                        _module.status = 'loaded';                                                                        //设置状态已经加载完成
                        _module.exports = callback ? callback.apply(_module, params) : null;    //校正接口
                        while (fn = _module.onload.shift()) {                                                         //执行模块加载完成回调函数
                            fn(_module.exports);
                        }
                    } else {                            //模块不存在(匿名模块),直接执行构造函数
                        callback && callback.apply(null, params);
                    }
                },
                loadModule = function (moduleName, callback) {       //模块名称，回调函数
                    var _module;                                                              //缓存依赖模块
                    if (moduleCache[moduleName]) {                             //如果模块被要去加载过
                        _module = moduleCache[moduleName];                  //获取模块
                        if (_module.status === 'loaded') {                                      //如果模块加载完成
                            setTimeout(callback(_module.exports), 0);                        //执行模块加载完成回调函数
                        } else {                                                                               //如果模块尚未加载完成
                            _module.onload.push(callback);                                        //缓存该模块所处文件加载完成回调函数
                        }
                    } else {                                                                  //如果是第一次被依赖引用
                        moduleCache[moduleName] = {                             //缓存该模块初始化信息
                            moduleName: moduleName,                                 //模块名称
                            status: 'loading',                                                     //模块默认加载状态
                            exports: null,                                                          //模块接口
                            onload: [callback]                                                  //模块对应文件加载完成回调函数缓冲器
                        }
                        loadScript(getUrl(moduleName));                     //加载对应文件
                    }
                },
                getUrl = function (moduleName) {                    //获取文件路径
                    return String(moduleName).replace(/\.js$/g, '') + '.js';                //添加js后缀
                },
                loadScript = function (src) {                               //加载脚本文件，创建标签添加到页面
                    var _script = document.createElement('script');
                    _script.type = 'text/javascript';
                    _script.src = src;
                    _script.async = true;
                    _script.charset = 'UTF-8';
                    document.getElementsByTagName('head')[0].appendChild(_script);
                };
        //创建与调度模块（集成两个功能于一体）
        F.module = function (url, modDeps, modCallback) {   //模块url,依赖模块,模块主函数
            var args = [].slice.call(arguments),                                //参数转为数组
                    callback = args.pop(),                                            //获取模块构造函数
                            //获取依赖模块,紧邻回调函数，且数据类型为数组
                    deps = (args.length && args[args.length - 1] instanceof Array) ? args.pop() : [],
                    url = args.length ? args.pop() : null,                      //当前模块url(模块ID)
                    params = [],                                                           //依赖模块序列
                    depsCount = 0,                                                      //未加载的依赖模块数量统计
                    i = 0,                                                                      //依赖模块序列中的索引值
                    len;                                                                        //依赖模块序列长度
            if (len = deps.length) {        //如果有依赖模块
                while (i < len) {                    //遍历依赖模块
                    (function (i) {                         //闭包保存i
                        depsCount++;                        //增加未加载依赖模块数量统计
                        loadModule(deps[i], function (mod) {   //异步加载依赖模块
                            params[i] = mod;                                    //依赖模块序列中添加依赖模块接口引用
                            depsCount--;                                          //依赖模块加载完成，依赖模块数量统计减一
                            if (depsCount === 0) {                    //如果依赖模块全部加载
                                setModule(url, params, callback);         //在模块缓存器中矫正该模块，并执行构造函数
                            }
                        })
                    })(i);
                    i++;                        //遍历下一个依赖模块
                }
            } else {                          //无依赖模块，直接执行回调函数
                setModule(url, [], callback);
            }
        }
    })(function () {
        return window.F = {};               //创建模块管理器对象F,并保存在全局作用域中
    }());
```
 
# 测试模块化
```html
<div id="demo">test</div>
<script>
    //定义lib/dom.js文件模块,该模块不需要依赖任何模块
    F.module('lib/dom', function () {
        return {
            g: function (id) {
                return document.getElementById(id);
            },
            html: function (id, html) {
                if (html) {
                    this.g(id).innerHTML = html;
                } else {
                    return this.g(id).innerHTML;
                }
            }
        }
    })
    //定义lib/event.js文件模块，依赖于lib/dom模块
    F.module('lib/event', ['lib/dom'], function (dom) {
        var events = {
            on: function (id, type, fn) {
                dom.g(id)['on' + type] = fn;
            }
        }
        return events;
    })
    // 以上两个模块均在单独的文件中定义
    
    //交互-- 模块调用
     F.module(['lib/dom'], function (dom) {
        console.log(dom.g('demo'));
    });                                         //执行结果输出<div id='test'></div>
    // 调用依赖模块
    F.module(['lib/event', 'lib/dom'], function (events, dom) {
        events.on('demo', 'click', function () {
            console.log(dom.html('demo'));          
        })
    })//  结果： 点击后控制台打印test
</script>
```

# 执行流程(以F.module(['lib/dom'],function())为例)
         首先F.module(['lib/dom'])会调用loadModule的else分支(即第一次被依赖引用)，生成moduleCache['lib/dom']对象，
         并在该对象的onload回调数组中push入loadModule的回调函数function(mod)。
         至此，当前页面继续执行后面的逻辑即F.module(['lib/event','lib/dom'])...
         当执行到</script>时,即页面结束时会异步加载lib/dom.js文件执行lib/dom.js文件里的逻辑。
         由于在dom.js中定义了F.module('lib/dom',function()),因此又会进入F.module函数，
         但本次执行回直接跑到F.module函数的else分支(因为它没有依赖任何模块),进入setModule函数,
         由于模块已经存在(moduleCache中的lib/dom对象存在了)，_module.exports会在_module环境中执行一遍，
         即拿到dom.js文件中定义模块的返回值(即return回来的对象)，
         再把该对象作为参数传入该模块onload压入的会调函数中遍历执行(即表示模块的param索引,重新设定未加载模块数),
         但onload中的所有回调都执行完了以后，未加载模块数为0,
         最后再次进入setModule函数中，对当前页面的模块做处理(由于当前模块url为空，直接执行了最终的回调函数)。

# loadModule和setModule

- loadModule的目的是加载依赖模块对应的文件并执行回调函数。它做了三件事情：
              第一：如果文件已经被要求加载过，则需要判断文件是否加载完成，若完成则异步执行模块加载完成回调函数
              第二：如果文件未加载完成，则将加载完成回调函数缓存入模块加载完成回调函数容器中
              第三：如果文件未被要求加载过，则加载该文件，初始化依赖模块信息写入缓存器中
- setModule的目的是执行模块回调函数，它也做了三件事情：
             第一：对创建的模块来说,但所有的依赖模块加载完成时，使用该方法(即定义的模块)
             第二：对于被依赖的模块，其所在的文件加载后要执行该依赖模块(即创建该模块)会间接调用(即依赖模块)
             第三：对于一个匿名模块(无url参数)执行过程也会使用该方法

# 应用场景
        异步模块加载在模块化的基础上增加了模块依赖，是开发不用担心某些方法尚未加载或未加载完全造成无法使用的问题。
        同时，异步加载部分功能也可剥去首屏不必要的功能，减少首屏加载时间。