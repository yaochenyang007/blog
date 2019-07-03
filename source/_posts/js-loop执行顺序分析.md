---
title: js loop执行顺序分析
date: 2019-06-27 12:08:59
tags: [js]
categories: js

---
### JavaScript 执行机制 event loop

同步任务：即在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务

异步任务：指的是不进入主线程，某个异步任务可以执行了，该任务才会进入主线程执行


macro-task大概包括：script(整体代码), setTimeout, setInterval, setImmediate, I/O, UI rendering。
micro-task大概包括: process.nextTick, Promise, Object.observe(已废弃), MutationObserver(html5新特性)
setTimeout/Promise等我们称之为任务源。而进入任务队列的是他们指定的具体执行任务（回调函数）。

<!-- more -->

![本地图片](/img/jsloop.png  "js执行顺序")

解释图片：

同步和异步任务分别进入不同的执行"场所"，同步的进入主线程，异步的进入Event Table并注册函数。
当指定的事情完成时，Event Table会将这个函数移入Event Queue。
主线程内的任务执行完毕为空，会去Event Queue读取对应的函数，进入主线程执行。
上述过程会不断重复，就是Event Loop(事件循环)。


 #### 实例1

```

console.log("start");
var promise = new Promise((resolve) => {
    console.log("promise start..");
    resolve("promise");
}); 
promise.then((val) => console.log(val));
setTimeout(()=>{console.log("setTime1")},0);
console.log("test end...")

// start
// promise start
// test end...
// promise
//setTime1

```
#####  分析执行顺序

第一轮
整体script代码作为一个宏任务进入主线程，运行console.log("start");。
然后遇到Promises直接运行console.log("promise start..")。

然后遇到promise.then被分发到微任务Event Queue中。我们记为then1。
遇到setTimeout，其回调函数被分发到宏任务Event Queue中。我们暂且记为setTimeout1
于然后运行console.log("test end...");
![本地图片](/img/miacro-task.png  "task")
在这一轮中，宏任务运行结束，运行micro-task队列中的 promise.then，输出promise
第二轮
取出macro-task队列中的setTimeout，运行console.log("setTime1");

 #### 实例2

```
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})

//整段代码，共进行了三次事件循环，完整的输出为1，7，6，8，2，4，3，5，9，11，10，12。

```
#####  分析执行顺序

第一轮事件循环流程分析如下：

整体script作为第一个宏任务进入主线程，遇到console.log，输出1。
遇到setTimeout，其回调函数被分发到宏任务Event Queue中。我们暂且记为setTimeout1。
遇到process.nextTick()，其回调函数被分发到微任务Event Queue中。我们记为process1。
遇到Promise，new Promise直接执行，输出7。then被分发到微任务Event Queue中。我们记为then1。
又遇到了setTimeout，其回调函数被分发到宏任务Event Queue中，我们记为setTimeout2。
我们发现了process1和then1两个微任务。
执行process1,输出6。
执行then1，输出8。
好了，第一轮事件循环正式结束，这一轮的结果是输出1，7，6，8。那么第二轮时间循环从setTimeout1宏任务开始：
首先输出2。接下来遇到了process.nextTick()，同样将其分发到微任务Event Queue中，记为process2。new Promise立即执行输出4，then也分发到微任务Event Queue中，记为then2。


第二轮事件循环宏任务结束，我们发现有process2和then2两个微任务可以执行。
输出3。
输出5。
第二轮事件循环结束，第二轮输出2，4，3，5。
第三轮事件循环开始，此时只剩setTimeout2了，执行。
直接输出9。
将process.nextTick()分发到微任务Event Queue中。记为process3。
直接执行new Promise，输出11。
将then分发到微任务Event Queue中，记为then3。



第三轮事件循环宏任务执行结束，执行两个微任务process3和then3。
输出10。
输出12。
第三轮事件循环结束，第三轮输出9，11，10，12。

