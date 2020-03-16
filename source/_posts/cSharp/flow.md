---
title: c#的流程控制
date: 2016-11-29 16:25:35
categories: C#
tags: c#基础
---
>c#语言中的分支及循环。

<!--more-->
# 顺序结构
```c#
    //执行a
    //执行b
```

# 单分支
```c#
    if(bool){
        //条件成立执行
    }else{
        //条件不成立执行
    }
    注：在多重嵌套的结构中，else是跟着最近的那个if
```

# 多分支
```c#
    if(bool){

    }else if(){

    }else if(){

    }
```

# 选择结构
```c#
    switch(str){
        case a:
        case b:
            break;
        default:
    }
    注：str可以是表达式或者数值，case的逻辑可以有判断或者函数调用。
```

# while循环
```c#
    while(bool){
        //循环体
    }
```

# do...while循环
```c#
    do{
       //循环体
    }wihle(bool);
    注：与while循环相比，这里的逻辑至少要执行一次
```


# for循环
```c#
    for(int i = 0; i < 3; i++){
        //执行循环体
    }
    注:循环可以用break终止循环，也可以用continue进行下一轮循环
```

# 异常捕获
```c#
    try{
        //捕获的代码
    }catch{
        //发生异常执行该逻辑
    }
```
