---
title: 外边距合并
date: 2016-11-01 15:30:07
tags: CSS浏览器差异
categories: CSS
---
>外边距合并是指两个或多个盒子(可能相邻也可能嵌套)的相邻边界(其间没有任何非空内容、补白、边框)重合在一起而形成一个单一边界。

<!--more-->
# 出现情况
	两个或多个块级盒子的垂直相邻边界会重合。 

# 计算原则
- 全部为正值,结果的边界宽度是相邻边界宽度中最大的值。
- 如果出现负边界，即不全是正值，则在最大的正边界中减去绝对值最大的负边界(都取绝对值)。
- 如果没有正边界，则从零中减去绝对值最大的负边界(都取绝对值)。

# 重叠的例外情况
- 水平边界不会重合。
-  相邻的盒模型中，如果其中一个是浮动的(float),垂直margin不会重叠，并且浮动的盒模型和它的子元素之间也是这样。
- 设置了overflow属性(非visible值)的元素和它的子元素之间margin不被重叠。
-  设置了绝对定位的盒模型，垂直margin不会被重叠，并且和他们的子元素之间也是一样。
-  设置了display:inline-block的元素垂直margin不会重叠，且和子元素之间也是一样。
-  根元素的垂直margin不会被重叠。
- 一个应用了清除操作的元素的margin-top不会和它的块级父元素的margin-bottom重叠。

# 解决方案
- 外层元素padding代替。
- 内层元素透明边框：border:1px solid transparent。
- 内层元素绝对定位：position:absolute。
-  外层元素：overflow:hidden。
- 内层元素浮动：float:left/right。
- 内层元素：display:inline-block。
- 内层元素：padding:1px。 
