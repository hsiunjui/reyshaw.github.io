---
title: 策略模式
date: 2016-11-03 15:24:27
tags: 行为型模式
categories: 设计模式
---
>将定义的一组算法封装起来，使其相互之间可以替换。封装的算法具有一定的独立性，不会随客户端的变化而变化。与状态模式不同的是，策略模式不需要管理状态、状态间没有依赖关系、策略之间可以相互替换、在策略对象内部保存的是相互独立的算法。

<!--more-->
# 价格策略
```javascript
//更似一个普通的状态对象
var PriceStrategy = (function () {
    var strategy = {                            //内部算法对象
        return30: function (price) {            //100返30
            return +price + parseInt(price / 100) * 30;                 //+price转化为数字类型
        },
        return50: function (price) {            //100返50
            return +price + parseInt(price / 100) * 50;
        },
        percent90: function () {            //9折
            return price * 100 * 90 / 10000;
        },
        percent80: function () {            //8折
            return price * 100 * 80 / 10000;
        },
        percent50: function () {            //5折
            return price * 100 * 50 / 10000;
        }
    }
    return function(algorithm,price){               //策略算法调用接口
        return strategy[algorithm] && strategy[algorithm](price);   //如果算法存在则调用算法，否则返回false
    }
})();
console.log(PriceStrategy('return50','314.67'));        //464.67
```
# 表单正则验证策略
```javascript
var InputStrategy = (function(){
    var strategy = {
        notNull:function(value){            //是否为空
            return /\s+/.test(value) ? '请输入内容' : '';
        },
        number:function(value){          //是否是数字
            return /^[0-9]+(\.[0-9]+)?$/.test(value) ? '':'请输入数字';
        },
        phone:function(value){           //是否是本地电话
            return /^\d{3}-\d{8}$|^\d{4}\-\d{7}$/.test(value)?'':'请输入正确的电话号码';
        }
    }
    return {
        check:function(type,value){                    //验证接口
            value = value.replace(/^\s+|\s+$/g,'');         //去除收尾空白符
            return strategy[type]?strategy[type](value):'没有该类型检测方法';
        },
        addStrategy:function(type,fn){                       //添加策略接口方法
            strategy[type] = fn;
        }
    }
})();
console.log(InputStrategy.check('phone','021-23456785'));       //通过
InputStrategy.addStrategy('nickname',function(value){                    //拓展算法
    return /^[a-zA-Z]\w{3,7}$/.test(value) ? '':'请输入4-8位昵称';
});
console.log(InputStrategy.check('nickname','asd'));                     //请输入4-8位昵称
```
# 状态模式与策略模式的侧重
        状态模式一般侧重于对状态的控制来决定表现行为，因此状态之间通常是不能相互替换的。
        而策略模式的核心是算法，每种算方法处理的业务逻辑是相同的因此他们可以相互替换，
        策略模式并不关心使用者环境，因为同一种策略模式最终产出是一定的。 

# 优点
        使得算法脱离于模块逻辑而独立管理，使用算法不受模块逻辑约束。这样可以对算法进行单元测试，
        提高算法的质量。同时在算法的复用以及维护上有很大的便利。

# 缺点
        增加了用户对策略对象的使用成本，因为需要用户了解具体算法的实现以决定使用哪种算法。
        由于算法间相互独立，一些复杂算法处理想沟通逻辑便无法共享。
        (当然也可以通过享元模式来解决)。 