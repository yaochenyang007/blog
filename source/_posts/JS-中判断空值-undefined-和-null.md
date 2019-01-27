---
title: JS 中判断空值 undefined 和 null
date: 2018-11-22 14:18:58
tags: [javascript]
categories: js
---


### JS 中 undefined 和 null 判断


####  JS 中如何判断 undefined
以下是不正确的用法：

```
var exp = undefined;
if (exp == undefined)
{
    alert("undefined");
}
```

exp 为 null 时，也会得到与 undefined 相同的结果，虽然 null 和 undefined 不一样。注意：要同时判断 undefined 和 null 时可使用本法。

以下是不正确的用法：

```
var exp = undefined;
if (typeof(exp) == undefined)
{
    alert("undefined");
}
```

***以下是正确的用法：***
<!-- more -->
```
var exp = undefined;
if (typeof(exp) == "undefined")
{
    alert("undefined");
}
```

####  JS 中如何判断 null

以下是不正确的用法：

```
var exp = null; 
if (exp == null) 
{ 
alert(“is null”); 
}
```

exp 为 undefined 时，也会得到与 null 相同的结果，虽然 null 和 undefined 不一样。注意：要同时判断 null 和 undefined 时可使用本法。
```
var exp = null; 
if (!exp) 
{ 
alert(“is null”); 
}
```
如果 exp 为 undefined 或者数字零，也会得到与 null 相同的结果，虽然 null 和二者不一样。注意：要同时判断 null、undefined 和数字零时可使用本法。

```
var exp = null; 
if (typeof(exp) == “null”) 
{ 
alert(“is null”); 
}
```

为了向下兼容，exp 为 null 时，typeof 总返回 object。


```
var exp = null; 
if (isNull(exp)) 
{ 
alert(“is null”); 
}
```
JavaScript 中没有 isNull 这个函数。

***以下是正确的用法：***

```
var exp = null; 
if (!exp && typeof(exp)!=”undefined” && exp!=0) 
{ 
alert(“is null”); 
}　
```