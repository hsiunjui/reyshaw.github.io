---
title: Css选择器
date: 2016-11-01 15:14:16
tags: CSS基础
categories: CSS
---
>选取某些特定元素及其组合的规则

<!--more-->
# 基础选择器
选择器名称|选择器说明
-------|--------
*|通配符,匹配任何元素
E|标签选择器
#info/E#info|id选择器
.info/E.info|class选择器

# 组合选择器
选择器名称|选择器说明
-------|--------
E,F|多元素选择器
E F|后代选择器
E>F|子元素选择器
E+F|毗邻元素选择器

# CSS2.1属性选择器
选择器名称|选择器说明
-------|--------
E[attr]|匹配有attr属性的元素
E[attr=val]|匹配attr=val的元素
E[attr~=val]|属性中一个值为val的元素
E[attr\=val]|用于lang的一以val开头的元素

# CSS2.1中的伪类
选择器名称|选择器说明
-------|--------
E:first-child|父元素的第一个子元素
E:link|所有未被点击的元素
E:visited|所有已被点击的元素
E:active|鼠标按下未释放的元素
E:hover|鼠标悬停其其上的元素
E:focus|获得当前焦点的元素
E:lang(c)|匹配lang属性等于c的元素

# CSS2.1中的伪元素
选择器名称|选择器说明
-------|--------
E:first-line|匹配元素的第一行
E:first-letter|匹配元素的第一个字母
E:before|在元素之前插入生成内容
E:after|在元素之后插入生成内容

# CSS3同级元素通用选择器
选择器名称|选择器说明
-------|--------
E~F|任意在E之后的同级F元素

# CSS3属性选择器
选择器名称|选择器说明
-------|--------
E[attr^='val']|属性以val开头的
E[attr$='val']|属性以val结尾的
E[attr*='val']|属性包含val的元素

# CSS3中与用户界面有关的伪类
选择器名称|选择器说明
-------|--------
E:enabled|表单中激活的元素
E:disabled|表单中禁用的元素
E:checked|被选中的单选或复选项
E:selection|用户当前选中的元素

# CSS3中的结构性伪类
选择器名称|选择器说明
-------|--------
E:root|文档根元素
E:nth-child(n)|父元素第n个子元素,1为基数
E:nth-last-child(n)|父元素倒数第n个子元素
E:nth-of-type(n)|匹配同种标签的nth-child(n)
E:nth-last-of-type(n)|匹配同种标签的nth-last-child(n)
E:last-child|匹配父元素的最后一个子元素，等同于nth-last-child()
E:first-of-type|父元素下使用同种标签的第一个子元素,等同于nth-of-type(1)
E:last-of-type|父元素下使用同种标签的最后一个子元素,等同于nth-last-of-type(1)
E:only-child|匹配父元素仅有的一个子元素
E:only-of-type|父元素下使用同种标签的唯一一个子元素
E:empty|匹配一个不包含任何子元素的元素

# CSS3的反选伪类
选择器名称|选择器说明
-------|--------
E:not(s)|匹配不符合当前选择器的元素

# CSS3中的:target伪类
选择器名称|选择器说明
-------|--------
E:target|文档中特定id点击后的效果


