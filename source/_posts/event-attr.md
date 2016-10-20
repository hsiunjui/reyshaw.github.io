---
title: 标签的事件属性
date: 2016-10-20 22:10:03
tags: 标签
categories: HTML
---
>HTML 的特性之一是可以使 HTML 事件触发浏览器中的行为。

<!--more-->
# 窗口事件
事件名称|事件说明|作用范围 
-------|-------|--------
onload|当文档被载入时执行脚本
onunload|当文档被卸载时执行脚本|body iframe frameset

# 表单元素事件
事件名称|事件说明|作用范围 
-------|-------|--------
onchange|当元素改变时执行脚本
onselect|当元素被选取时执行脚本
onfocus|当元素获得焦点时执行脚本
onblur|当元素失去焦点时执行脚本
onsubmit|当表单被提交时执行脚本
onreset|当表单被重置时执行脚本|仅在表单元素中有效

# 图像事件
事件名称|事件说明|作用范围 
-------|-------|--------
onabort|图片加载中断时执行脚本|img

# 键盘事件
事件名称|事件说明|作用范围 
-------|-------|--------
onkeydown|当键盘被按下时执行脚本
onkeypress|键盘被按下又松开时执行脚本
onkeyup|当键盘被松开时执行脚本|除html head meta title style script base frameset frame iframe param bdo br外

# 鼠标事件
事件名称|事件说明|作用范围 
-------|-------|--------
onclick|当鼠标被单击时执行脚本	
ondblclick|当鼠标被双击时执行脚本
onmousemove|当鼠标指针移动时执行脚本
onmouseover|鼠标指针悬停于某元素之上时
onmouseout|当鼠标指针移出某元素时
onmousedown|当鼠标按钮被按下时执行脚本
onmouseup|当鼠标按钮被松开时执行脚本|除html head meta title style script base frameset frame iframe param bdo br外

# 触屏事件
事件名称|事件说明|作用范围 
-------|-------|--------
ontouchstart|当屏幕被点击时触发
ontouchmove|在屏幕上移动时连续触发
ontouchend|当离开屏幕时触发|移动设备

# 其他不常用或非标准事件
事件名称|事件说明 
-------|--------
onscroll|浏览器滚动条滚动时所激发的事件
onresize|当浏览器窗口大小被改变时所激发的事件
onreadystatechange|当对象的初始值发生变化时所激发的事件
onerror|在产生异常时所激发的事件
ondrag|当对象被拖动时所激发的事件
ondragdrop|当一个外部对象被拖进当前窗口或框架中时所激发的事件
ondragstart|当对象开始拖动时所激发的事件(拖动对象上触发)
ondragover|当对象被拖到当前对象范围上时所激发的事件(目标对象上触发)
ondragenter|当对象拖动到当前对象范围中时所激发的事件(目标对象上触发)
ondragleave|当对象被拖出当前对象范围时所激发的事件(目标对象上触发)
ondragend|当拖动完成时所激发的事件(拖动对象上触发)
ondrop|在拖动时释放鼠标键时所激发的事件(目标对象上触发)
onbeforecopy|在当前页面被选择的内容将要复制到系统剪贴板前时所激发的事件
copy|在当前页中复制内容时所激发的事件
onbeforecut|当前页面中的内容将被剪切到系统剪贴板前时所激发的事件
oncut|在当前页中剪切内容时所激发的事件
onbeforepaste|从系统剪贴板粘贴内容到页面中时所激发的事件
onpaste|在当前窗口中粘贴内容时所激发的事件
onbeforeunload|卸载文档前所激发的事件（通常是当前页面内容要改变）
onbeforeeditfocus|当前元素将要进入编辑状态时所激发的事件
oncontextmenu|弹出右键菜单时所激发的事件
onselectstart|当前文档内容开始选择时所激发的事件
onpropertychange|当对象的属性发生变化时所激发的事件
onstop|按下浏览器的停止按钮时或正在下载的文档被中断时所激发的事件
onmove|当浏览器窗口移动时所激发的事件
onlosecapture|当元素失去鼠标移动形成的选择焦点时所激发的事件
ondataavailable|当数据接收完毕时所激发的事件
ondatasetcomplete|当数据源中的所有数据读取完毕时所激发的事件
oncellchange|在数据来源发生变化时所激发的事件
onbeforeupdate|更新数据前所激发的事件
ondatasetchanged|当数据在数据源中发生变化时所激发的事件
onerrorupdate|更新数据异常所激发的事件
onafterupdate|当数据源向对象传送完数据后所激发的事件
onrowexit|当数据源的数据将在发生变化时所激发的事件
onrowenter|当数据源的数据发生变化并且有新的有效数据产生时所激发的事件
onrowsinserted|当数据源将要插入新记录时所激发的事件
onrowsdelete|当数据记录被删除时所激发的事件
onstart|当Marquee开始显示内容时所激发的事件
onbounce|在Marquee中的内容移动到显示范围之外时所激发的事件
onfinish|当Marquee完成需要显示的内容之后所激发的事件
onbeforeprint|文档打印前所激发的事件
onafterprint|打印文档后所激发的事件
onfilterchange|当某个对象的滤镜效果产生变化时所激发的事件
onhelp|当使用者按下F1键或浏览器的帮助选项时所激发的事件



