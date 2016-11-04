---
title: 杨辉三角
date: 2016-11-04 14:22:35
tags: 经典
categories: 算法
---
>运用Java实现输入杨辉三角形。

<!--more-->
# 杨辉三角
- 杨辉三角由数字排列，可以将它看成一个数字表，其基本特征是两侧的数值均为1，其他位置的数值是其正上方的数值与左上角数值之和。本例实现输出包括10行内容的杨辉三角形。 

# 示例代码(Java)
```java
public class O17_YanghuiTest {
	public static void main(String[] args) {
		int triangle[][] = new int[10][];
		for (int i = 0; i < triangle.length; i++) {         //遍历第一层
			triangle[i] = new int[i+1];                     //初始化第二层数组的大小
			for(int j = 0; j <= i; j++){
				if(i == 0 || j == 0 || j == i) {
					triangle[i][j] = 1;
				}else{
					triangle[i][j] = triangle[i-1][j] + triangle[i-1][j-1];     //正上方的值 加上正上方前面的一个值
				}
				System.out.print(triangle[i][j] +"\t");     //输出一个值后打印一个制表位
			}
			System.out.println();                                //循环一层后换行
		}
	}
}
```
# 输出结果
    1	
    1	1	
    1	2	1	
    1	3	3	1	
    1	4	6	4	1	
    1	5	10	10	5	1	
    1	6	15	20	15	6	1	
    1	7	21	35	35	21	7	1	
    1	8	28	56	70	56	28	8	1	
    1	9	36	84	126	126	84	36	9	1 


# 其实正常显示应该是这样的
                                        1	
                                    1	   1	
                                1	   2     1	
                            1	   3	   3	 1	
                       1       4	    6     4     1	
                    1       5	   10	  10	 5	   1	
                1	    6	   15     20     15     6	 1	
            1	    7	  21	  35	  35    21	    7	1	
         1	   8	 28       56       70	   56   28	  8	  1	
     1	   9    36         84       126 	126   84	36 	9	1 