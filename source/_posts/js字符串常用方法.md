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
<!-- more -->
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
![本地图片](/img/replace.jpg  "replace")


####  splice() 方法向/从数组中添加/删除项目，然后返回被删除的项目。

##### 注释：该方法会改变原始数组。说明

##### splice() 方法可删除从 index 处开始的零个或多个元素，并且用参数列表中声明的一个或多个值来替换那些被删除的元素。

#####  如果从 arrayObject 中删除了元素，则返回的是含有被删除的元素的数组。


```  
<script type="text/javascript">

var arr = new Array(6)
arr[0] = "George"
arr[1] = "John"
arr[2] = "Thomas"
arr[3] = "James"
arr[4] = "Adrew"
arr[5] = "Martin"

document.write(arr + "<br />")
arr.splice(2,0,"William")  第一个参数，表示删除数的位置，第二个参数表示删除数的个数，第三个参数表示 删除的数改为当前参数值
document.write(arr + "<br />")

</script>
输出：

George,John,Thomas,James,Adrew,Martin
George,John,William,Thomas,James,Adrew,Martin
      
```  



####  reduce() 方法接收一个函数作为累加器（accumulator），数组中的每个值（从左到右）开始缩减，最终为一个值。
#### reduce 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或从未被赋值的元素。接受四个参数：初始值（或者上一次回调函数的返回值），当前元素值，当前索引，调用 reduce 的数组。


```  
计算数组元素相加后的总和：

var numbers = [65, 44, 12, 4];
 
function getSum(total, num) {
    return total + num;
}
function myFunction(item) {
    document.getElementById("demo").innerHTML = numbers.reduce(getSum);
}
输出结果：

125

      
```  

![本地图片](/img/reduce.jpg  "reduce")


####  forEach() 方法用于调用数组的每个元素，并将元素传递给回调函数。注意: forEach() 对于空数组是不会执行回调函数的。 forEach方法中的function回调有三个参数：第一个参数是遍历的数组内容，第二个参数是对应的数组索引，第三个参数是数组本身


因此：
```  
var arr = [1,2,3,4];
arr.forEach(function(value,index,array){
    array[index] == value;    //结果为true
    sum+=value;  
    });
console.log(sum);    //结果为 10      
```  