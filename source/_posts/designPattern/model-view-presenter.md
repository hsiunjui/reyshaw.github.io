---
title: MVP模式
date: 2016-11-03 16:52:41
tags: 架构型模式
categories: 设计模式
---
>即模型-视图-管理器,View层不直接引用Model层内的数据，而是通过Presenter层实现对Model层内数据的访问。即所有层次的交互都发生在presenter层中。

<!--more-->
# MVP简单结构
```javascript
~(function(window){
    var MVP = MVP || {};
    MVP.model = (function(){            //数据层保持不变
        var M = {};
        M.data = {};
        M.conf = {};
        return {
            getData:function(m){
                return M.data[m];
            },
            getConf:function(m){
                return M.conf[m];
            },
            setData:function(m,v){
                M.data[m] = v;
                return this;
            },
            setConf:function(c,v){
                M.conf[c] =v;
                return this;
            }
        }
    })()
    MVP.view = (function(){                 //视图层只解析模板，不再持有数据层的引用
        return function(str){
            //解析模板
            var html = '';
            return html
        }
    })();
    MVP.presenter = (function(){        //管理层负责管理数据、UI视图创建、交互逻辑、动画特效等一切事务
        var V= MVP.view;
        var M = MVP.model;
        var C = {};
        return {
            init:function(){
                for(var i in C){                //遍历内部管理器并执行内部逻辑
                    C[i]&& C[i](M,V,i);
                }
            }
        }
    })();
    MVP.init =function(){               //MVP入口
        this.presenter.init();
    };
    window.MVP = MVP;           //暴露对象
})(window)
```
 
# MVP的简单示例
```javascript
~(function(window){                     // 依赖jquery
    var MVP = MVP || {};
    MVP.model = (function(){            //数据层保持不变
        var M = {};
        M.data = {
            nav:[
                {
                    text:'新闻头条1',
                    mode:'news',
                    url:'http://www.example.com/01'
                },
                {
                    text:'新闻头条2',
                    mode:'news',
                    url:'http://www.example.com/01'
                },
                {
                    text:'新闻头条3',
                    mode:'news',
                    url:'http://www.example.com/01'
                },
                {
                    text:'新闻头条4',
                    mode:'news',
                    url:'http://www.example.com/01'
                }
            ]
        };
        M.conf = {};
        return {
            getData:function(m){
                return M.data[m];
            },
            getConf:function(m){
                return M.conf[m];
            },
            setData:function(m,v){
                M.data[m] = v;
                return this;
            },
            setConf:function(c,v){
                M.conf[c] =v;
                return this;
            }
        }
    })()
    MVP.view = (function(){             //视图层只解析模板，不再持有数据层的引用
        /*var REPLACEKEY = '__REPLACEKEY__';      //子元素或兄弟元素替换模板
        function getHTML(str,type){       //对一个元素模板的渲染
            return str                      //简化实现，只处理字符串中第一个{}里面的内容
                .replace(/^(\w+)([^\{\}]*)?(\{([@\w]+)\})? (.*?)$/,function(match,$1,$2,$3,$4,$5){
                    $2 = $2 ||'';           //元素属性参数容错处理
                    $3 = $3 ||'';           //{元素内容}参数容错处理
                    $4 = $4 ||'';           //元素内容参数容错处理
                    $5 = $5.replace(/\{([@\w]+)\}/g,'');    //去除元素内容后面添加的元素属性中的{}内容
                    //呈现形式
                    return type === 'in'?
                    '<'+$1+$2+$5+'>'+$4+REPLACEKEY+'</'+$1+'>':type === 'add'?
                    '<'+$1+$2+$5+'>'+$4+'</'+$1+'>'+REPLACEKEY:
                    '<'+$1+$2+$5+'>'+$4+'</'+$1+'>';
                })
                .replace(/#([@\-\w]+)/g,' id="$1"')         //处理特殊标识符#--id属性
                .replace(/\.([@\-\s\w]+)/g,' class="$1"')   //处理特殊标识符.--class属性
                .replace(/\[(.+)\]/g,function(match,key){   //处理其他属性组
                    var a =key.replace(/'|"/g,'').split(' '),
                        h = '';
                    for(var j = 0,len = a.length;j<len;j++){
                        h+=' '+a[j].replace(/=(.*)/g,'="$1"');
                    }
                    return h;
                })
                .replace(/@(\w+)/g,'{#$1#}');       //处理可替换内容
        }
        function eachArray(arr,fn){             //数组迭代器
            for(var i = 0,len = arr.length;i<len;i++){
                fn(i,arr[i],len);
            }
        }
        function formatItem(str,rep){           //渲染方法
            return str.replace(new RegExp(REPLACEKEY,'g'),rep);
        }
        //解析模板，本例旨在解析类似以下模板
        // 'li.@mode @choose @last[data-mode=@mode]>a#nav_@mode.nav-@mode[href=@url title=@text]...'的模板
        return function(str){
            var part = str
                .replace(/^\s+|\s+$/g,'')       //去除首尾空白符
                .replace(/^\s+(>)\s+/g,'$1')    //去除>两端空白
                .split('>'),                     //以>分割
                html = REPLACEKEY,              //模块视图根模板
                item,                           //同层元素
                nodeTpl;                        //同级元素模板
            eachArray(part,function(partIndex,partValue,partLen){       //遍历每组元素
                item = partValue.split('+');            //为同级元素分组
                nodeTpl = REPLACEKEY;                   //设置同级元素初始模板
                eachArray(item,function(itemIndex,itemValue,itemLen){   //遍历每一个同级元素
                    //用渲染元素得到的模板去渲染同级元素模板，此处简化逻辑处理，
                    // 如果itemIndex(统计元素索引)对应元素不是最后一个，则为兄弟元素处理
                    //否则，如果partIndex(层级索引)对应的层级不是最后一层则作为父层级处理(该层级有子层级，即该元素是父元素)
                    // ，否则无兄弟及子元素
                    nodeTpl =formatItem(nodeTpl,getHTML(itemValue,itemIndex===itemLen-1?(partIndex===partLen-1?'':'in'):'add'));
                })
                html = formatItem(html,nodeTpl);   //用渲染子层级得到的模板去渲染父层级模板
            })
            return html;        //返回期望视图模板
        }*/
        var tpl = [
            '<li class="{#mode#} {#choose#} {#last#}" data-mode="{#mode#}">',
            '<a id="nav_{#mode#}" class="nav-{#mode#}" href="{#url#}" title="{#text#}">',
            '<i class="nav-icon-{#mode#}"></i>',
            '<span>{#text#}</span>',
            '</a>',
            '</li>'
        ].join('');
        return function(){
            return tpl;             //view的任务是返回类似tpl的模板
        }
    })();
    MVP.presenter = (function(){        //管理层负责管理数据、UI视图创建、交互逻辑、动画特效等一切事务
        var V= MVP.view;
        var M = MVP.model;
        var C = {
            nav:function(M,V){
                var data = M.getData('nav');          //处理导航渲染数据
                data[0].choose = 'choose';            //第一条
                data[data.length-1].last = 'last';    //最后一条
               // var tpl = V('li.@mode @choose @last[data-mode=@mode]>a#nav_@mode.nav-@mode' +
                 //   '[href=@url title=@text]>i.nav-icon-@mode+span{@text}');
                //console.log(tpl);   //正则匹配未正确
                var tpl = V();
                var result = '';
                for(var i = 0;i<data.length;i++){
                    result+=formatString(tpl,data[i])
                }
                $('<ul></ul>').attr({'class':'navigation','id':'nav'}).html(result).appendTo($('body'));
                //交互逻辑即动画逻辑
            },
            //添加其他模块控制
        };
        return {
            init:function(){
                for(var i in C){        //遍历内部管理器并执行内部逻辑
                    C[i]&& C[i](M,V,i);
                }
            }
        }
    })();
    MVP.init =function(){               //MVP入口
        this.presenter.init();
    };
    window.MVP = MVP;           //暴露对象
})(window)
function formatString(str,data){
    return str.replace(/\{#(\w+)#\}/g,function(match,key){
        return typeof data[key]==='undefined'?'':data[key];
    })
}
window.onload =function(){
    MVP.init();             // 页面加载执行
}
```

# 功能与应用
        MVP将视图层与数据层完全解耦。
        可将其作为一个管理器灵活地调用多个数据创建多个视图，且不受限制。
        但管理器内过多的逻辑也会增加开发和维护的成本。