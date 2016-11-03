---
title: 观察者模式
date: 2016-11-03 15:16:11
tags: 行为型模式
categories: 设计模式
---
>又被称作发布-订阅者模式或消息机制，定义了一种依赖关系，解决了主体对象与观察者之间功能的耦合。

<!--more-->
# 卫星、飞机、中转站之间的观察者模型
- 卫星为飞机导航，卫星作为一个观察者监测飞机的位置信息，那这架飞机便是一个被观察者或一个主体对象。飞机向卫星发送位置消息，卫星接收位置消息。而中转站想要获取飞机的具体位置，需要在卫星上注册，以便接收这家飞机的信息。这样，飞机发送信息给卫星，卫星广播给注册的中转站，并在必要的时候移除中转站，形成一个观察者的模式。 

# 观察者对象
```javascript
var Observer = (function () {             //将观察者放到闭包中，页面加载即执行
    var _msg = {};                                      //防止消息队列暴露而被篡改，故将消息容器设为静态私有变量
    return {
        regist: function (type, fn) {                            //注册信息接口,接收消息类型和处理动作
            if (typeof _msg[type] === 'undefined') {                  //如果消息不存在，则应该创建一个该消息类型
                _msg[type] = [fn];                                                   //将动作推入到该消息对应的动作执行队列中，这里是一个动作数组
            } else {
                _msg[type].push(fn);                                             //将动作方法直接推入
            }
        },
        fire: function (type, args) {             //发布信息接口(将所有订阅者订阅的消息一次执行)
            if (!_msg[type]) {                                 //如果消息没有注册，则返回
                return;
            }
            var events = {                                //定义消息信息
                    type: type,                                 //消息类型
                    args: args || {}                           //消息所要携带的信息（参数）
                },
                i = 0,                                       //消息动作循环变量
                len = _msg[type].length;        //消息动作长度
            for (; i < len; i++) {                          //遍历消息动作
                _msg[type][i].call(this, events);       //依次执行注册的消息对应的动作序列
            }
        },
        remove: function (type, fn) {                //移除信息接口
            if (_msg[type] instanceof Array) {            //检测消息队列是否存在
                var i = _msg[type].length - 1;                  //从最后一个消息动作遍历
                for (; i >= 0; i--) {
                    _msg[type][i] === fn && _msg[type].splice(i, 1);      //如果存在动作则在消息动作序列中移除相应动作
                }
            }
        }
    }
})();
//订阅消息
var listen = function(e){
    console.log(e.type + ' ' + e.args.msg);
}
Observer.regist('test', listen);
//发布消息
Observer.fire('test', {msg: '传递参数'});       //test 传递参数
//取消订阅
Observer.remove('test',listen);
Observer.fire('test', {msg: '传递参数'});      //无输出
```
 
# 模块通信
```html
<div id='msg_num'>0</div>
    <ul id="msg">
    </ul>
<input type='text' id='user_input'/>
<button id='user_submit'>提交</button>
<script>
     function $(id) {                                        //外观模式，通过id获取元素
        return document.getElementById(id);
    }
    //模块A，既是消息发出者也是消息执行者
    (function () {
        function addMsgItem(e) {                        //追加一则消息
            var text = e.args.text,
                    ul = $('msg'),
                    li = document.createElement('li'),
                    span = document.createElement('span');
                    span.innerHTML = '删除';
            li.innerHTML = text;
            span.onclick = function () {                    //删除追加的项
                ul.removeChild(li);
                Observer.fire('removeCommentMessage', {num: -1});
            }
            li.appendChild(span);
            ul.appendChild(li);
        }
        Observer.regist('addCommentMessage',addMsgItem);
    })();
    //模块B，消息执行者
    (function(){
        function changeMsgNum(e){               //更改用户消息数
            var num = e.args.num;
            $('msg_num').innerHTML = parseInt($('msg_num').innerHTML)+num;
        }
        Observer.regist('addCommentMessage',changeMsgNum);
        Observer.regist('removeCommentMessage',changeMsgNum);
    })();
    //模块C，消息发出者
    (function(){
        $('user_submit').onclick = function(){          //提交数据
            var text = $('user_input');
            if(text.value == ''){
                return;
            }
            Observer.fire('addCommentMessage',{
                text:text.value,
                num:1
            })
            text.value = '';
        }
    })()                        //regist即添加方法，fire即执行队列里面的方法，剩下的便是谁注册和谁发布的问题了。
</script>
```
 
# 对象间解耦
```javascript
//学生类
var Student = function (result) {
    var that = this;
    this.result = result;                //回答的结果
    that.say = function () {          //回答问题的动作
        console.log(that.result);
    }
}
Student.prototype.answer = function (question) {       //回答问题方法
    Observer.regist(question, this.say);                             //注册参数问题
}
Student.prototype.sleep = function (question) {               //睡觉的同学不能回答老师问题
    console.log(this.result + ' ' + question + '已经被注销');
    Observer.remove(question, this.say);                         //取消对老师问题的监听
}
//教师类
var Teacher = function () {
}
Teacher.prototype.ask = function (question) {         //老师提问题的方法
    console.log('问题是:' + question);
    Observer.fire(question);                                            //发布提问消息
}
//创建学生对象并构造答案结果
var stu1 = new Student('stu1'),
    stu2 = new Student('stu2'),
    stu3 = new Student('stu3');
//学生订阅老师的问题
stu1.answer('question1');            
stu1.answer('question2');
stu2.answer('question1');
stu3.answer('question1');              //到这里_msg里question1里已经有3个函数对象了，有调优的空间
stu3.answer('question2');
stu3.sleep('question2');
var teacher = new Teacher();        //构建教师对象
//老师发布了2个问题
teacher.ask('question1');
teacher.ask('question2');
/**
 stu3 question2已经被注销            //因stu3订阅第二个问题后睡着了，所以取消订阅第二个问题
 问题是:question1                              //老师发布问题1，因3位学生都监听，故均有答案
 stu1
 stu2
 stu3
 问题是:question2                              //老师发布问题2，因现在只有stu1订阅，故只有他一人回答
 stu1
 */
``` 

# 应用场景
        主要用于解决类或对象之间的耦合，解耦两个相互依赖的对象，使其依赖于观察者消息机制。
        另外在大型项目中用于模块之间的相互通信也是常用的方式之一。

# 注意
    对于每一个订阅者来说，其自身既可以是消息的发出者也可以是消息的执行者，
    这都依赖于调用观察者对象的三种方法(订阅消息，注销消息，发布消息)中的哪一种。 