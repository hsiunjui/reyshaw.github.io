---
title: MVC模式
date: 2016-11-03 16:49:37
tags: 架构型模式
categories: 设计模式
---
>用一种将业务逻辑、数据、视图分离的方式组织架构代码。

<!--more-->
# 简单MVC示意
```javascript
 (function(){
    var MVC = MVC || {};
    MVC.model = (function(){
        var M = {};                     //内部数据对象
        M.data  = {};                  //服务端获取的数据
        M.conf = {};                   //配置数据，页面加载时提供
        return {                          //返回数据模型层对象操作方法
            getData:function(m){        //根据字段获取服务端数据
                return M.data[m];
            },
            getConf:function(c){         //根据字段获取配置数据
                return M.conf[c];
            },
            setData:function(m,v){      //设置服务端数据
                M.data[m] = v;
                return this;
            },
            setConf:function(c,v){       //设置配置数据
                M.conf[c] =v;
                return this;
            }
        }                                           //之所以都是自执行的形式，是为了对外提供访问接口
    })();
    MVC.view = (function(){
        var M = MVC.model;          //模型数据层对象操作方法引用,--视图层可以调用数据层创建视图
        var V = {};                           //内部视图创建方法对象
        return function(v){               //根据视图名返回视图
            V[v]();
        }
    })();
    MVC.ctrl = (function(){
        var M = MVC.model;          //模型数据层对象操作方法引用，--控制层可以调用数据层数据
        var V = MVC.veiw;            //模型视图数据层对象操作方法引用，--控制层可以控制视图创建
        var C = {};                         //控制器创建方法对象
    })();
})()
```
 
# 简单实例
```javascript
// 需要引入jquery，图片的元素尚无法加载
(function(){
        var MVC = MVC || {};
        MVC.model = (function(){
            var M = {};                         //内部数据对象
            M.data  = {
                sideBar:[
                    {
                        text:'萌妹子1',
                        icon:'left_meng.png',
                        title:'喵耳萝莉的千本樱',
                        content:'自古幼女有三好~',
                        img:'left_meng_img.png',
                        href:'http://moe.hao123.com'
                    },
                    {
                        text:'萌妹子2',
                        icon:'left_meng.png',
                        title:'喵耳萝莉的千本樱',
                        content:'自古幼女有三好~',
                        img:'left_meng_img.png',
                        href:'http://moe.hao123.com'
                    },
                    {
                        text:'萌妹子3',
                        icon:'left_meng.png',
                        title:'喵耳萝莉的千本樱',
                        content:'自古幼女有三好~',
                        img:'left_meng_img.png',
                        href:'http://moe.hao123.com'
                    },
                    {
                        text:'萌妹子4',
                        icon:'left_meng.png',
                        title:'喵耳萝莉的千本樱',
                        content:'自古幼女有三好~',
                        img:'left_meng_img.png',
                        href:'http://moe.hao123.com'
                    },
                    {
                        text:'萌妹子5',
                        icon:'left_meng.png',
                        title:'喵耳萝莉的千本樱',
                        content:'自古幼女有三好~',
                        img:'left_meng_img.png',
                        href:'http://moe.hao123.com'
                    }
                ],
                newsMode:[]                     // 这里可以新添加模块
            };                          //服务端获取的数据
            M.conf = {
                slideBarCloseAnimate:false      //侧边动画配置数据
            };                        //配置数据，页面加载时提供
            return {                    //返回数据模型层对象操作方法
                getData:function(m){    //根据字段获取服务端数据
                    return M.data[m];
                },
                getConf:function(c){    //根据字段获取配置数据
                    return M.conf[c];
                },
                setData:function(m,v){  //设置服务端数据
                    M.data[m] = v;
                    return this;
                },
                setConf:function(c,v){  //设置配置数据
                    M.conf[c] =v;
                    return this;
                }
            }                           //之所以都是自执行的形式，是为了对外提供访问接口
        })();
        MVC.view = (function(){
            var M = MVC.model;          //模型数据层对象操作方法引用,--视图层可以调用数据层创建视图
            var V = {
                createSlideBar:function(){              //创建侧边栏
                    var html = '',
                            data = M.getData('sideBar');    //视图渲染数据
                    if(!data || !data.length){                      //屏蔽无效数据
                        return;
                    }
                    var dom = $('<div></div>').attr({'class':'sidebar','id':'sidebar'});    //创建视图容器
                    var tpl = {
                        container:[         //视图容器模板
                                '<div calss="sidebar-inner"><ul>{#content#}</ul></div>',
                                '<a hidefocus href="#" class="sidebar-close" title="收起"></a>'
                        ].join(''),
                        item:[              //导航图标模块模板
                                '<li>',
                                    '<a class="icon" href="{#href#}">',
                                        '<img src="common/img/{#icon#}">',
                                        '<span>{#text#}</span>',
                                    '</a>',
                                    '<div class="box">',
                                        '<div>',
                                            '<a class="title" href="{#href#}">{#title#}</a>',
                                            '<a href="{#href#}">{#content#}</a>',
                                        '</div>',
                                        '<a class="image" href="{#href#}"><img src="common/img/{#img#}"/></a>',
                                    '</div>',
                                '</li>'
                        ].join('')
                    }
                    //渲染全部导航图片模块
                    for(var i = 0, len = data.length;i<len;i++){
                        html += formatString(tpl.item,data[i]);
                    }
                    //渲染导航视图并添加到页面
                    dom.html(formatString(tpl.container,{content:html})).appendTo($('body'));
                },
                createNewsMod:function(){}      //  新添加模块追加代码
                
            };                 //内部视图创建方法对象
            return function(v){         //根据视图名返回视图
                V[v]();
            }
        })();
        MVC.ctrl = (function(){
            var M = MVC.model;            //模型数据层对象操作方法引用，--控制层可以调用数据层数据
            var V = MVC.view;               //模型视图数据层对象操作方法引用，--控制层可以控制视图创建
            var C = {
                initSideBar:function(){         // 控制器主要创建视图并添加事件, 而创建视图的具体实现也交给了视图层
                    V('createSlideBar');        //渲染导航栏模块视图
                    $('li','.sidebar').on('mouseover',function(e){
                        $(this).addClass('show');
                    }).on('mouseout',function(e){
                        $(this).removeClass('show');
                    })
                    $('.sidebar-close','.sidebar').on('click',function(e){
                        if(M.getConf('slideBarCloseAnimate')){
                            return;
                        }
                        M.setConf('slideBarCloseAnimate',true);
                        var $this = $(this);
                        if($this.hasClass('is-close')){             //如果箭头icon是关闭状态
                            $('.sidebar-inner','.sidebar').animate({
                                duration:800,
                                type:'easeOutQuart',
                                main:function(dom){
                                    dom.css('left',-50+this.tween*50+'px');
                                },
                                end:function(){
                                    $this.removeClass('is-close');
                                    M.setConf('slideBarCloseAnimate',false);
                                }
                            })
                        }else{                                      //如果箭头icon是打开状态
                            $('.sidebar-inner','.sidebar').animate({
                                duration:800,
                                type:'easeOutQuart',
                                main:function(dom){
                                    dom.css('left',this.tween*-50+'px');
                                },
                                end:function(){
                                    $this.addClass('is-close');
                                    M.setConf('slideBarCloseAnimate',true);
                                }
                            })
                        }
                    })
                },
                initNewsMod:function(){}    // 新增模块追加代码
            };                 //控制器创建方法对象
            C.initSideBar();
             /*for(var i in c){     // 遍历创建
                 C[i]&& C[i]();
             }*/
        })();
    })()
    function formatString(str,data){
        return str.replace(/\{#(\w+)#\}/g,function(match,key){
            return typeof data[key]==='undefined'?'':data[key];
        })
    }
```

# 优点及应用
        在大型页面系统中有利于工程化、模块化开发并管理代码，
        便于大型页面系统 的可持续开发与维护，同时也是降低层次耦合提升代码复用的良好实践。
        在复杂组件开发中，用MVC思想管理组件内部的层次也是很好的选择。 

