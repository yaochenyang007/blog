---
title: javascript使用技巧
date: 2019-06-17 14:59:13
tags: [javascript]
categories: js
---

<!-- https://mp.weixin.qq.com/s/X6ks8HrhAXvIjkcnZ9qMLw -->


### 手写一个Promise(中高级必考)
<!-- more -->
我们来过一遍 Promise/A+规范：

三种状态 pending|fulfilled(resolved)|rejected

当处于 pending状态的时候，可以转移到 fulfilled(resolved)或者 rejected状态

当处于 fulfilled(resolved)状态或者 rejected状态的时候，就不可变。

必须有一个 then异步执行方法， then接受两个参数且必须返回一个promise：

// onFulfilled 用来接收promise成功的值

// onRejected 用来接收promise失败的原因

promise1=promise.then(onFulfilled,onRejected);

```
function  myPromise(constructor) {
    let self = this;
    self.status = "pending"
    //定义状态改变前的初始状态

    self.value = undefined;
    //定义状态为resolved的时候的状态

    self.reason = undefined;
    //定义状态为rejected的时候的状态

    function resolve(value) {

        //两个==="pending"，保证了状态的改变是不可逆的

        if (self.status === "pending") {
            self.value = value;
            self.status = "resolved";
        }
    }


    functionreject(reason) {

        //两个==="pending"，保证了状态的改变是不可逆的
        if (self.status === "pending") {
            self.reason = reason;
            self.status = "rejected";
        }
    }

    //捕获构造异常
    try {
      constructor(resolve, reject);
    } catch (e) {
        reject(e);
    }
}
```
同时，需要在 myPromise的原型上定义链式调用的 then方法：

```
myPromise.
prototype.
then = function(
onFullfilled, onRejected) {
    let self = this;

    switch (self.status) {

    case "resolved":
        onFullfilled(self.value);
        break;
    case "rejected":
        onRejected(self.reason);
        break;
    default:

    }
}
var p = newmyPromise(
  function(resolve, reject) {
      resolve(1)
  }
);

p.then(function(x) {
  console.log(x)
})

//输出1

```
### bind,call,apply的实现

call 和 apply 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向

```
一、模拟思路
先看个常用例子

var foo = {
  value: 1
};

function bar() {
  console.log(this.value);
}

bar.call(foo); // 1
试想下，是不是可以先把bar变成foo对象的属性，执行完后再删除它呢？

var foo = {
  value: 1,
  bar: function() {
    console.log(this.value);
  }
};

foo.bar(); // 1
delete foo.bar;
总结一下步骤

1、将要执行的函数设置为对象的属性
2、执行函数（难点在于取出参数）
3、删除该函数

二、模拟call
Function.prototype.myCall = function(context) {
  // 取得传入的对象（执行上下文），比如上文的foo对象
  // 不传第一个参数，默认是window,
  var context = context || window;
  // 给context添加一个属性，这时的this指向调用call的函数，比如上文的bar
  context.fn = this;
  // 通过展开运算符和解构赋值取出context后面的参数
  var args = [...arguments].slice(1);
  // 执行函数
  var result = context.fn(...args);
  // 删除函数
  delete context.fn;
  return result;
};

  let foo = {
        value :1
      };
      function bar(name,age){
        console.log(name);
        console.log(age);
        console.log(this.value)
      };
    bar.myCall(foo,'black','18'); // foo black 18

三、模拟apply
思路跟call一样，只是在处理参数的时候有点不一样

Function.prototype.myApply = function(context) {
  var context = context || window;
  context.fn = this;
  var result;

  // 判断第二个参数是否存在，是一个数组
  // 如果存在，则需要展开第二个参数
  if (arguments[1]) {
    result = context.fn(...arguments[1]);
  } else {
    result = context.fn();
  }

  delete context.fn;
  return result;
}
四、模拟bind
思路和作用基本一致，区别在于返回一个函数，并且可以通过bind实现柯里化

Function.prototype.myBind = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error');
  }

  var _this = this;
  var args = [...arguments].slice(1);

  // 返回函数
  return function Fn() {
    // bind有个特点 一个绑定函数也能使用new操作符创建对象
    if (this instanceof Fn) {
      return new _this(args, ...arguments);
    }
    return _this.apply(context, args.concat(arguments));
  }
}

```

### map的实现

首先要搞清楚map()函数的用法，传入一个函数，函数允许传三个参数（数组每一项，数组索引，数组本身），并且map()函数有返回值,如此一来也就有思路了:

array.map(function(currentValue,index,arr), thisValue);
currentValue	必须。当前元素的值
index	可选。当前元素的索引值
arr	可选。当前元素属于的数组对象
thisValue	可选。对象作为该执行回调时使用，传递给函数，用作 "this" 的值。
如果省略了 thisValue，或者传入 null、undefined，那么回调函数的 this 为全局对象。

```
var  arr = [1, 2, 3];
arr.map(function() {
  console.log(this) // 在回调函数里使用 this，这个 this 就指向那个 {a: 1}
}, {a: 1})

```

使用Array.prototype 实现map 方法

```
  Array.prototype.newMap = function(fn,context){
        if(typeof fn !== 'function')return;
        var newArr = [];
        for(var i = 0;i < this.length;i++){
            newArr.push(fn.call(context,this[i],i,this));
        }
        return newArr;
    }
    //调用
    [1,2,3].newMap(function(item,index,arr){
        return item * 2;
    });//[2,4,6]

```

使用 reduce 实现数组 map 方法

```
const selfMap2 = function (fn,content){
  let arr = Array.prototype.slice.call(this);
  return arr.reduce( (pre,cur,index) => {
    return [...pre,fn.call(context,cur,index,this)]
  },[] )
} 

```


###   实现一个JS函数柯里化

什么是柯里化？

在计算机科学中，柯里化（Currying）是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数且返回结果的新函数的技术。
函数柯里化的主要作用和特点就是参数复用、提前返回和延迟执行。


```
function curry(fn,args){

var length = fn.length;

var args = args || [];
   
return function(){
  var newArgs = args.concat( Array.prototype.slice.call(arguments));
     
  if (newArgs.length <length){          
    return curry.call(this,fn,newArgs);       
  }else{      
    return fn.apply(this,newArgs);     
} }
}
function multiFn(a,b,c){
  returna *b *c;
}


var multi =curry(multiFn);
multi(2)(3)(4);
multi(2,3,4)
multi(2)(3,4);
multi(2,3)(4)

```
### 实现一个深拷贝

如何区分深拷贝与浅拷贝，简单点来说，就是假设B复制了A，当修改A时，看B是否会发生变化，如果B也跟着变了，说明这是浅拷贝，拿人手短，如果B没变，那就是深拷贝，自食其力。

递归方法实现深度克隆原理：遍历对象、数组直到里边都是基本数据类型，然后再去复制，就是深度拷贝
```
//定义检测数据类型的功能函数
function checkedType(target) {   // "[object Object]"   "[object Array]"
  return Object.prototype.toString.call(target).substring(8,13)  
}
//实现深度克隆---对象/数组
function clone(target) {
  //判断拷贝的数据类型
  //初始化变量result 成为最终克隆的数据
  let result,
    targetType = checkedType(target)
  if (targetType === 'Object') {
    result = {}
  } else if (targetType === 'Array') {
    result = []
  } else {
    return target
  }
  //遍历目标数据
  for (let i in target) {
    //获取遍历数据结构的每一项值。
    let value = target[i]
    //判断目标结构里的每一值是否存在对象/数组
    if (checkedType(value) === 'Object' || checkedType(value) === 'Array') {
      //对象/数组里嵌套了对象/数组
      //继续遍历获取到value值
      result[i] = clone(value)
    } else {
      //获取到value值是基本的数据类型或者是函数。
      result[i] = value
    }
  }
  return result
}
```