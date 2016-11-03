---
title: 组合模式
date: 2016-11-03 15:01:54
tags: 结构型模式
categories: 设计模式
---
>又称为部分-整体模式，将对象组合成树形结构以表示"部分整体"的层次结构。组合模式使得用户对单个对象和组合对象的使用具有一致性。

<!--more-->
# 新闻模块的组合方式
```javascript
/**
 * 新闻模块
 * 实现图标或标题/图片和标题/类别与标题等组合显示
 */
//虚拟新闻父类
var News = function () {
    this.children = [];                 //子组件容器
    this.element = null;             //当前组件元素
}
News.prototype = {
    constructor: News,
    init: function () {
        throw new Error('请重写init方法');
    },
    add: function () {
        throw new Error('请重写add方法');
    },
    getElement: function () {
        throw new Error('请重写getElement方法');
    }
}
/**寄生式继承方法**/
function inheritObject(o) {          //原型式继承
    function F() { }                                    //过渡函数
    F.prototype = o;                                 //过渡函数的原型经常父对象o
    return new F();                                  //返回过渡对象实例
}
function inheritPrototype(subClass, superClass) {     //最终继承方法
    var p = inheritObject(superClass.prototype);                    //复制父类原型副本
    p.constructor = subClass;                                                 //修正构造函数
    subClass.prototype = p;                                                   //设置子类原型
}
/**-----创建容器类-------**/
//顶层容器类       ---相当于是添加ul框
var Container = function (id, parent) {
    News.call(this);                        //继承News类
    this.id = id;                               //模块id
    this.parent = parent;                //模块父容器
    this.init();
}
inheritPrototype(Container, News);               //寄生式继承父类方法
Container.prototype.init = function () {                  //构建方法
    this.element = document.createElement('ul');         //创建ul作为当前组件
    this.element.id = this.id;                                          //为创建的组件赋id值
    this.element.className = 'new-container';            //为创建组件添加样式
}
Container.prototype.add = function (child) {       //添加子元素方法
    this.children.push(child);                                              //在子元素容器中插入子元素
    this.element.appendChild(child.getElement());           //插入当前组件元素树中
    return this;                                                                  //返回容器
}
Container.prototype.getElement = function () {
    return this.element;                                        //返回当前组件
}
Container.prototype.show = function () {           //显示方法
    this.parent.appendChild(this.element);
}
//下一层级的行成员  --相当于排布ul里面的li元素
var Item = function (classname) {
    News.call(this);
    this.classname = classname || '';                         //添加样式
    this.init();
}
inheritPrototype(Item, News);
Item.prototype.init = function () {
    this.element = document.createElement('li');         //创建li标签
    this.element.className = this.classname;            //添加样式
}
Item.prototype.add = function (child) {
    this.children.push(child);                                       //模型添加
    this.element.appendChild(child.getElement());     //视图添加
    return this;                                                            //返回当前组件
}
Item.prototype.getElement = function () {
    return this.element;
}
//新闻组合体   ---相当于排布div元素(即在li里面排布div)
var Newsgroup = function (classname) {
    News.call(this);
    this.classname = classname || '';
    this.init();
}
inheritPrototype(Newsgroup, News);
Newsgroup.prototype.init = function () {
    this.element.createElement('div');                  //创建div容器
    this.element.className = this.classname;    //添加样式
}
Newsgroup.prototype.add = function (child) {
    this.element.push(child);
    this.element.appendChild(child.getElement());
    return this;
}
Newsgroup.prototype.getElement = function () {
    return this.element;
}
/**----创建新闻类，该类成员不能拥有子成员---**/
//图片新闻类   --a标签内插入了一个图片
var ImageNews = function (url, href, classname) {
    News.call(this);
    this.url = url || '';                                   //图片src属性的路径
    this.href = href || '#';                            //a标签的链接
    this.classname = classname || 'normal';     //class样式
    this.init();
}
inheritPrototype(ImageNews,News);
ImageNews.prototype.init = function () {
    this.element = document.createElement('a');             //创建a标签
    var img = new Image();                                              //创建图像
    img.src = url;
    this.element.appendChild(img);                                                //把图像添加到a标签内
    this.element.className = 'image-news ' + this.classname;    //设置样式
    this.element.href = href;
}
ImageNews.prototype.add = function(){
    //由于是最终基类，不能添加子成员
}
ImageNews.prototype.getElement = function(){
    return this.element;
}
//图标类   --icon图标加标题形式
var IconNews = function(text,href,type){
    News.call(this);
    this.text = text || '';                           //标题
    this.href = href || '#';                        //icon链接
    this.type = type || 'video';                //icon类型样式
    this.init();
}
inheritPrototype(IconNews,News);
IconNews.prototype.init = function(){
    this.element = document.createElement('a');
    this.element.innerHTML = this.text;
    this.element.href = this.href;
    this.element.className = 'icon '+this.type;
}
IconNews.prototype.add = function(){
    //由于是最终基类，不能添加子成员
}
IconNews.prototype.getElement = function(){
    return this.element;
}
//纯文字新闻类
var EasyNews = function(text,href){
    News.call(this);
    this.text = text || '';               //标题
    this.href = href || '#';            //链接
    this.init();
}
inheritPrototype(EasyNews,News);
EasyNews.prototype.init = function(){
    this.element.createElement('a');
    this.element.innerHTML = this.text;
    this.element.href = this.href;
    this.element.className = 'text';
}
EasyNews.prototype.add = function(){
    //由于是最终基类，不能添加子成员
}
EasyNews.prototype.getElement = function(){
    return this.element;
}
//分类新闻类         --有分类和标题的
var TypeNews = function(text,href,type,pos){
    News.call(this);
    this.text = text || '';               //标题
    this.href = href || '#';            //链接
    this.type = type || '';            //分类
    this.pos = pos || 'left';         //分类的位置
}
inheritPrototype(TypeNews,News);
TypeNews.prototype.init = function(){
    this.element = document.createElement('a');
    if(this.pos === 'left'){
        this.element.innerHTML = '['+this.type+'] '+this.text;
    }else{
        this.element.innerHTML = this.text + ' ['+this.type+']';
    }
    this.element.href = this.href;
    this.element.className = 'text';
}
TypeNews.prototype.init = function(){
    //由于是最终基类，不能添加子成员
}
TypeNews.prototype.getElement = function(){
    return this.element;
}
//创建板块
var news1 = new Container('news',document.body);
new1.add(
    new Item('normal1')                         //添加容器
        .add(
            new IconNews('梅西不拿金球也伟大','#',video)             //添加内容
        )
)
.add(new Item('normal').add(new IconNews('保护强国强队用意明显','#','live')))
.add(new Item('normal').add(new ImageNews('img/1.jpg','#','small'))
                       .add(new EasyNews('从240斤胖子成功变形男','#'))
                       .add(new EasyNews('五大雷人跑步机','#')))
.add(new Item('normal').add(new TypeNews('AK47不愿为费城打球','#','NBA','left')))
.add(new Item('normal').add(new TypeNews('火炮彪6三分创新高','#','CBA','right')))
.show();
```
# 实现原理
     抽象一个虚拟父类，类中有两个属性，子成员的集合及自身，类中有三个方法初始化，
     添加和提供对外访问接口。所有的容器（ul,li,div）抽象为容器类，主要用于添加具体的新闻类。
     所有的新闻条目(带图片，带图标，带分类，横排组合的)抽象为具体新闻基类，以实现不同的组合。
     最后通过添加容器，容器添加新闻条目及组合的面向对象的方式实现。

# 优点
        可以实现不同类型的组合及显示方式的变换，提供了一个清晰的组成结构，
        模糊了简单的对象与组合对象的区别，便于对数据的管理和使用维护。

# 缺点
       代码量较大，对于资源有一定的开销，要根据具体情况决定是否使用。

# 其他适用场景
        表单创建过程中，对各个容器及控件的组合使用。 
