---
title: MVVM模式
date: 2016-11-03 16:55:24
tags: 架构型模式
categories: 设计模式
---
> 即模型-视图-视图模型,为视图层(View)量身订做一套视图模型(ViewModel)，并在视图模型(ViewModel)中创建属性和方法，为视图层(View)绑定数据(Model)并实现交互。

<!--more-->
# MVVM模型
```html
<div class="first" data-bind="type:'progressbar',data:demo"></div>
<script>
    ~(function () {                       //屏蔽压缩报错
        var window = this || (0, eval)('this');               //在闭包中获取全局变量
        var VM = (function () {
            var Method = {          //创建组件对象，对视图的处理
                progressbar: function (dom, data) {                 //进度条容器,进度条数据模型
                    var progress = document.createElement('div'),           //进度条完成容器
                            param = data.data;                                              //数据模型数据,结构{position:50}
                    progress.style.width = (param.position || 100) + '%';   //设置进度完成容器尺寸
                    progress.style.background = 'red';
                    progress.style.height = '10px';
                    dom.className += ' ui-progressbar';                      //为进度条组件添加UI样式
                    dom.appendChild(progress);                                  //进度完成容器元素插入进度条容器
                }
                //slide：function(){}        //添加其他组件
            }
            function getBindData(dom) {                        //对数据的处理
                var data = dom.getAttribute('data-bind');                                   //获取绑定的数据
                return !!data && (new Function("return ({" + data + "})"))();       //转为对象
            }
            return function () {
                var doms = document.body.getElementsByTagName('*'),       //页面所有容器
                        ctx = null;             //转换后的数据
                for (var i = 0; i < doms.length; i++) {
                    ctx = getBindData(doms[i]);
                    ctx.type && Method[ctx.type] && Method[ctx.type](doms[i], ctx); //有该种类型，且方法中有该种方法，执行
                }
            }
        })()
        window.VM = VM;                    //向外暴露接口
    })();    
    var demo = {                                //测试数据(数据模型层)
        position: 60,
        totle: 200
    };
    window.onload = function () {
        VM();                   //出现一个60%长度的进度条
    }
</script>
```
 
# 原理
        视图模型层通过获取视图的特定属性值，然后把获得的属性值和真正的数据层数据绑定并转成对象再交给视图模型层创建视图。
        其实现过程是通过在页面直接书写HTML代码创建视图组件，让控制器或管理器去监听这些视图组件，并处理这些组件完成的预期功能。

# 功能与应用
        MVVM模式也是用来分离视图和数据模型的，不同的是视图的作用提升了且更加自由。
        这使得视图层和数据模型层都可以独立变化，自由创建，同时还可以实现代码逻辑的复用。 