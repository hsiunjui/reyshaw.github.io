---
title: 建造者模式
date: 2016-11-03 14:23:22
tags: 创建型模式
categories: 设计模式
---
>将一个复杂对象的构建层与其表示层相分离，同样的建构过程可采用不同的表示。建造者模式关注的是创建对象的过程。通常将创建对象的类模块化，这样使被创建的类的每一个模块都可以得到灵活的运用和高质量的复用。

<!--more-->
# 建造者模型
- 化整为零，缓存构建
```javascript
//创建应聘者基类
var Human = function(param){
    this.skill = param && param.skill || '保密';      //技能
    this.hobby = param && param.hobby || '保密';      //爱好
}
Human.prototype = {
    constructor:Human,
    getSkill:function(){
        return this.skill;
    },
    getHobby:function(){
        return this.hobby;
    }
}
//姓名类
var Named = function(name){
    var that = this;                                                //缓存变量
    (function(name,that){
        that.wholeName = name;                                      //全名
        if(name.indexOf(' ') > -1){
            that.firstName = name.slice(0,name.indexOf(' '));       //姓
            that.secondName = name.slice(name.indexOf(' '));        //名
        }
    })(name,that);
}
//职位类
var Work = function(work){
    var that = this;                                            //同一个引用，一个改变会引起另外一个改变
    (function(work,that){
        switch (work){
            case 'code':
                that.work = '工程师';
                that.wordDescription = 'coding...';
                break;
            case 'UI':
            case 'UE':
                that.work = '设计师';
                that.wordDescription = 'arting...';
                break;
            case 'teach':
                that.work = '教师';
                that.wordDescription = 'teaching...';
                break;
            default:
                that.work = work;
                that.wordDescription = 'not find...';
        }
    })(work,that);
}
Work.prototype.changeWork = function(work){             //变更工作职位
    this.work = work;
}
Work.prototype.changeDescription = function(setence){   //变更工作描述
    this.wordDescription = setence;
}
/**
 * 建造者类
 * 参数name:姓名（全名）
 * 参数work:职位
 */
var Person = function(name,work){
    var _person = new Human();          //这里未传递param参数，因此技能和爱好会显示保密
    _person.name = new Named(name);     //得到姓和名
    _person.work = new Work(work);      //获得职位和职位描述
    return _person;
}
//test
var person = new Person('xiao ming','code');
console.log(person.skill);          //保密
console.log(person.getHobby());     //保密
console.log(person.work.work);      //工程师
console.log(person.work.wordDescription);   //coding...
person.work.changeDescription('coding again');
console.log(person.work.wordDescription);   //coding again
/**
 * 把整个应聘者分成三部分构建
 * 通过一个整体的Peron类来组合，应用缓存对象的相关属性和方法，返回缓存对象
 */
``` 
# 适用场景
     --------该模式适合复杂对象或复合对象的创建。建造者模式对于整体对象类的拆分无形中增加了结构的复杂性，因此对于对象粒度很小，或者模块间的复用率很低而且变动不大，最好还是创建整体对象。