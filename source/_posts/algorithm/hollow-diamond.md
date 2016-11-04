---
title: 空心菱形
date: 2016-11-04 14:51:27
tags: 经典
categories: 算法
---
>在控制台打印一个空心的菱形。

<!--more-->
# 示例代码(Java)
```java
import java.util.Scanner;
public class O18_Diamond {
	public static void main(String[] args) {
		System.out.print("请输入菱形的一个size：");
		Scanner scan = new Scanner(System.in);
		int size = scan.nextInt();
		if(size % 2 == 0) {
			size++;												//计算棱形大小
		}
		for(int i = 0; i < size / 2 + 1; i++){
			for(int j = size/2+1; j > i + 1; j--) {
				System.out.print("  ");								//输出左上角空白
			}
			for(int j = 0; j < 2 * i + 1; j++){						
				if(j == 0 || j == 2 * i) {
					System.out.print("*  ");							//输出菱形上半边缘
				}else{
					System.out.print("  ");							//输出菱形上半部分空心
				}
			}
			System.out.println("");
		}
		for(int i = size / 2 + 1; i < size; i++) {
			for(int j = 0; j < i - size / 2; j++){
				System.out.print("  ");								//输出菱形左下角空白
			}
			for(int j = 0; j < 2 * size - 1 -2 * i; j++ ) {
				if(j == 0 || j == 2 * (size - i - 1)) {
					System.out.print("*  ");						//输出菱形下半部分边缘
				}else{
					System.out.print("  ");						//输出菱形下半部分空心
				}
			}
			System.out.println("");
		}
	}
}
```

# 输出结果
    请输入菱形的一个size：6
          *  
        *    *  
      *        *  
    *            *  
      *        *  
        *    *  
          *   
