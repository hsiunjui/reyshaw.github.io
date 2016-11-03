---
title: 简单模板模式
date: 2016-11-03 16:17:59
tags: 技巧型模式
categories: 设计模式
---
>通过格式化字符串拼凑出视图避免创建视图时大量节点操作。优化内存开销。

<!--more-->
# 模板生成器
```html
<div id="container"></div>
<script>
    var A = A || {};                    //命名空间，单体对象
    A.root = document.getElementById('container');
    A.strategy = {
        'listPart': function (data) {
            var s = document.createElement('div');  //创建模块容器
            var ul = '';                                    //列表字符串
            var ldata = data.data.li;               //列表数据
            var tpl = A.view(['h2', 'p', 'ul']),    // '<h2>{#h2#}</h2><p>{#p#}</p><ul>{#ul#}</ul>'
            liTpl = A.formatString(A.view('li'), {li: A.view(['strong', 'span'])}); // '<li><strong></strong>{#strong#}<span>{#span#}</span></li>'
            data.id && (s.id = data.id);                //若数据有id则设置容器id
            for (var i = 0, len = ldata.length; i < len; i++) {
                if (ldata[i].strong || ldata[i].span) {             //有strong属性或span属性
                    ul += A.formatString(liTpl, ldata[i]);       
                }
            }
            data.data.ul = ul;
            s.innerHTML = A.formatString(tpl, data.data);       //用data.data渲染tpl
            A.root.appendChild(s);
        }
        //........
    }
    A.init = function (data) {
        this.strategy[data.type](data);                         //调用相应策略并传入参数
    }
    //模板渲染方法，核心
    A.formatString = function (str, data) {
        return str.replace(/\{#(\w+)#\}/g, function (match, key) {
            return typeof data[key] === 'undefined' ? '' : data[key];     //有值填值,无值填空
        })
    }
    A.view = function (name) {
        var v = {                   //固定模板
            code: '<pre><code>{#code#}</code></pre>',
            img: '<img src="{#src#}" alt="{#alt#}" title="{#title#}" />',
            part: '<div id="{#id#}" class="{#class#}">{#part#}</div>',
            theme: [
                '<div>',
                '<h1>{#title#}</h1>',
                '{#content#}',
                '</div>'
            ].join('')
        }
        if (Object.prototype.toString.call(name) == '[object Array]') {     //参数为数组
            var tpl = '';
            for (var i = 0, len = name.length; i < len; i++) {
                tpl += arguments.callee(name[i]);           //重新调用
            }
            return tpl;
        } else {
            return v[name] ? v[name] : ('<' + name + '>{#' + name + '#}</' + name + '>');   //有则返回，无则创建
        }
    }
    //模拟数据
    var data = {
        id: 'myid',
        type: 'listPart',
        data: {
            li: [
                {strong: 'strong1', span: 'span1'},
                {strong: 'strong2', span: 'span2'},
                {strong: 'strong3', span: 'span3'},
                {strong: 'strong4', span: 'span4'},
                {strong: 'strong5', span: 'span5'}
            ],
            h2: 'h2标题',
            p: 'p标签'
        }
    }
    A.init(data);
</script>
```

# 原理
        用数据格式化字符串来渲染视图并插入容器,这样渲染模板引擎方法便成了核心。 

# 应用场景
        简单模板模式主要解决DOM操作资源消耗大，性能低下，操作复杂等问题，
        常备用于大型框架(如MVC)创建视图操作。

# 主要组成部分
        字符串模板库(随需求变化)，格式化方法(通常不变)，字符串拼接操作(随视图变化)。
