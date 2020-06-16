---
title: JVM简介
date: 2020-06-16 14:50:15
tags: Java SE
categories: Java
---
>JVM的架构,GC.

<!--more-->
# JVM
Java Virtial Merchine。
- SUN HotSpot (主流, Oracle所有)
- BEA JRockit (被Oracle收购)
- IBM J9

# JVM内存结构图
```
        class 文件  --> Class Loader ||
--------------------------------------V---------------------
|        Runtime Data Area(运行时数据区)                    |
|                                                           |
|   @@@@@@@@@@@@@@@   ############  ######################  |
|   @ Method Area @   #  Stack   #  # Native Mthod Stack #  |
|   @ (方法区)    @   # (Java栈) #  #    (本地方法栈)    #  |
|   @@@@@@@@@@@@@@@   ############  ######################  |
|                                                           |
|   @@@@@@@@@@@@@@@   ####################################  |
|   @    Heap     @   #   Program Counter Register       #  |
|   @    (堆)     @   #        (程序计数器)              #  |
|   @@@@@@@@@@@@@@@   ####################################  |
|                                                           |
-------------------------------------------------------------
    ||                  ||                 ||
excution engine -> Native Interface -->Native Libraires
    (执行引擎)      (本地接口)          (本地接口)
```
- class文件通过class Loader加载到内存空间,然后交给执行引擎执行。
  + Bootstrap ClassLoader (顶层加载类,c++)
  + Extention ClassLoader (扩展的类加载器, \lib\ext)
  + Appclass Loader(加载当前应用的classpath的所有类)
  + User ClassLoader (用户自定义加载器)
- 本地方法栈通过本地接口再调用本地方法
- ##区域不会发生垃圾回收,线程私有
- @@区域线程间共享

## 运行时五大功能区(核心)
- 方法区 (永久代/元数据处于该区)
  + 类信息
  + 常量
  + 静态变量
  + 常量池
- 堆(-Xms=1/64 -Xmx=1/4)
  + 年轻代 (Young Generation 8:1:1)
    * 伊甸区 (Eden)
        - 刚new出来的对象都会放这里, Minor GC存活的会进入Survivor区
    * 幸存1区 (Survivor from)
        - from区和to区会经常发生转换
        - 经历过15次Minor GC后会进入老年代
    * 幸存2区 (Servivor to)
  + 老年代 (Old Generation)
    - 该区域会触发Full GC
  + 永久代/元数据(Permernent Generation/MetaData ,位于方法区)
    - 存活时间长,同jvm。如连接池,线程池等
- Java栈
  + 局部变量(对象引用/8种基本数据类型)
  + 操作数栈
  + 动态链接
  + 方法出口
- 本地方法栈
  + 为虚拟机执行native方法服务
- 程序计数器
  + 当前线程所执行的字节码的行号显示器,它会指出下一条指令的执行地址。
  + 每条线程都有一个独立的程序计数器
  + 唯一一个不会出现OutOfMemoryError的内存区域


## OOM
- java.lang.OutOfMemoryError:Java heap space 
    * 内存泄漏,不能被回收的对象消耗太多内存(死循环new 类)
- java.lang.OutOfMemoryError:Permgen space
- java.lang.OutOfMemoryError:Metaspace 
   * 加载类太多(依赖太多的jar包)
  
## GC
- 引用计数法(淘汰)
  * 给对象添加一个引用计数器,为0是回收
    + 影响性能
    + 对循环引用无法回收
- 复制算法(适用于年轻代)
  * 从一块内存空间复制到另一块(如从from区到to区)
    + 效率高
    + 占用空间
- 标记-清除法
  * 遍历,第一遍标记哪些是存活的,第二遍清除未标记的
    + 可以节省空间
    + 应用程序会停止,且需遍历两遍,效率低
    + 空闲内存不连续
- 标记-压缩法
  * 同标记-清除法一样,第一遍标记,第二遍是整理,即把存活的对象压缩到一块区域,然后清理末端。
    + 比起标记-清除法,它的空间是连续的。

> 注意：GC采用分代收集算法。年轻代触发的是Minor GC(轻量级GC),且采用复制算法; 老年代触发的Full GC,采用的标记-清除/标记-整理算法。

# 参考
- [深入理解 JVM 之 JVM 内存结构](https://juejin.im/post/5c73c6446fb9a04a0d577d14)
- [Java虚拟机详解04----GC算法和种类【重要】](https://www.cnblogs.com/qianguyihao/p/4744233.html)
  

# 创建及修改时间
> 创建时间: 2020-06-16 14:50:15  