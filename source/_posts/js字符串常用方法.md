---
title: JS字符串常用方法
date: 2018-11-22 14:42:50
tags: [javascript]
categories: js
---
### JS字符串常用方法

#### concat()  :连接字符串


![本地图片](/img/concat.jpg  "连接字符串")


#### chatAt()  :返回指定索引位置的字符，若索引越界，返回空字符串

![本地图片](/img/chatAt.jpg  "chatAt")

#### substr(fromIndex,length)获取部分字符串:
##### 从起始索引fromIndex开始截取长度length的字符串

![本地图片](/img/substr.jpg  "substr")

#### indexOf(),lastindexOf()：找到匹配项返回索引值（一定是绝对位置），如果没找到返回-1

![本地图片](/img/indexOf.jpg  "indexOf")

#### substring(startIndex,endIndex):获取部分字符串

##### 截取 起始索引startIndex  到  结束索引endIndex的子字符串， 结果包含startIndex处的字符，不包含endIndex处的字符。
  
![本地图片](/img/substring.jpg  "substring")

#### slice(startIndex,endIndex):获取部分字符串
##### 截取 起始索引startIndex  到  结束索引endIndex的子字符串，结果包含startIndex处的字符，不包含endIndex处的字符。

![本地图片](/img/slice.jpg  "slice")

#### split()分割:按给定字符串分割，返回分割后的多个字符串组成的字符串数组。

<!-- more -->

![本地图片](/img/split.jpg  "split")


#### join()合并:使用您选择的分隔符将一个数组合并为一个字符串
![本地图片](/img/join.jpg  "join")

#### 大小写 toLowerCase(), toUpperCase()
![本地图片](/img/tocase.jpg  "join")


#### replace(exp,a)

```  
myString = "boy & girl";
myString.replace(/(\w+)\s*&\s*(\w+)/g, "$2 & $1") //girl & boy
    
```  
#### replace(exp,a) 第二个参数为函数
![本地图片](/img/replace.jpg  "join")