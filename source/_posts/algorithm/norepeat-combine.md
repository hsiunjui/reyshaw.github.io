---
title: 三位非重复组合
date: 2016-11-04 15:14:33
tags: 运算
categories: 算法
---
>列举四重for循环实现四个数字的三位非重复组合的数

<!--more-->
# 示例代码(javascript)
```javascript
    var a = [1,2,3,4];
	var no = 1;
	console.log("满足条件的三位数是：");
	for(var i = 0; i < a.length; i++) {
		for(var j = 0; j <a.length; j++) {
			if(j == i) {
				continue;
			}
			for(var k = 0; k < a.length; k++) {
				if(k == i || k == j) {
					continue;
				}
				for(var h = 0; h < a.length; h ++) {
					if(h == i || h == j || h == k) {
						continue;
					}
					console.log("No" + no + ": " + (a[j]*100 + a[k] * 10 + a[h] + "  "));
					no++;
				}
			}
		}
	}
```
# 输出结果
        满足条件的三位数是：
        No1: 234  
        No2: 243  
        No3: 324  
        No4: 342  
        No5: 423  
        No6: 432  
        No7: 134  
        No8: 143  
        No9: 314  
        No10: 341  
        No11: 413  
        No12: 431  
        No13: 124  
        No14: 142  
        No15: 214  
        No16: 241  
        No17: 412  
        No18: 421  
        No19: 123  
        No20: 132  
        No21: 213  
        No22: 231  
        No23: 312  
        No24: 321
