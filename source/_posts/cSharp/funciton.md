---
title: c#的方法
date: 2016-11-29 17:32:48
categories: C#
tags: c#基础
---
>c#语言中的函数或者方法调用。

<!--more-->
# 方法定义
```c#
    public [static] void func(int param){
        //函数体
    }
    注：
        命名一般以大写开头，参数名小写开头且有意义。
        静态方法可以直接调用或类名.方法名的方式调用。
        可以有返回值也可以没有。
        return可以立即退出。
```
# 变量的作用域
    在方法定义的变量成为局部变量，起作用范围从定义开始，到其所在的大括号结束为止。
    如果想访问另一个方法中的变量可以使用参数或者返回值实现。

# 方法重载
```c#
    public void getA(string name){
        //执行体
    }
    //重载
    public void getA(int age){
        //执行体
    }
    注：
        构成函数重载的条件是参数类型不同或个数不同，与返回值无关（重写）。
```

# 参数说明
```c#
    //out
    public static getMsg(out string name,out int age){
        //批量返回不同类型的值
        name = "zhangsan";
        age = 12;
    }
    string name;
    int age;
    string msg = getMsg(out string name,int age);
    Console.WriteLine(name + " " + age);  //zhangsan 12

    //ref
    public static void add(double salary){
        salary += 1000;
    }
    double salary = 5000;
    Console.WriteLine(salary);  //6000

    //param
    public static sum sum(params int[] arr){
        int sum = 0;
        for(int i = 0; i < arr.length; i++){
            sum += arr[i];
        }
        return sum;
    }
    sum(2,10,30);   //42

    注：
        out -- 表示是内部为外部赋值，其一般用在函数需要有多个不同类型返回值的场所。
        ref -- 能够将一个变量带入一个方法中进行改变，改变完成后，再将改变后的值带出去。
        params -- 可变参数，将实例列表中跟可变参数数组类型一致的元素都当做数组的元素处理。
```

# 方法的递归
    自己调用自己，但是要有明确的终止条件。

