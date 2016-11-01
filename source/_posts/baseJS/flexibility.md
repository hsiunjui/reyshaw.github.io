---
title: JavaScript的灵活性
date: 2016-11-01 17:42:03
tags: js特性
categories: 客户端JS
---
>JavaScript具有很大的灵活性，以下是实现表单的检查(只检查用户名，邮箱和密码)和链式调用的示意。

<!--more-->
# 方式一(全局定义)
```javascript
console.log('================1.定义全局函数==================');
function checkName1(){
    console.log('检查用户名...');
}
function checkEmail1(){
    console.log('检查邮箱...');
}
function checkPassword1(){
    console.log('检查密码...');
}
checkName1();
checkEmail1();
checkPassword1();
```

# 方式二(通过定义变量保存函数)
```javascript
console.log('================2.定义函数变量======================');
var checkName2 = function(){
    console.log('检查用户名...');
}
var checkEmail2 = function(){
    console.log('检查邮箱...');
}
var checkPassword2 = function(){
    console.log('检查密码...');
}
checkName2();
checkEmail2();
checkPassword2();
```

# 方式三(定义json对象方式)
```javascript
console.log('================3.定义JSON对象======================');
var CheckObject3 = {
    checkName3:function(){
        console.log('检查用户名...');
    },
    checkEmail3:function(){
        console.log('检查邮箱...');
    },
    checkPassword3:function(){
        console.log('检查密码...');
    }
}
CheckObject3.checkName3();
CheckObject3.checkEmail3();
CheckObject3.checkPassword3();
```

# 方式四(对象的另外一种形式，可以看成类的静态公有方法)
```javascript
console.log('================4.定义类的静态公有方法======================');
var CheckObject4 = function(){
}
CheckObject4.checkName4 = function(){
    console.log('检查用户名...');
}
CheckObject4.checkEmail4 =function(){
    console.log('检查邮箱...');
}
CheckObject4.checkPassword4 = function(){
    console.log('检查密码...');
}
CheckObject4.checkName4();
CheckObject4.checkEmail4();
CheckObject4.checkPassword4();
```
 # 方式五(通过代理对象返回一个对象）
 ```javascript
console.log('================5.定义代理对象======================');
var CheckObject5 = function(){
    return {
        checkName5:function(){
            console.log('检查用户名...');
        },
        checkEmail5:function(){
            console.log('检查邮箱...');
        },
        checkPassword5:function(){
            console.log('检查密码...');
        }
    }
}
var checkObj5 = CheckObject5();
checkObj5.checkName5();
checkObj5.checkEmail5();
checkObj5.checkPassword5();
```

 # 方式六(通过类)
 ```javascript
console.log('================6.定义类======================');
var CheckObject6 = function(){
    this.checkName6 = function(){
        console.log('检查用户名...');
    }
    this.checkEmail6 = function(){
        console.log('检查邮箱...');
    }
    this.checkPassword6 = function(){
        console.log('检查密码...');
    }
}
var checkObj6 = new CheckObject6();
checkObj6.checkName6();
checkObj6.checkEmail6();
checkObj6.checkPassword6();
```
# 方式七(基于原型)
```javascript
console.log('================7.定义原型方法======================');
var CheckObject7 = function(){
}
CheckObject7.prototype.checkName7 = function(){
    console.log('检查用户名...');
}
CheckObject7.prototype.checkEmail7 = function(){
    console.log('检查邮箱...');
}
CheckObject7.prototype.checkPassword7 =function(){
    console.log('检查密码...');
}
var checkObj7 = new CheckObject7();
checkObj7.checkName7();
checkObj7.checkEmail7();
checkObj7.checkPassword7();
```
# 方式八(原型改进)
```javascript
console.log('================8.定义原型重写======================');
var CheckObject8 = function(){
}
CheckObject8.prototype = {
    constructor:CheckObject8,
    checkName8:function(){
        console.log('检查用户名...');
    },
    checkEmail8:function(){
        console.log('检查邮箱...');
    },
    checkPassword8:function(){
        console.log('检查密码...');
    }
}
var checkObj8 = new CheckObject8();
checkObj8.checkName8();
checkObj8.checkEmail8();
checkObj8.checkPassword8();
```
 # 链式调用一(在对象中返回this对象)
```javascript
console.log('================A.链式调用一======================');
var CheckObject9 = {
    checkName9:function(){
        console.log('检查用户名...');
        return this;
    },
    checkEmail9:function(){
        console.log('检查邮箱...');
        return this;
    },
    checkPassword9:function(){
        console.log('检查密码...');
        return this;
    }
}
CheckObject9.checkName9().checkEmail9().checkPassword9();
```

# 链式调用二(在原型中返回this对象)
```javascript
console.log('================B.链式调用二======================');
var CheckObject10 = function(){
}
CheckObject10.prototype = {
    constructor:CheckObject10,
    checkName10:function(){
        console.log('检查用户名...');
        return this;
    },
    checkEmail10:function(){
        console.log('检查邮箱...');
        return this;
    },
    checkPassword10:function(){
        console.log('检查密码...');
        return this;
    }
}
var checkObj10 = new CheckObject10();
checkObj10.checkName10().checkEmail10().checkPassword10();
```
# 基于Function的链式调用（会污染原生对象Function）
```javascript
console.log('================基于Function原型方法的链式调用======================');
Function.prototype.checkName11 = function(){
    console.log('检查用户名...');
    return this;
}
Function.prototype.checkEmail11 = function(){
    console.log('检查邮箱...');
    return this;
}
Function.prototype.checkPassword11 = function(){
    console.log('检查密码...');
    return this;
}
var checkObj11 = function(){
}
//checkObj11.checkName11();
//checkObj11.checkEmail11();
//checkObj11.checkPassword11();
checkObj11.checkName11().checkEmail11().checkPassword11();
/**
 * 为Function添加一个添加方法的方法
 */
console.log('================基于Function添加方法的原型方法的链式调用======================');
Function.prototype.addMethods = function(name,fn){
    this[name] = fn;
    //this.prototype[name] = fn;    //如果使用这种方式，需用使用new 关键字来创建对象才能调用
    return this;
}
var checkName12 =function(){
    console.log('检查用户名...');
    return this;
}
var checkEmail12 = function(){
    console.log('检查邮箱...');
    return this;
}
var checkPassword12 = function(){
    console.log('检查密码...');
    return this;
}
var checkObj12 = function(){
}
checkObj12.addMethods('checkName12',checkName12)
.addMethods('checkEmail12',checkEmail12)
.addMethods('checkPassword12',checkPassword12);
checkObj12.checkName12().checkEmail12().checkPassword12();
```