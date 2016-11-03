---
title: 状态模式
date: 2016-11-03 15:20:36
tags: 行为型模式
categories: 设计模式
---
> 当一个对象的内部状态发生改变时，会导致其他行为的改变，这看起来像是改变了对象。其主要目的是将条件判断的不同结果转化为状态对象的内部状态(一般是私有的)，然后提供一个能够调用状态对象内部状态的接口方法对象。

<!--more-->
# 条件分支
```javascript
//条件分支
function showResult(result) {
    if (result == 0) {
        //处理结果0
    } else if (result == 1) {
        //处理结果1
    } else if (result == 2) {
        //处理结果2
    } else if (result == 3) {
        //处理结果3
    }
}
/**
 *  对新增，修改，调用，删除以及管理上都会带来一定的不便利。
 */
```

# 状态对象
```javascript
var ResultState = (function () {
    var States = {               //判断结果保存在内部状态中
        state0: function () {       //处理结果0
        },
        state1: function () {       //处理结果1
        },
        state2: function () {       //处理结果2
        },
        state3: function () {       //处理结果3
        }
    }
    function show(result) {             //获取某一种状态并执行其相应的方法
        States['state' + result] && States['state' + result]();
    }
    return {
        show: show              //返回调用状态方法接口
    }
})();
ResultState.show(0);            //外部调用
```
 
# 复合状态
```javascript
//状态类
var MarryState = function () {
    var _currentState = {},                 //当前状态
        states = {                                 //动作与状态方法映射
            jump: function () {
                console.log('jump')              //跳跃
            },
            move: function () {
                console.log('move')             //移动
            },
            shoot: function () {
                console.log('shoot')             //射击
            },
            squat: function () {
                console.log('squat')             //蹲下
            }
        };
    //动作控制类
    var Action = {
        changeState:function(){             //改变状态方法
            var arg = arguments;                    //组合动作通过传递多个参数实现
            _currentState = {};                        //重置内部状态
            if(arg.length){                        //如果有动作则添加动作
                for(var i = 0,len = arg.length;i<len;i++){
                    _currentState[arg[i]] = true;              //向内部状态中添加动作
                }
            }
            return this;                                               //返回动作控制类以便链式调用
        },
        goes:function(){                                //执行动作
            console.log('触发一次动作');
            for(var i in _currentState){                //遍历内部状态保存的动作
                states[i] && states[i]();                       //如果该动作存在则执行
            }
            return this;                                    //返回当前动作类以便链式调用
        }
    }
    return {                                        //返回接口方法
        change: Action.changeState,
        goes:Action.goes
    }
}
MarryState().change('jump','shoot').goes().goes();              //触发一次动作 jump shoot 触发一次动作 jump shoot
new MarryState().change('jump','shoot').goes().goes();      //触发一次动作 jump shoot 触发一次动作 jump shoot
``` 

# 应用场景
        简化分支判断流程。 