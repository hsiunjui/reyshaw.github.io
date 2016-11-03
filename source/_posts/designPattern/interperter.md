---
title: 解释器模式
date: 2016-11-03 15:53:26
tags: 行为型模式
categories: 设计模式
---
> 对于一种语言，给出其文法表示形式，并定义一种解释器，通过使用这种解释器来解释语言中定义的的句子。

<!--more-->
# Xpath解析器(模拟冒泡路径)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div></div>
<div id="container">
    <div>
        <div>
            <ul>
                <li><span id="span1"></span></li>
                <li><span id="span2"></span></li>
            </ul>
        </div>
    </div>
    <div>
        <ul>
            <li><span id="span6"></span></li>
            <li><span id="span7"></span></li>
        </ul>
    </div>
</div>
</body>
<script>
    var Interpreter = (function () {
        function getSublingName(node) {              //获取兄弟元素名称
            if (node.previousSibling) {                           //如果存在上一个兄弟元素，标签名不一定相同<p></p><span></span>
                var name = '',                                              //返回的上一个兄弟元素名称字符串(标签名)
                        count = 1,                                           //紧邻兄弟元素中相同名称元素的个数
                        nodeName = node.nodeName,         //原始字节名称
                        sibling = node.previousSibling;         //前一个兄弟元素
                while (sibling) {                                 //如果存在前一个兄弟元素
                    //如果节点为元素，并且节点类型与前一个兄弟元素类型相同，并且前一个兄弟元素名称存在（同级元素）
                    if (sibling.nodeType == 1 && sibling.nodeType === node.nodeType && sibling.nodeName) {
                        if (nodeName == sibling.nodeName) {         //如果节点名称和前一个兄弟元素名称相同
                            name += ++count;                                            //节点名称后面添加计数
                        } else {                                                         //如果节点名称和前一个兄弟元素不同
                            count = 1;                                                               //重置个数
                            name += '|' + sibling.nodeName.toUpperCase();   //追加新的节点名称
                        }
                    }
                    sibling = sibling.previousSibling;          //向前获取前一个兄弟元素
                }
                return name;
            } else {                                                        //不存在兄弟元素返回空字符串
                return '';
            }
        }
        return function (node, wrap) {            //目标节点和容器节点
            var path = [],                                          //路径数组
                    wrap = wrap || document;             //如果不存在容器节点，默认为document
            if (node === wrap) {                          //如果当前节点等于容器节点
                if (wrap.nodeType == 1) {                  //容器节点为元素
                    path.push(wrap.nodeName.toUpperCase());      //路径容器中输入容器节点名称
                }
                return path;                                        //返回路径数组结果
            }
            if (node.parentNode !== wrap) {        //如果当前节点的父节点不等于容器节点
                path = arguments.callee(node.parentNode, wrap);         //对当前节点的父节点执行遍历操作
            } else {                                              //如果当前节点的父节点与容器节点相等
                if (wrap.nodeType == 1) {                   //如果容器节点为元素
                    path.push(wrap.nodeName.toUpperCase());    //路径数组中输入容器节点名称
                }
            }
            var sublingNames = getSublingName(node);            //获取元素的兄弟元素名称统计
            if (node.nodeType == 1) {                       //如果节点是元素
                path.push(node.nodeName.toUpperCase() + sublingNames);   //输入当前节点元素名称及其前面兄弟元素名称统计
            }
            return path;            //返回最终路径数组结果
        }
    })();
    var path = Interpreter(document.getElementById('span7'));
    console.log(path.join('>'));            //  HTML>BODY|HEAD>DIV2>DIV2>UL>LI2>SPAN
</script>
</html>
```

# 上式递归示意
```javascript
 var path = Interpreter(document.getElementById('container'));
    console.log(path.join('>'));                                // HTML>BODY|HEAD>DIV2
    var _con = document.getElementById('container');
    var _boby = con.parentNode;
    var _html = _body.parentNode;
    (function(_con,document){                           //1.相当于当前目标是container,容器节点是document
        var path = [];
        //...中间步骤省略
        if(_con.parentNode !== document){           //1.1container的父节点不是document,于是
            //【第一次递归调用】
            (function(_body,document){               //2.相当于当前目标是container,容器节点是document
                var path = [];
                //...中间步骤省略
                if(_body.parentNode !== document){     //2.1body的父节点是html,于是
                    //【第二次递归调用】
                    (function (_html, document) {    //3.相当于当前目标是container,容器节点是document
                        var path = [];
                        //...中间步骤省略
                        if (_html.parentNode !== document) {      //到这里，条件成立，则会去执行else及后面的部分
                        } else {                                      //3.1执行else分支相应的操作
                        }
                        var sublingNames = getSublingName(_con);  //3.2继续执行逻辑
                        //push path...后面省略，
                        return path;                                                      //3.3 path被覆盖，返回出path
                    })()                                        //3.4 执行完成，释放空间
                    //【第二次递归调用结束】
                }
                                                                         //2.2拿到path 继续push
                var sublingNames = getSublingName(_con);
                //...后面省略
                return path;                                    //2.3再次返回path
            })()                                                     //2.4 递归完成，释放空间
            //【第一次递归调用结束】
        }
                                                                            //1.2 再次拿到path,继续push
        var sublingNames = getSublingName(_con);
        //...后面省略
        return path;                                                //1.3 最后返回path
    })()
    //其整个过程是目标元素向html递归调用函数,而有从html递归返回数据 
```
 
# 应用准则
        能否根据需求解析出一套完整的语法规则，不论语法规则简单或是复杂。 