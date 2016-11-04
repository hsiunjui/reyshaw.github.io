---
title: 字符串的加密与解密
date: 2016-11-04 14:13:31
tags: 加解密
categories: Java
---
>运用Java实现的输入字符的简单加密与解密。

<!--more-->
# 代码示例
```java
import java.util.Scanner;
public class O07_Coding {
	int xor = 1000;					//定义异或变量，可以是随机的数字，只要在范围之内
	private String code (String pwd) {
		char[] codeChar = pwd.toCharArray();             //转换为字符数组
		for (int i = 0 ; i < codeChar.length; i++) {
			codeChar[i] = (char)(codeChar[i] ^ xor);     //返回异或后的值
		}
		return new String(codeChar);                          //返回加密字符串
	}
	
	private String  unCode (String unpwd) {
		char[] unCodeChar = unpwd.toCharArray();
		for(int i = 0; i < unCodeChar.length; i++) {
			unCodeChar[i] = (char) (unCodeChar[i] ^ xor);
		}
		return new String(unCodeChar);                   //返回解密字符串
	}
	
	public static void main(String[] args) {
		System.out.print("请输入一个英文字符串進行加密:  ");
		Scanner scan = new Scanner(System.in);         		 //从控制台输入数据
		String pwd = scan.nextLine();                           		 //获取用户输入
		O07_Coding cd = new O07_Coding();
		String codeString = cd.code(pwd);
		System.out.println("加密后的字符串為：  "  + codeString);
		String uncodeString = cd.unCode(codeString);
		System.out.println("解密后的字符串為：  "  + uncodeString);
	}
}
```

# 控制台打印情况
    请输入一个英文字符串進行加密:  abc123456789
    加密后的字符串為：  ΉΊ΋ϙϚϛϜϝϞϟϐϑ
    解密后的字符串為：  abc123456789
