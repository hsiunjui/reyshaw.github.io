---
title: 移动端缩放适配
date: 2016-11-04 13:50:17
tags: 适配
categories: 大前端
---
>移动端适应多分辨率屏幕。

<!--more-->
# 设计需求
- ps设计稿定义为640*1136px (也可以是其他比例)

# 技术步骤
    1. 新建html文档，手机的每一屏定义一个div,设置其class属性为page.
    2. 在css中设置好每一屏的属性。
       .page{width:640px; position:absolute;},有需要也可以设置一下overflow属性。
       如果是其他尺寸的ps设计稿，只需要将宽度改为设计的宽度即可。
    3. 在js文件中，动态设置.page的高度。
       $(".page").height(window.innerHeight*640/window.innerWidth);
       这里的值其实便是设计稿的高度(1136px);
       其实现原理是，window.innerWidht/window.innerHeight =640/1136,是一个等比例关系；
       若是其他比例换掉宽度。
    4. 再在js中设定body的缩放，$("body").css("zoom", window.innerWidth/640).
       即以等比例缩放ps的设计稿。需要注意，zoom是ie的专有属性，新版chrome也支持，
       但是火狐浏览器不支持。这里可以试试css3的tranform:scale属性。其他比例照换。

# 注意事项
- 这样便实现了页面的整体缩放。在.page的div里面的内容可以完全按照ps中的尺寸和位置来布置。需要注意的是在设置背景和内容位置的时候需要考虑一下是顶部对其还是底部对其的问题。

# 局限
- 该方案并不是很完善，在一较小尺寸的手机屏幕上可能会出现问题。在iPhone5+以上的屏幕中运行时非常完美的。

# 总结
- 该方案的原理是把手机的每一屏大小设置为设计稿的尺寸，然后再通过等比例缩放而让其自适应的。当然，因为设置的是body的缩放属性，其内部元素也会由于继承关系而等比例缩放。
    