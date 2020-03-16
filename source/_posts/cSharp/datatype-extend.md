---
title: c#的数据类型扩展
date: 2016-11-29 15:59:49
categories: C#
tags: c#基础
---
>c#语言中的常量、枚举、数组、结构体。

<!--more-->
# 常量
```c#
    const double PI = 3.1415
```

# 枚举
```c#
    public enum Gender{
        male=1,       //枚举类型和int是兼容的
        femail
    }
    Gender g = Gender.male;
    注：与字符串相比，它的最大好处是限定了变量的取值范围。
```

# 数组
```c#
    int[] nums = new int[3];
    int[] nums = {1,2,3};
    int[] nums = new int[3]{1,2,3};
    int[] nums = new int{1,2,3};
    注：类型一致，长度确定。
```


# 结构体
```c#
    public struct Person{
        public string Name;
        public int Age;
        ...
    }
    注：一次性声明多个不同类型的变量。
```
