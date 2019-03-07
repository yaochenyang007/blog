---
title: 前端面试分享css篇
date: 2019-02-28 17:29:40
tags: [css]
categories: 面试
---
###   解释BFC概念，重绘和回流？

###   CSS实现三角形
利用border属性绘制三角形
```
 <div class="kailong"></div>
.kailong{
	width:0;
        height:0;
	border-right:50px solid transparent;
	border-left:50px solid transparent;
	border-bottom:50px solid red;
}
```
<!--- more --->
###  移动端rem布局如何实现? 简述原理? 

###  响应式布局的方法，并详细解释

###  box-sizing的属性值及区别

###  css3实现动画

###  移动端调试的方法

###  移动端和PC和微信兼容性问题

###  画一条0.5px的直线
兼容
transfrom属性画0.5
```           
height: 1px;
transform: scaleY(0.5);
transform-origin: 50% 100%;
```
SVG画0.5
还可以使用SVG，利用SVG的描边等属性的1px还是物理像素的1px，而不是高清屏的1px。
```   
.hr.svg {
    background: none;
    height: 1px;
    background: url("data:image/svg+xml;utf-8,<svg xmlns='http://www.w3.org/2000/svg' width='100%' height='1px'><line x1='0' y1='0' x2='100%' y2='0' stroke='#000'></line></svg>")
}
<div class="hr svg"></div>
```  

###  伪类和伪元素的对比，应用
CSS3为了区别伪类和伪元素，伪类用: 伪元素用:: 分别来表示
伪类
伪类选择元素基于的是当前元素处于的状态，或者说元素当前所具有的特性，而不是元素的id、class、属性等静态的属性。由于状态是动态变化的，所以一个元素达到一个特定状态时，它可能得到一个伪类的样式；当状态改变时，它又会失去这个样式。由此可以看出，它的功能和class有些类似，但它是基于文档之外的抽象，所以叫伪类。
例如：

:link :visited :hover :active
:root :not(selector) :valid :invalid
:required :optional 选择没有required属性的元素
:in-range :out-of-range
:checked :disabled :enabled
:empty 选择没有子元素的元素
:first-of-type :last-of-type :nth-of-type(n) :nth-last-of-type(n) :only-of-type 同一级中的某一个
:last-child :nth-child(n) :nth-last-child(n) :first-child :only-child 是父元素中唯一一个子元素
:target 当前目标元素
伪元素
类似于行内块级元素，伪元素是对元素中的特定内容进行操作，它所操作的层次比伪类更深了一层，也因此它的动态性比伪类要低得多。设计伪元素的目的就是去选取诸如元素内容第一个字（母）、第一行，选取某些内容前面或后面这种普通的选择器无法完成的工作。它控制的内容实际上和元素是相同的，但是它本身只是基于元素的抽象，并不存在于文档中，所以叫伪元素。
例如：
::selection 用户选中高亮的部分
 ::backdrop 全屏模式设置下层文档样式 
 ::first-line ::first-letter 
 ::before ::after
#### :after/::after和:before/::before的异同
相同点
都可以用来表示伪类对象，用来设置对象前的内容
:before和::before写法是等效的; :after和::after写法是等效的

不同点
:before/:after是Css2的写法，::before/::after是Css3的写法
所以css2的要比css3的兼容好    ,,  :before/:after 的兼容性要比::before/::after好 ， 
不过在H5开发中建议使用::before/::after比较好


### 是否了解viewport

viewport的大小决定了，css中的设置多少像素能刚好占满屏幕。例如，viewport=320,那么设置div的宽度为320px,则div刚好能占满屏幕。

1.移动设备中1px不等于1个物理像素

现如今，移动设备多已经采用高倍屏，像素分辨率（物理分辨率）要比逻辑分辨率高，下表为iphone分辨率数据。

型号|像素分辨率|逻辑分辨率|倍数
:-|:-:|  -: | -:
iphone5|640*1136|320*568|2
iphone6|750*1334|375*667|2
iphone6 plus|1242*2208|414*736|3

2.利用meta标签对viewport进行控制。
```  
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
```  
该meta标签的作用是让当前viewport的宽度等于设备的宽度，同时不允许用户手动缩放。也许允不允许用户缩放不同的网站有不同的要求，但让viewport的宽度等于设备的宽度，这个应该是大家都想要的效果，如果你不这样的设定的话，那就会使用那个比屏幕宽的默认viewport，也就是说会出现横向滚动条。


|width|设置layout viewport  的宽度，为一个正整数，或字符串"width-device"|
| --------   |:-----:| 
|initial-scale|设置页面的初始缩放值，为一个数字，可以带小数|
|minimum-scale|允许用户的最小缩放值，为一个数字，可以带小数|
|maximum-scale|允许用户的最大缩放值，为一个数字，可以带小数|
|height|设置layout viewport  的高度，这个属性对我们并不重要，很少使用|
|user-scalable|是否允许用户进行缩放，值为"no"或"yes", no 代表不允许，yes代表允许|

