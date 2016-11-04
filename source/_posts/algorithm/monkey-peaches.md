---
title: 猴子分桃
date: 2016-11-04 14:56:07
tags: 经典
categories: 算法
---
>一半的一半的一半的问题。

<!--more-->
# 问题描述
- 海滩上有一堆桃子，五只猴子来分。第一只猴子把这堆桃子分成了五份，多了一个，这只猴子吧多的一个扔入海中，拿走了一份。第二只猴子把剩下的桃子又平均分成五份，又多了一个，它同样把多的一个扔入海中，拿走了一份。第三、第四、第五只猴子都是这样做的，问海滩上原来最少有多少个桃子，此时第五只猴子得到多少桃子？

# 示例代码(Java) 
```java
public class O20_MonkeyPeach {
    public static void main(String[] args) {
        int n = 1;
        int m = 0;
        int flag = 1;
        int  monkeyNum = 5;
        while(true){
            flag = 1;
            m = monkeyNum*n + 1;
            for(int i = monkeyNum; i >= 1; i--) {
                if(m%(monkeyNum-1) ==0) {
                    m = m/(monkeyNum -1) * monkeyNum + 1;
                    flag++;							//計算多少次的桃子數量被4整除
                }else{
                    break;
                }
            }
                if(flag == monkeyNum){                                 //每次的桃子數量都能被4整除
                    break;
                }
                n++;
        }
        System.out.println("開始的桃子數至少是：" + m);
        System.out.print("此時第五個猴子得到的桃子數是：" + n);
    }
}
```
# 输出结果
    開始的桃子數至少是：3121
    此時第五個猴子得到的桃子數是：255 