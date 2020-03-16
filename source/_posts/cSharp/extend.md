---
title: c#的继承
date: 2016-12-2 09:26:45
categories: C#
tags: c#高级
---
>c#中父类、子类、继承等相关概念。

<!--more-->
# 父类
```c#
    public class Person {    //封装重复的基本属性和方法作为父类
        private string name;
        public int _age;
        
        public int Age{
            get{
                return _age;
            }
            set{
                _age = value;
            }
        }
        
    }
```

# 继承
```c#
    public class Student : Person{
        private string stuNo;   //子类特有
    }
    //子类继承了父类的属性和方法，但子类不会继承父类的私有字段。
    //子类没有继承父类的构造器，但子类会默认调用父类的无参构造器。
    //如果父类重写了构造器，必须显式保留无参构造器或者在子类中显式调用base();
    //所有的类都直接或间接继承自object类。c#中只支持单继承。
```

# 继承的特性
    1.单根性，一个子类只能有一个父类
    2.传递性

# 父子类转换
    1.子类可以赋值给父类,子类可以隐式转换为父类。
    2.如果父类中有子类的对象，可以将父类强转成子类对象。
    注：子类对象可以调用父类中的成员，但父类对象仅能调用自己的成员。
        is表示类型转换，成功为true,失败为false.
        as也表示类型转换，成功返回对应对象，失败返回null