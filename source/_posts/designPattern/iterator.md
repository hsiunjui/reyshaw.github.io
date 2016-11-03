---
title: 迭代器模式
date: 2016-11-03 15:49:09
tags: 行为型模式
categories: 设计模式
---
>在不暴露对象内部结构的同时，可以顺序地访问聚合对象内部的元素。

<!--more-->
# 元素迭代器
```html
<div id="demo">
    <ul>
        <li>1</li>
        <li>2</li>
        <li>3</li>
        <li>4</li>
        <li>5</li>
    </ul>
</div>
<script>
    var Iterator = function (items, container) {
        var container = document.getElementById(container) || document,      //获取父容器
                items = container.getElementsByTagName(items),                      //获取父容器下的元素
                length = items.length,                 //获取元素长度
                index = 0;                                   //默认索引值为0
        var splice = [].splice;                          //缓存数组的splice方法
        return {
            first: function () {                 //获取第一个元素
                index = 0;                              //校正索引
                return items[index];               //返回第一个元素
            },
            last: function () {                //获取最后一个元素
                index = length - 1;              //校正当前索引
                return items[index];            //返回最后一个元素
            },
            pre: function () {                //获取前一个元素
                if (--index > 0) {                  //如果不是第一个元素
                    return items[index];            //索引减1,返回前一个
                } else {                               //如果是第一个元素
                    index = 0;                          //校正索引
                    return null;                         //返回null
                }
            },
            next: function () {              //获取后一个元素
                if (++index < length) {            //如果不是最后一个元素
                    return items[index];                //索引加1,返回后一个
                } else {                                  //如果是最后一个
                    index = length - 1;                  //校正索引
                    return null;                              //返回null
                }
            },
            get: function (num) {          //获取某一个元素
                index = num >= 0 ? num % length : num % length + length;     //大于0正向获取，小于0反向获取
                return items[index];                    //设置索引并返回指定元素
            },
            dealEach: function (fn) {   //对每一个元素执行某一个方法
                var args = splice.call(arguments,1);         //第二个参数起为回调函数的参数
                for(var i = 0 ;i<length;i++){
                    fn.apply(items[i],args);                            //使回调函数在每一个元素作用域执行一次
                }
            },
            dealItem: function (num,fn) {         //对某一个元素执行某一个方法
                fn.apply(this.get(num),splice.call(arguments,2));    //在指定作用域中执行,参数大于2个后面将作为回调参数
            },
            exclusive: function (num,allFn,numFn) {          //排他方式处理某一个元素
                this.dealEach(allFn);               //对所有元素执行回调函数
                if(Object.prototype.toString.call(num) === "[object Array]" ){      //如果num为数组
                    for(var i = 0,len = num.length;i<len;i++){          //遍历数组
                        this.dealItem(num[i],numFn);                            //执行排他函数
                    }
                }else{                  //如果num不是数组
                    this.dealItem(num,numFn);               //直接执行排他函数
                }
            }
        }
    }
    var demo = new Iterator('li','demo');
    //获取元素
    console.log(demo.first());              //<li>1</li>
    console.log(demo.pre());               //null
    console.log(demo.next());             //<li>2</li>
    console.log(demo.get(1000));       //<li>1</li>
    //处理所有元素
    demo.dealEach(function(text,color){
        this.innerHTML = text;
        this.style.background = color;
    },'test','pink');                                                   //文字变为test,背景变为了pink
    //排他方法处理第三个和第四个元素
    demo.exclusive([2,3],function(){
        this.innerHTML = '被排除的';
        this.style.background = 'green';
    },function(){
        this.innerHTML = '选中的';
        this.style.background = 'red';
    });         //除了第三和第四个元素的文本为选中的且背景为红色外，其他的文本为被排除的，背景为绿色
</script>
```

# 数组迭代器
```javascript
var eachArray = function (arr, fn) {
    var i = 0,                //循环变量
        len = arr.length;     //数组长度
    for (; i < len; i++) {          //遍历
        fn.call(arr[i], i, arr[i]);         //回调函数中第一个为索引，第二个为该索引对应的值
    }
}
var arr = [1, 2, 3, 4];
eachArray(arr, function (key, value) {
    arr[key] += value;
})
console.log(arr);           //[2,4,6,8]
```

# 对象迭代器
```javascript
var eachObject = function (obj, fn) {
    for (var i in obj) {
        fn.call(obj[i], i, obj[i]);     // 与数组类似
    }
}
var obj = {
    one: 1,
    two: 2,
    three: 3
}
eachObject(obj, function (key, value) {
    obj[key] += value;
})
console.log(obj);           // { one: 2, two: 4, three: 6 }
```

# 同步变量迭代器
- 问题为如果有对象A，要访问该对象b属性下面的c属性，不能直接A.b.c访问，而是A&&A.b&&A.b.c,这是便需要迭代校验。
```javascript
//同步变量
var A = {
    common: {},              //公共的
    client: {                     //客户端
        user: {
            username: 'name',
            uid: '123'
        }
    },
    server: {}                //服务端
}
//同步变量迭代取值器
var AGetter = function (key) {
    if (!A) {
        return undefined;
    }
    var result = A;                 //获取同步变量A
    key = key.split('.');           //解析属性层次序列
    for (var i = 0, len = key.length; i < len; i++) {       //迭代A对象属性
        if (result[key[i]] !== undefined) {                           //如果第i层属性存在
            result = result[key[i]];                                          //迭代该属性值(result层次变深)
        } else {                                                                //如果不存在属性
            return undefined;                                                //返回未定义
        }
    }
    return result;                  //返回获取结果
}
//console.log(AGetter('client.user.username'));       //name
//console.log(AGetter('server.lang.local'));              //undefined  因为server下没有lang属性
//同步变量迭代设值器
var ASetter = function (key, val) {
    if (!A) {                         //如果A不存在则返回false
        return false;
    }
    var result = A;
    key = key.split('.');
    for (var i = 0, len = key.length; i < len - 1; i++) {       //之所以是len-1是把i的最终结果放到了return里
        console.log(key[i]);
        if (result[key[i]] === undefined) {         //如果第i层属性对应的值不存在
            result[key[i]] = {};                                //则定义为对象
        }
        if (!(result[key[i]] instanceof Object)) {        //如果第i层属性对应的值不是一个对象
            throw new Error('A.' + key.splice(0, i + 1).join('.') + ' is not Object');      //抛出错误
            return false;
        }
        result = result[key[i]];                //迭代该属性值
    }
    return result[key[i]] = val;            //返回设置成功的属性值
}
console.log(ASetter('client.module.news.sports', 'on'));             //on
console.log(ASetter('client.user.username.sports', 'on'));           // A.client.user.username is not Object
```

# 分支循环迭代
```javascript
var data = {};                      //假设为canvas的context.getImageData(...) .data获取的像素数据
var alpha = 0.5;                  //透明度
for (var i = 0, len = data.length; i < len; i += 4) {
    switch (t) {
        case 'red':                 //红色滤镜
            data[i + 1] = 0;
            data[i + 2] = 0;
            data[i + 3] = alpha;
            break;
        case 'gray':                //灰色滤镜
            var num = parseInt((data[i] + data[i + 1] + data[i + 2]) / 3);
            data[i] = num;
            data[i + 1] = num;
            data[i + 2] = num;
            data[i + 3] = alpha;
            break;
        //其他滤镜省略...
        //该方式的确定是每次遍历都需要进行分支判断，如果分支较大，则性能消耗也会很大
    }
}
//将循环抽象成一个迭代器
var Deal = (function () {
    var method = {                  //状态模式封装
        'default': function (i) {           //默认类型
            return method['gary'](i);
        },
        'red': function (i) {               //红色滤镜
            data[i + 1] = 0;
            data[i + 2] = 0;
            data[i + 3] = alpha;
        },
        'gray': function (i) {              //灰色滤镜
            data[i] = data[i + 1] = parseInt(data[i + 2] = (data[i] + data[i + 1] + data[i + 2] / 3));
            data[3] = alpha;
        }
        //其他滤镜省略...
    }
    return function (type) {                  //通过给定类型返回对应滤镜算法，调用时需要传参
        return method[type] || method['default'];
    }
})();
//迭代处理数据
function eachData(fn) {
    for (var i = 0, len = data.length; i < len; i += 4) {
        fn(i);          //处理一组像素数据
    }
}
``` 

# 优点
        简化了代码中的循环语句，使代码结构清晰紧凑(这些简化的循环语句实质上隐形地移到了迭代器中)。
        这有效地解决了对象使用者与对象内部结构之间的耦合，同时也为我们提供了操作对象的一个统一接口。