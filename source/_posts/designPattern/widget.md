---
title: Widget模式
date: 2016-11-03 16:46:12
tags: 架构型模式
categories: 设计模式
---
>web widget指的是一块可以在任意页面中执行的代码块。Widget模式是指借用web widget思想将页面分解成部件，针对部件开发，最终组合成完整的页面。

<!--more-->
# 模板引擎模块
```javascript
//需要引用异步模块管理器F
F.module('lib/template', function () {
    var _TplEngine = function (str, data) {   //模板引擎，处理数据与编译模板接口(模板容器id或模板字符串，渲染数据)
            if (data instanceof Array) {                  //如果数据是数组
                var html = '',
                    i = 0,
                    len = data.length;
                for (; i < len; i++) {                               //缓存模板渲染结果
                    html += _getTpl(str)(data[i]);              //或者html+=arguments.callee(str,data[i])
                }
                //console.log(html);
                return html;                                //返回最终结果
            } else {
                return _getTpl(str)(data);      //非数组(一般是对象),直接返回渲染结果
            }
        },
        _getTpl = function (str) {             //获取模板
            var ele = document.getElementById(str);             //通过id获取元素
            if (ele) {
                var html = /^(textarea|input)$/i.test(ele.nodeName) ? ele.value : ele.innerHTML;    //值或内容
                return _compileTpl(html);        //返回编译后内容
            } else {
                return _compileTpl(str);           //编译字符串模板
            }
        },
        _dealTpl = function (str) {   //处理模板
            var _left = '{%',                    //左分隔符
                _right = '%}';                   //右分隔符
            //console.log('编译前：');
            //console.log(str);
            return String(str)
                .replace(/&lt;/g, '<')            //转义标签内的<,如在模板中a<b需写成a &lt; b
                .replace(/&gt;/g, '>')          //转义标签内的>
                .replace(/[\r\t\n]/g, '')         //过滤换行，制表符，回车符
                .replace(new RegExp(_left + '=(.*?)' + _right, 'g'), "',typeof($1) === 'undefined'?'':$1,'")    //替换内容
                .replace(new RegExp(_left,'g'),"');")                                      //替换左分隔符
                .replace(new RegExp(_right,'g'),"template_array.push('");   //替换右分隔符
        },
        _compileTpl = function (str) { //编译执行
           var fnBody = "var template_array = [];"+            //模板容器组
            "var fn = (function(data){"+                                //闭包，模板容器组添加成员
                "var template_key='';"+                                 //渲染数据变量的执行函数体
                "for(key in data){"+                                        //遍历渲染数据
                    "template_key+=('var '+key +'data[\"'+key+'\"];');"+        //为渲染数据变量的执行函数体添加复制语句
                "}"+
                "eval(template_key);"+                                  //执行渲染数据变量函数
                "template_array.push('"+_dealTpl(str)+"');"+    //为模板容器添加成员
                "template_key = null;"+                             //释放渲染数据变量函数
            "})(templateData);"+                                     //为闭包传入数据
            "fn = null;"+                                                  //释放闭包
            "return template_array.join('');";                  //返回渲染后的模板容器组，并拼接成字符串
            return new Function('templateDate',fnBody);             //返回函数
        }
    return _TplEngine;
})
```

# 页面模板编写即调用
```html
<div id="demo_tag" class="template">
    <div id="tag_cloud">
        {% for(var i =0,len = tagCloud.length;i<len;i++){
            var ctx = tagCloud[i];
        %}
        <a href="#" class="tag_item {% if(ctx['is_selected']){ %} selected {% } %}" title="{%= ctx['title'] %}">{%=ctx["text"]%}</a>
        {%
            }
        %}
    </div>
</div>
<div id="test">test</div>
<script>
    var data = {
        tagCloud:[
            {is_selected:true,title:'这是一本设计模式的书',text:'设计模式'},
            {is_selected:false,title:'这是一本HTML的书',text:'HTML'},
            {is_selected:null,title:'这是一本CSS的书',text:'CSS'},
            {is_selected:'',title:'这是一本JS的书',text:'JS'}
        ]
    }
    F.module(['lib/template'], function (template) {
        var str = template('demo_tag',data);
        document.getElementById('test').innerHTML=str;          
    });
</script>
```

# 组件的两大部分
        一个完整的组件应该包含该模块的完整的视图和一套完整的功能。
        组件需要做两件事,第一是创建视图，第二是添加相应的功能 .

# 实现原理
        第一：处理数据； 
        第二：获取模板；  
        第三：处理模板； 
        第四：编译执行；

# 优点
        使页面开发模块化，页面(组件)均可独立开发，
        降低相互之间因功能或视图创建的耦合性影响。同时也方便与组件的管理及复用。 
 
# 缺点
        将页面视图与交互逻辑模块化的同时也将视图与创建视图所需的数据以及组件之间的交互逻辑耦合在一起了。 