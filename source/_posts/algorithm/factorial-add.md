---
title: 阶乘累加
date: 2016-11-04 14:17:31
tags: 运算
categories: 算法
---
>计算1+1/2!+1/3!+...+1/10!的值。

<!--more-->
# 示例代码(java)
```java
import java.math.BigDecimal;
public class O16_Factorial {
	public static void main(String[] args) {
		BigDecimal sum = new BigDecimal(0.0);
		BigDecimal factorial = new BigDecimal(1.0);
		int i = 1; 
		while( i <= 10){
			sum = sum.add(factorial);
			++i;
			factorial = factorial.multiply(new BigDecimal(1.0 / i));
		}
		System.out.println("1+1/2!+1/3!+...+1/10!的计算结果等于：\n"+ sum);
	}

} 
```

# 输出结果 
    1.7182818011463844680506197765458292782475282626660293735976508627
    699738897945464175058103891543535553529470422349589137676698119651
    382240869491132928305256625939425716726877506029677251404259975629
    396959621364789699075430677403979592771673716111122001234997396527
    4118377230841674432773079155521711580689725451520644128322601318359375 
