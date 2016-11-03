---
title: 链模式
date: 2016-11-03 15:56:33
tags: 技巧型模式
categories: 设计模式
---
>通过在对象方法中将当前对象返回，实现对同一个对象多个方法的链式调用。

<!--more-->
# 通过中间者实现链式调用
```javascript
var A = function () {                   //定义对象，返回一个对象。
    return B;
}
var B = A.prototype = {             //中介对象直接是源对象的原型
    length: 2,
    size: function () {
        return this.length;
    }
}
console.log(A().size());                //2
console.log(new A().size());        //2  因为属性和方法都挂载到了原型上去了
```
 
# jQuery的链式
```html
<div id="demo">demo</div>
<div id="test">test</div>
<script>
    /*
     var A = function(){             //把原型赋值给属性并返回
     return A.fn;
     }
     A.fn = A.prototype = {};
     */
    var A = function (selector) {
        return new A.fn.init(selector);           //如果直接return A.fn.init(selector)会导致后面的元素覆盖当前元素
    }
    A.fn = A.prototype = {
        constructor: A,                 //强化构造器
        init: function (selector) {                   //通过id获取元素     
            this[0] = document.getElementById(selector);       //如果直接返回元素，则不具备相应的属性方法
            this.length = 1;
            //console.log(this === A.fn, this === A.prototype, this);  //非new -- true true Object{...},new -- false false A.fn.A.init{...}
            return this;                                   //返回当前对象A.fn/A.prototype不是A
        },
        length: 2,
        size: function () {
            return this.length;
        }
    }
    A.fn.init.prototype = A.fn; //因为new了它，所以指明其原型指向，其实例的_proto_才会正确指向,如果未正确指向，size等方法会丢失
    var demo = A('demo');
    var test = A('test');
    console.log(demo.size());   //Object {0: div#demo, length: 1}  --如果是return A.fn.init 则为Object {0: div#test, length: 1}(覆盖了)
</script>
```    
- 注意
 + 如果要获取元素，则直接修改init方法即可。
 + JavaScript是弱类型语言，数组，对象，函数都被看成对象的实例。所以JavaScript中没有一个纯粹的数组类型。
 + 要实现数据呈数组的形式展现，只需要该对象具有数组特性即可（通常添加一到多个数组的方法即可）。

# 简版jQuery
```html
<div id="demo">demo</div>
<div id="test">test</div>
<script>
    var _$ = function (selector, context) {
        return new _$.fn.init(selector, context);
    }
    _$.fn = _$.prototype = {
        constructor: _$,
        init: function (selector, context) {
            this.length = 0,                                                //元素长度
                    context = context || document;               //上下文对象
            if (~selector.indexOf('#')) {                                   //如果是id选择符，
                this[0] = document.getElementById(selector.slice(1));
                this.length = 1;
            } else {                                                                  //如果不是id选择符（以下示意元素名称）
                var doms = context.getElementsByTagName(selector),   //在上下文中选择元素
                        i = 0,
                        len = doms.length;                  //元素长度
                for (; i < len; i++) {
                    this[i] = doms[i];                              //压入this中
                }
                this.length = len;                              //校正长度
            }
            this.context = context;                  //保存上下文
            this.selector = selector;               //保存选择器
            return this;                                   //返回对象
        },
        //添加数组特性
        push: [].push,
        sort: [].sort,
        splice: [].splice
    }
    _$.fn.init.prototype = _$;
    //_$.fn.init.constructor = _$.fn;           //这里有待商榷
    // console.log(_$('#test').length);
    //对象拓展
    _$.extend = _$.fn.extend = function () {
        var i = 1,                                          //拓展参数从第二个参数算起
                len = arguments.length,          //参数长度
                target = arguments[0],            //第一个参数为源参数
                j;
        if (i == len) {                             //如果只传了一个对象
            //console.log(this);
            target = this;                          //源对象为当前对象
            i--;
        }
        for (; i < len; i++) {                          //遍历参数中拓展对象
            for (j in arguments[i]) {                   //遍历属性
                target[j] = arguments[i][j];            //拓展属性
            }
        }
        return target;              //返回源对象
    }
    var demo = _$.extend({first: 5}, {second: 6}, {three: 7});           //此处返回的是一个object,跟参数列表一样，跟_$和_$.fn关系不大
    console.log(demo.first);                                //5
    demo = _$.extend(_$, {author: 'auto'});
    console.log(demo.author);                           //auto
    demo = _$.extend(_$.fn, {version: '1.0'});
    console.log(demo.version);                          //1.0
    demo = _$.fn.extend({
        getVersion: function () {
            return this.version;
        }
    });
    console.log(demo.getVersion());                 //1.0
    //拓展事件绑定
    _$.extend({
        on: (function () {                                      //为避免浏览器开销，第一加载时就创建好事件绑定模式
            if (document.addEventListener) {
                return function (type, fn) {
                    var i = this.length - 1;
                    for (; i >= 0; i--) {
                        //this[i].addEventListener(type, fn.bind(this[i]), false);
                        this[i].addEventListener(type, function(){
                            fn.call(this,false);
                        });
                    }
                    return this;
                }
            } else if (document.attachEvent) {          //IE
                return function (type, fn) {
                    var i = this.length - 1;
                    for (; i >= 0; i--) {
                        this[i].attachEvent('on' + type, function(){
                            fn.call(this);
                        });
                    }
                    return this;
                }
            } else {                                                    //其他
                return function (type, fn) {
                    var i = this.length - 1;
                    for (; i >= 0; i--) {
                        this[i]['on' + type] = function(){
                            fn.call(this);
                        };
                    }
                    return this;
                }
            }
        })()
    });
    //将'-'转为驼峰
    _$.extend({
        cameCase: function (str) {
            return str.replace(/\-(\w)/g, function (all, letter) {
                return letter.toUpperCase();
            });
        }
    });
    //设置css样式
    _$.extend({
        css: function () {
            var arg = arguments,
                    len = arg.length;
            if (this.length < 1) {
                return this;
            }
            if (len === 1) {                                        //只有一个参数时
                if (typeof arg[0] === 'string') {                   //如果是字符串  ('height')
                    if (this[0].currentStyle) {                             //IE
                        return this[0].currentStyle[arg[0]];
                    } else {                                                        //其他浏览器
                        return getComputedStyle(this[0], false)[arg[0]];
                    }
                } else if (typeof arg[0] === 'object') {    //为对象时则设置多个样式 ({'width':'200px','height':'30px'})
                    for (var i in arg[0]) {                             //遍历样式
                        for (var j = this.length - 1; j >= 0; j--) {    //遍历元素
                            this[j].style[_$.cameCase(i)] = arg[0][i];      //设置属性
                        }
                    }
                }
            } else if (len === 2) {                               //若有两个参数，设置一个样式  ('width','20px')
                for (var j = this.length - 1; j >= 0; j--) {
                    this[j].style[_$.cameCase(arg[0])] = arg[1];
                }
            }
            return this;
        }
    });
//获取或设置元素属性
    _$.extend({
        attr: function () {
            var arg = arguments,
                    len = arg.length;
            if (this.length < 1) {
                return this;
            }
            if (len === 1) {                                //一个参数
                if (typeof arg[0] === 'string') {               //字符串，则取出
                    return this[0].getAttribute(arg[0]);
                } else if (typeof arg[0] === 'object') {    //对象，设置属性
                    for (var i in arg[0]) {
                        for (var j = this.length - 1; i >= 0; i--) {
                            this[j].setAttribute(i, arg[0][i]);
                        }
                    }
                }
            } else if (len === 2) {                       //两个参数则设置每个元素单个属性
                for (var j = this.length - 1; j >= 0; j--) {
                    this[j].setAttribute(arg[0], arg[1]);
                }
            }
            return this;
        }
    });
//获取或设置元素内容
    _$.extend({
        html: function () {
            var arg = arguments,
                    len = arg.length;
            if (len === 0) {                        //无参数则获取第一个元素的内容
                return this[0] && this[0].innerHTML;
            } else {                                    //一个参数则设置每个元素的内容
                for (var i = this.length - 1; i >= 0; i--) {
                    this[i].innerHTML = arg[0];
                }
            }
            return this;
        }
    });
    //console.log(_$('#demo'));
    _$('div').css({
        height: '30px',
        border: '1px solid #000',
        'background-color': 'red'
    }).attr('class', 'demo').html('add demo text').on('click', function () {
        console.log(this);
    }).css('height');
</script>
```
 
# 核心思想
        通过在对象中的每个方法调用执行完毕后返回对象this来实现。

# 优点
        提高功能的开发效率，降低开发成本，简洁明了。 
