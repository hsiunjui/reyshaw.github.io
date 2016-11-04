---
title: 焦点图切换
date: 2016-11-04 15:19:59
tags: 特效
categories: 案例
---
>原生javascript实现链式操作，左右切换焦点图。

<!--more-->
# HTML部分
```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, user-scalable=no,initial-scale=1" >
        <title>Banner Test</title>
    </head>
    <body>
        <div id="container">
            <div class="pre"></div>
            <div class="next"></div>
            <div class="stitle">
                <ul>
                    <li class="ttitle"></li>
                    <li></li>
                    <li></li>
                    <li></li>
                </ul>
            </div>
            <ul class="banner">
                <li><div style="width:640px; height:300px; background:red;"></div></li>   <!--若是图片直接替换li里面的内容-->
                <li><div style="width:640px; height:300px; background:green;"></div></li>
                <li><div style="width:640px; height:300px; background:blue;"></div></li>
                <li><div style="width:640px; height:300px; background:orange;"></div></li>
            </ul>
        </div>            
    </body>
</html>

```
# CSS部分
```css
*{margin:0; padding:0; list-style-type:none;}
#container{width:640px; height:300px;overflow:hidden; margin:20px auto; background:#333; position:relative;}
#container .banner{width:99999px; position:absolute; left:0; top:0;}
#container .banner li{float:left;}
#container .pre{cursor:pointer; width:45px; height:45px;background:url(Content/images/l.png); position:absolute; left:10px; top:130px; z-index:1;}
#container .next{cursor:pointer; width:45px; height:45px;background:url(Content/images/r.png); position:absolute; right:10px; top:130px; z-index:1;}
#container .stitle{width:640px; height:10px; position:absolute; bottom:15px; z-index:1;}
#container .stitle ul{width:120px; margin:0 auto;z-index:2;}
#container .stitle ul li{width:10px; height:10px; margin:0 5px; float:left; background:#FFF; border-radius:10px; overflow:hidden; *display:inline; *zoom:1;}
#container .stitle ul li.ttitle{background:#69aaec;}
```

# JavaScript部分
```javascript
        function getStyle(obj,name){
            if(obj.currentStyle){                        //IE  obj是载体，name是属性名 如 obj.currentStyle['width']  ===>  obj.style.width
                return obj.currentStyle[name];
            }else{                                        //其他　FF等
                return getComputedStyle(obj,false)[name];
            }
        }        
        function startMove(obj,att,add){                    //链式运动        
            clearInterval(obj.timer);
            obj.timer = setInterval(function(){
                var cutt = 0;
                if(att=="opacity"){
                    cutt = Math.round(parseFloat(getStyle(obj,att)));  //取opacity值，本程序未用到
                }else{
                    cutt = Math.round(parseInt(getStyle(obj,att)));   //其他属性值
                }
                var speed = (add-cutt)/4;                                //可以改变除数，以变更速度
                speed = speed>0?Math.ceil(speed):Math.floor(speed);
                if(cutt == add){
                    clearInterval(obj.timer);
                }else{
                    if(att =="opacity"){
                        obj.style.opacity = (cutt + speed)/100;
                        obj.style.filter = 'alpha(opacity:'+(cutt+speed)+')';
                    }else{
                        obj.style[att] = (cutt+speed)+"px";        //位移视图显示
                    }
                }
            },30);
        }
        window.onload=function(){
            var container = document.getElementById("container");          //容器
            var pre = document.getElementsByClassName("pre")[0];        //左箭头
            var next = document.getElementsByClassName("next")[0];        //右箭头
            var banner = document.getElementsByClassName("banner")[0];  //banner切换区ul
            var bannerLi = banner.getElementsByTagName("li");             //banner切换去ul li
            var stitle = document.getElementsByClassName("stitle")[0];  //圆点 div
            var stitleLi = stitle.getElementsByTagName("li");            //圆点div ul li
            var now = 0;
            
            function tab(){
                for(var i = 0; i<stitleLi.length; i++){                //下方小圆点切换
                    stitleLi[i].className = "";
                }
                stitleLi[now].className = "ttitle";
                startMove(banner,'left',-now*bannerLi[0].offsetWidth);    //调用banner切换函数
            }
        
            for(var j = 0; j<stitleLi.length; j++){
                stitleLi[j].index = j;                        //给属性赋值
                stitleLi[j].onclick=function(){                //小圆点点击事件
                    now = this.index;                       //获取now值
                    tab();                                    //样式切换
                }
            }
            pre.onclick=function(){                       //左箭头事件
                now--;
                if(now==-1){
                    now=bannerLi.length-1;
                }
                tab();
            }    
            next.onclick=function(){                        //右箭头事件
                now++;
                if(now==bannerLi.length){
                    now=0;
                } 
                tab();
            }
            var timer = setInterval(next.onclick, 2000);    //定时右箭头事件
            container.onmouseover = function(){                //鼠标移入清除定时器
                clearInterval(timer);
            }
            container.onmouseout = function(){                //鼠标移除重新设置定时器
                timer = setInterval(next.onclick,2000);
            }
        }
```