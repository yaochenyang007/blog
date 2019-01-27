---
title: flex布局
date: 2018-10-10 16:36:10
tags: [css]
categories: css
---
### flex布局
1.flex-direction:决定主轴的方向（即项目的排列方向）。

>row（默认值）：主轴为水平方向，起点在左端。
row-reverse：主轴为水平方向，起点在右端。
column：主轴为垂直方向，起点在上沿。
column-reverse：主轴为垂直方向，起点在下沿

2.flex-wrap:排序方式

>nowrap（默认）：不换行。
wrap 换行，第一行在上方。
wrap-reverse：换行，第一行在下方。

3.flex-flow:flex-direction||flex-wrap 默认row nowrap
<!--- more --->
4.justify-content:定义了项目在主轴上的对齐方式。
>flex-start（默认值）：左对齐
flex-end：右对齐
center： 居中
space-between：两端对齐，项目之间的间隔都相等。
space-around：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

5.align-items属性定义项目在交叉轴上如何对齐。
>flex-start：交叉轴的起点对齐。
flex-end：交叉轴的终点对齐。
center：交叉轴的中点对齐。
baseline: 项目的第一行文字的基线对齐。
stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

6.align-content定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。
>flex-start：与交叉轴的起点对齐。
flex-end：与交叉轴的终点对齐。
center：与交叉轴的中点对齐。
space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
stretch（默认值）：轴线占满整个交叉轴。
