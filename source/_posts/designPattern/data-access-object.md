---
title: 数据访问对象模式
date: 2016-11-03 16:08:41
tags: 技巧型模式
categories: 设计模式
---
>抽象和封装对数据源的访问与存储，DAO通过对数据源链接的管理方便对数据的访问与存储。

<!--more-->
# 本地存储DAO(执行环境为chrome浏览器)
```javascript
 var BaseLocalStorage = function (preId, timeSign) {
        this.preId = preId;                         //本地存储数据库前缀
        this.timeSign = timeSign || '|-|';     //时间戳与存储数据之间的拼接符
    }
    BaseLocalStorage.prototype = {
        constructor: BaseLocalStorage,
        status: {                               //操作状态
            SUCCESS: 0,
            FAILURE: 1,
            OVERFLOW: 2,
            TIMEOUT: 3,
        },
        storage: localStorage || window.localStorage,      //本地存储方法
        getKey: function (key) {                                        //获取本地数据库真实字段
            return this.preId + key;                                            //前缀+字段名
        },
        set: function (key, value, callback, time) {            //添加数据
            var status = this.status.SUCCESS;                       //默认操作状态为为成功
            key = this.getKey(key);                                          //获取真实的key值
            try {
                time = new Date(time).getTime() || time.getTime();         //获取时间戳
            } catch (e) {
                time = new Date().getTime() + 1000 * 60 * 60 * 24 * 31; //时间戳有误，默认为1个月
            }
            try {
                this.storage.setItem(key, time + this.timeSign + value);     //存入本地值key为前缀+key,value为时间戳+分隔符+value
            } catch (e) {
                status = this.status.OVERFLOW;                                       //存储溢出，返回溢出状态
            }
            callback && callback(this, status, key, value);               //有回调则执行回调方法
        },
        get: function (key, callback) {                    //获取数据
            var status = this.status.SUCCESS,           //默认为成功状态
                    key = this.getKey(key),                      //获取真是的key
                    value = null,                                       //默认值为null
                    timeSign = this.timeSign.length,       //时间戳与存储数据之间的拼接符长度
                    that = this,                                         //缓存当前对象
                    index,                                                //事件戳与存储数据之间的拼接符位置
                    time,                                                  //时间戳
                    result;                                                //最终获取数据
            try {
                value = that.storage.getItem(key);        //获取字段对应的数据字符串(时间戳+分隔符+value)
            } catch (e) {
                result = {
                    status: that.status.FAILURE,             //置为失败状态
                    value: null                                          //值置为null
                }
                callback && callback(this, result.status, result.value);      //有回调执行回调
                return result;                                                                      //返回结果对象
            }
            if (value) {                          //如果成功获取字符串
                index = value.indexOf(this.timeSign);           //时间戳与存储数据之间的拼接起始位置
                time = +value.slice(0, index);                        //获取时间戳
                if (new Date(time).getTime() > new Date().getTime() || time == 0) {         //如果时间未过期
                    value = value.slice(index + timeSign);                 //获取数据结果
                } else {                                                         //如果时间过期
                    value = null;                                                   //值置为null
                    status = that.status.TIMEOUT;                      //状态为超时
                    that.remove(key);                                           //删除该字段
                }
            } else {                                            //未获取到数据字符串
                status = that.status.FAILURE;         //状态置为失败
            }
            result = {
                status: status,
                value: value
            }
            callback && callback(this, result.status, result.value);      //执行回调
            return result;                                                                      //返回状态对象
        },
        remove: function (key, callback) {           //删除数据
            var status = this.status.FAILURE,               //默认状态置为失败
                    key = this.getKey(key)                        //获取实际数据字段名称
            value = null;                                                //设置默认数据结果为空
            try {
                value = this.storage.getItem(key);   //获取字段对应的数据
            } catch (e) {
                console.log('获取数据失败');
            }
            if (value) {                                 //如果数据存在
                try {
                    this.storage.removeItem(key);         //删除数据
                    status = this.status.SUCCESS;       //设置操作成功
                } catch (e) {
                    console.log('删除失败');
                }
            }
            callback && callback(this, status, status > 0 ? null : value.slice(value.indexOf(this.timeSign) + this.timeSign.length));//回调
        }
    }
    var Ls = new BaseLocalStorage('LS_');
    Ls.set('a','xiaoming',function(){        //未传Time,默认一个月
        console.log(arguments);                     //[BaseLocalStorage, 0, "LS_a", "xiaoming"]
    });                                                     //    LS_a       1462887262598|-|xiaoming
    var getter = Ls.get('a',function(){
        console.log(arguments);                    //[BaseLocalStorage, 0, "xiaoming"]
    });
    console.log(getter.value);                //xiaoming
    Ls.remove('a',function(){           //删除已有的记录
        console.log(arguments);                    // [BaseLocalStorage, 0, "xiaoming"]
    });
    Ls.remove('a',function(){           //删除不存在的记录
      console.log(arguments);                      //[BaseLocalStorage, 1, null]
    })
    getter = Ls.get('a',function(){     //获取不存的记录
        console.log(arguments);                    //[BaseLocalStorage, 1, null]
    });
    console.log(getter.value);              //null
```

# Nodejs中的DAO操作
```javascript
/**
 *  数据连接配置模块 config.js
 */
module.exports = {
    DB: {                        //数据配置对象
        db: 'demo',               //数据库名称
        host: 'localhost',       //主机名
        port: '8008'               //端口号
    }
}
/**
 *  数据操作模块 db.js
 */
var mongodb = require('mongodb');   //引入mongodb模块
var config = require('./config').DB;     //引入数据配置模块
var d = new mongodb.Db(
    config.db,                         //数据库名称
    new mongodb.Server(
        config.host,                        //主机名
        config.port,                         //端口号
        {auto_reconnet: true}         //自动连接
    ),
    {safe: true}                      //安全模式
);
exports.DB = function (col) {        //数据数据访问对象
    return {
        insert: function (data, success, fail) {        //插入数据项
            connect(col, function (col, db) {               //打开数据库操作col集合
                col.insert(data, function (err, docs) {         //向集合中插入数据
                    if (err) {                                      //失败，跑出错误
                        fail && fail(err);
                    } else {                                        //成功，执行成功回调
                        success && success(docs);
                    }
                    db.close();                         //关闭数据库
                })
            })
        },
        remove: function (data, success, fail) {                          //删除数据项
            connect(col, function (col, db) {
                col.remove(data, function (err, len) {                          //在集合中删除数据
                    if (err) {
                        fail && fail(err);
                    } else {
                        success && success(len);
                    }
                    db.close();
                })
            })
        },
        update: function () {                                                  //更新数据项
            connect(col, function (con, doc, success, fail) {
                col.update(con, doc, function (err, len) {                   //在集合中更新数据
                    if (err) {
                        fail && fail(err);
                    } else {
                        success && success(len);
                    }
                    db.close();
                })
            })
        },
        find: function () {
            connect(col, function (col, db) {                           //查找数据
                col.find(con).toArray(function (err, docs) {               //在集合中查找数据
                    if (err) {
                        fail && fail(err);
                    } else {
                        success && success(docs);
                    }
                    db.close();
                })
            })
        }
    }
}
function connect(col, fn) {     //连接数据库  col集合名  fn操作方法
    d.open(function (err, db) {     //打开数据库
        if (err) {
            throw err;
        } else {
            db.collection(col, function (err, col) {        //操作集合
                if (err) {
                    throw err;
                } else {
                    fn && fn.call(col, db);             //执行操作
                }
            })
        }
    })
}
/**
 * 数据测试模块 test.js
 */
var DB = require('./db').DB;        //引用数据访问对象模块
var user = DB('user');              //操作user集合
//插入数据
user.insert({name: 'xiaobai', nick: 'small white'}, function (docs) {
    console.log(docs);      //[{name:'xiaobai',nick:'small white',_id:xxxxxxx}]     _id为数据项的索引值
});
//查找
user.find({name:'xiaobai'},function(doc){
    console.log(doc);       //[{name:'xiaobai',nick:'small white',_id:xxxxxxx}]
});
//更新操作
user.update({name:'xiaobai'},{name:'xiaobai',nick:'small'},function(len){
    console.log(len);       //1
});
//删除操作
user.remove({name:'xiaobai'},function(len){
    console.log(len);           //1         (删除数据的长度)
});
```

# 应用场景
        对前端存储的管理，统一使用方式。封装数据库操作，解耦数据库层与业务逻辑层。 