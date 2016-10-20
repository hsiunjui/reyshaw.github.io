---
title: 标签的基本属性
date: 2016-10-20 21:15:40
tags: 标签
categories: HTML
---
>HTML标签可以拥有属性。属性提供了有关HTML元素的更多的信息,总是以名称/值对的形式出现。

<!--more-->
# 通用属性
属性名称|属性说明 
-------|--------
id|当前元素唯一标识符
class|css类，样式标识
style|当前元素内联样式
title|当前元素工具提示文本
tagIndex|定义当前元素的焦点顺序
lang|定义元素内文本的主要语言
hidden|布尔值，应用在不该显示的元素上
dir|设置文本方向
spellcheck|元素的文本是否有拼写检查
contextMenu|作为上下文菜单显示的menu元素的ID
contentEditable|元素内容是否可编辑
accessKey|键盘快捷键
draggable|拖动源元素上的一个布尔属性，用于拖放API
dropzone|放置目标元素上的一个属性，用于拖放API

# 下面这些元素只支持通用的属性
	head  title  noscript  br   hr  
    header  nav  footer  article  section   aside   div 
    ul   dl  dt   dd  tr  thead  tbody  tfoot 
    h1~h6  address  p  span  strong  small  i  em  s  b  mark  abbr
    code  pre  sup  sub  samp  kbd  bdo   bdi   var   cite   caption  var  dfn
    figcaption  figure  datalist  summary  hgroup  legend
    rt  rp  ruby

# 除通用属性外,还有自己的个别属性  
属性名称 | 属性说明 |适用标签 
-------|--------|-----------
href|超文本引用|a  area  base  link
target|规定在何处打开链接|a  area  base  link  form
src|规定资源的URL|img audio video source embed iframe script track input
width/height|规定宽度/高度|canvas embed iframe input img object video
type|指定类型|input button style script link source object embed menu area a command
label|规定更短版本的选项|option optgroup menu command track
media|为不同媒介类型规定不同样式|style source link area a
name|规定名称|input button textarea form select object param iframe fieldset map meta output keygen ins
disabled|规定是否是禁用状态|input button textarea select option optgroup fieldset command keygen
checked|规定是否被选中|input command
readonly/placeholder|规定是否是只读/预制提示文本|input textarea
form|规定隶属哪个表单|input button textarea select label fieldset progressoutput object meter keygen
value|指定值|input button option li param progress meter
cite|指定引用来源	|blockquote del ins q
required|必填项|input textarea select
alt|备用文本|img input area
autofocus|自动获取焦点|input button textarea select keygen
autocomplete|自动补全|input form
rel/hreflang|当前与链接文档间的关系/指定被链接文档的语言|link area a 
span|规定col元素应该横跨的列数|col colgroup
size|指定长度|input select 
sizes|被链接资源的尺寸|link
maxlength|规定最大长度|input textarea
charset|指定编码|meta script
min|指定最小值|input meter
max|指定最大值|input meter progress
selected|是否被选中option
action/enctype/method/ novalidate/accept-charset|表单提交路径/表单发送前对数据的编码/表单提交方式/规定不对输入框进行校验/服务器接收表单处理的字符集|form
formaction/formenctype/ formmethod/formnovalidate/ formtarget|form的action属性/enctype属性/method属性/novalidate属性/target属性|input button
datetime|元素中的日期/时间|time ins del
colspan/rowspan/headers|纵排合并的单元格/横排和平的单元格/规定表头与单元格相关联|th td
preload/autoplay/loop/controls|是否在页面载入后加载/时候自动播放/是否循环/是否显示控制面板|audio video
icon/radiogroup|定义命令图标/定义命令组|command
open|规定其可见性|details
manifest|文档缓存manifest的位置|html
srcdoc/sandbox/seamless|显示的页面的HTML内容/规定一系列额外限制/规定像文档的一部分|iframe
usemap/ismap|定义为客户端图像映射/定义为服务器端图形映射|img
for|绑定对象|label output
accept/dirname/list/pattern/step|规定文件上传类型/设置提交方向性控制/引用数据列表/正则表达式规则/设置步长|input
challenge/keytype|keygen的值在提交时询问/生成RSA密钥|keygen
http-equiv/content|把content关联到http头/http-equiv与name元信息|meta
low/high/optimum|被界定为低的值/被界定为高的值/界定最佳度量值|meter
data|对象使用资源URL|object
reversed/start|指定降序/指定有序列表开始点|ol
async/defer|规定异步执行脚本/延迟执行脚本|script
multiple|允许一个以上的值|input select
summary|对表格的描述|table
cols/rows/wrap|文本区内可见宽度/文本区内可见高度/文本区域换行模式|textarea
poster|下载中/播放前显示的图像|video
pubdate|指示发布日期|time
default/kind/srclang|规定默认轨道/轨道的文本类型/轨道语言|track