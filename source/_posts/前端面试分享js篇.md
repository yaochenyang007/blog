---
title: 前端面试分享js篇
date: 2019-02-22 14:30:06
tags:  [javascript，继承,promise,call,prototype,事情委托]
categories: 面试
---

<!-- https://juejin.im/post/5d23e750f265da1b855c7bbe -->

### JavaScript call,apply,bind异同


call 和 apply 。这两个函数都是在特定的作用域中调用函数,能改变函数的作用域，实际上是改变函数体内 this 的值 。
call传递多个参数。
apply传递多个参数的时候第二个参数需要传递一个数组。

bind用来改变this的指向，返回一个修改过的函数，该函数不会被执行。

用一个对象的一个方法，以另一个对象替换当前对象。
<!--- more --->
```
var a = {
    user:"Ice",
    fn:function(){
        console.log(this.user); //Ice
    }
}


var b = a.fn;
b.apply(a); //将a对象的this赋值给b对象,
b.call(a); //将a对象的this赋值给b对象,
//如果call和apply的第一个参数写的是null，那么this指向的是window对象

```

bind用来改变this的指向，返回一个修改过的函数，该函数不会被执行
```
var a = {
    user:"Ice",
    fn:function(){
        console.log(this.user); 
    }
}
var b = a.fn;
var c = b.bind(a);
c();//Ice
```

调用函数，传递参数
```
  //定义一个add 方法
    function add(x, y) {
        return x + y;
    }

    //用call 来调用 add 方法
    function myAddCall(x, y) {
        //调用 add 方法 的 call 方法
        return add.call(this, x, y);
    }

    //apply 来调用 add 方法
    function myAddApply(x, y) {
        //调用 add 方法 的 applly 方法
        return add.apply(this, [x, y]);
    }

    console.log(myAddCall(10, 20));    //输出结果30
  
    console.log(myAddApply(20, 20));  //输出结果40

    
```


高级用法，实现 js 继承 ，子类继承父类的方法。

```
//父类Person
function Person(){
    this.Name = function(){
       return this.name;
    }
}

//子类Chinese
function Chinese(name){
        
        Person.call(this); //将对象Chinese的this赋值给对象Person,
        this.name = name;  //此时this是指父类Person的this;
}

//子类America
function America(name){
        Person.apply(this);  //将对象America的this赋值给对象Person,
         this.name = name; //此时this是指父类Person的this;
}

var chinese = new Chinese("我是中国人");

chinese.Name();  //我是中国人


var america = new America("我是美国人");

america.Name();  //我是美国人



```

###  实例化new 操作过程
要创建 Person 的新实例，必须使用 new 操作符。以这种方式调用构造函数实际上会经历以下 4
个步骤：

```
var person =  new Person();

```
等价于

```
var person  = {};
person.__proto__ = Person.prototype;
Person.call(person);
第一行，我们创建了一个空对象person
第二行，我们将这个空对象的__proto__成员指向了Person函数对象prototype成员对象
第三行，我们将Person函数对象的this指针替换成person，然后再调用Person函数，于是我们就给person对象赋值了一个id成员变量。
```
(1) 创建一个新对象;
(2) 将构造函数的作用域赋给新对象（因此 this 就指向了这个新对象） ；
(3) 执行构造函数中的代码（为这个新对象添加属性） ；
(4) 返回新对象。

###   prototype和 __proto__的区别
任意一个函数（包括构造函数）都有一个prototype属性，指向该函数的原型对象，同样任意一个构造函数实例化的对象，都有一个__proto__属性。

```

function a() {
    //console.log("I'am a function.");
}
//b是实例化对象，a是构造函数
var b = new a();

console.log(b.__proto__ == a.prototype);  //true
console.log(Object.getPrototypeOf(b) == a.prototype);  //true,标准方法返回的结果和上述一样

```

区别
```
//注意：a作为构造函数时的prototype属性与a作为普通函数时的__proto__属性并不相等
console.log(a.prototype == a.__proto__);//false

//这个怎么理解呢，我们再看看如下代码
console.log(a.__proto__);         //function (){}
console.log(a.__proto__ == Function.prototype);//true

//实际上，a作为一个普通函数调用的时候，它的构造函数就是内置对象Function了，所以它指向的原型对象，自然对应就是Function.prototype.
//其实这个和console.log(b.__proto__ == a.prototype)是一样的道理

//我们继续看如下代码，当a作为构造函数时，它的原型，和它的原型的原型都指向神马
console.log(a.prototype);                   //a{}
console.log(a.prototype.__proto__);  //Object{}

//我们再看看a作为普通函数时，它原型的原型指向神马
console.log(a.__proto__.__proto__); //Object{}

//即有以下结果
console.log(a.__proto__.__proto__ == a.prototype.__proto__); //true

```
结论:
prototype是构造函数访问原型对象，__proto__是对象实例访问原型对象。


###   什么是事件委托吗？

事件委托就是利用冒泡的原理，把事件加到父元素或祖先元素上，触发执行效果。


```
<input type="button" value="click me" id="btn6">

var btn6 = document.getElementById("btn6");
document.onclick = function(event){
	event = event || window.event;
	var target = event.target || event.srcElement;
	if(target == btn6){
		alert(btn5.value);
	}
}

```
在实际的代码中 我们可能用到jQuery的live()、delegate()、bind()、on()等。
事件委托优点
1、提高JavaScript性能。事件委托可以显著的提高事件的处理速度，减少内存的占用。 实例分析JavaScript中的事件委托和事件绑定 


###    现在有函数A 和函数B，请你实现B继承A。 
####   原型继承
其核心是将父类的实例作为子类的原型
```
function A(){
    this.a = 1;
    this.b = 2;
    this.add = function(){
        return this.a + this.b;
    }
}
function B(){

}
 B.prototype = new A();
B.prototype.add();  //3
B.prototype.c = 4;
var b = new B();

b  = function B(){
    _proto_: A(){
        a:1,
        b :2,
        add : function(){
            return this.a + this.b;
        },
        _proto_:Object
    }
}  

b.a //1 
b.b //2
b.c //4

```

#### 构造函数继承
核心是将父类的实例作为子类的原型
```
var function A(){
    this.a=1;
    this.b=2;
    this.add=function(){
    	console.log(a+b);
    }
}
var B=new A();
B.c=3;
B.add=function () {
	console.log(B.a+B.b+B.c);
}
console.log(B.a); 
console.log(B.b);
console.log(B.c);
B.add();

```
###  js中的 位运算符 &(AND) 和 |(OR)
位运算 AND 由和号（&）表示，直接对数字的二进制形式进行运算。它把每个数字中的数位对齐，然后用下面的规则对同一位置上的两个数位进行 AND 运算：

1 | 1 = 1
1 | 0 = 1
0 | 1 = 1
0 | 0 = 0

1 & 1 = 1
1 & 0 = 0
0 & 1 = 0
0 & 0 = 0

###  彻底理解js中的&&(逻辑与)  和|| (逻辑或)

在javascript中：

以下内容会被当成false处理："" , false , 0 , null , undefined , NaN

其他都是true。注意：字符串"false"也会被当做true处理，在未转型的情况下他是字符串，属于一个对象，所以是true。


所以：

a || b：如果a是true，那么b不管是true还是false，都返回true。因此不用判断b了，这个时候刚好判断到a，因此返回a。

　　　如果a是false，那么就要判断b，如果b是true，那么返回true，如果b是false，返回false，其实不就是返回b了吗。
```
var a = 0 || 1 // 1
var a = 1 || 0 // 1
var a = 0 || false // false
var a = 1 || 2 // 1
```
a && b：如果a是false，那么b不管是true还是false，都返回false，因此不用判断b了，这个时候刚好判断到a，因此返回a。

　　　如果a是true，那么就要在判断b，和刚刚一样，不管b是true是false，都返回b。

```
var a = 1 && 0; // 0
var a = 0 && 1; // 0
var a = 1 && 1; // 1
var a = 0 && 0; // 0
```

###    js去重方法

数组去重
```
var arr = [2,2,3,5,6,7,9,10,2,4,6,7,8,4,6]
var temp = []
for(let key in arr){
if(temp.indexOf(arr[key]) ===-1){
	temp.push(arr[key]);
}
}

console.log(temp) //[2, 3, 5, 6, 7, 9, 10, 4, 8]
```

数组对象属性值去重



```
var arr = [{id:1},{id:2},{id:3},{id:4},{id:5},{id:2},{id:6},{id:20},{id:10},{id:8},{id:6},{id:5},{id:6}]
function arrayUnique2(arr, name) {
    const hash = {};
    return arr.reduce(function (item, next) {
        hash[next[name]] ? '' : hash[next[name]] = true && item.push(next);
        //先执行 hash[next[name]],若为false,则执行 hash[next[name]] = true && item.push(next),
       // 再执行 等号右边 (true && item.push(next))，再执行 等号左边 hash[next[name]] 赋值。
        return item;
    }, []);
}
var qwe = arrayUnique2(arr,'id')

console.log(qwe)  
// [{id: 1}, {id: 2}, {id: 3},{id: 4}, {id: 5}, {id: 6},{id: 20}, {id: 10},{id: 8}]

```



###   window的onload事件和domcontentloaded谁先谁后？
DOMContentLoaded事件 先执行。

当DOM树构建完成的时候就会执行DOMContentLoaded事件。
当window.onload事件触发时。页面上所有的DOM，样式表，脚本，图片，flash都已经加载完成了。


###    typeof和instanceof的区别

typeof 是一个一元运算，放在一个运算数之前，运算数可以是任意类型。

它返回值是一个字符串，该字符串说明运算数的类型。（typeof 运算符返回一个用来表示表达式的数据类型的字符串。 ）

运算数为数字 typeof(x) = "number" 

字符串 typeof(x) = "string" 

布尔值 typeof(x) = "boolean" 

对象,数组和null typeof(x) = "object" 

函数 typeof(x) = "function" 

在 JavaScript 中，判断一个变量的类型尝尝会用 typeof 运算符，在使用 typeof 运算符时采用引用类型存储值会出现一个问题，无论引用的是什么类型的对象，它都返回 “object”。这就需要用到instanceof来检测某个对象是不是另一个对象的实例。

instanceof

instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。
instanceof 用于判断一个变量是否某个对象的实例.

```
如 :var a=new Array();

alert(a instanceof Array); // true，

同时 alert(a instanceof Object) //也会返回 true;

这是因为 Array 是 object 的子类。


再如：function test(){};

var a=new test();

alert(a instanceof test) 会返回true

```

更重的一点是 instanceof 可以在继承关系中用来判断一个实例是否属于它的父类型。

```
function Foo(){} 
Foo.prototype = new Aoo();//JavaScript 原型继承 
var foo = new Foo(); 
console.log(foo instanceof Foo)//true 
console.log(foo instanceof Aoo)//true

```
###    new和instanceof的内部机制

首先我们来看看var obj = new O()这条语句发生了什么:

```
var obj = (function(){
    var obj = {};
    obj.__proto__ = O.prototype;

    //其他赋值语句...

    return obj;
})();
也就是说var obj = new O()返回了一个obj对象，它的隐式原型链(__proto__)是指向O原型(prototype)的。这就是new的内部工作方式。
```

下面再看instanceof，假设现在有 x instanceof y 一条语句，则其内部实际作出了如下的判断:

```
while(x.__proto__!==null) {
　　if(x.__proto__===y.prototype) {
　　　　return true;
　　　　break;
　　}
　　x.__proto__ = x.__proto__.proto__;
}
if(x.__proto__==null) {return false;}
x会一直沿着隐式原型链__proto__向上查找直到x.__proto__.__proto__......===y.prototype为止，如果找到则返回true，也就是x为y的一个实例。否则返回false，x不是y的实例。
```

下面举出两个例子，把对O的原型(prototype)的修改放在obj的声明之前和之后的两种情况下，使用instanceof对obj作检测的结果对比：
```
function F(){}
function O(){}
O.prototype = new F();
var obj = new O();
console.log(obj instanceof O);//true
console.log(obj instanceof F);//true
```

```
function F(){}
function O(){}
var obj = new O();
o.prototype = new F();
console.log(obj instanceof O);//false
console.log(obj instanceof F);//false
```


###    javascript中的异步 macrotask 和 microtask ,说一下macrotask 和 microtask？
macrotasks: setTimeout, setInterval, setImmediate, I/O, UI rendering
microtasks: process.nextTick, Promises, Object.observe(废弃), MutationObserver

```
console.log(1);
setTimeout(function(){
  console.log(2);
}, 0);
Promise.resolve().then(function(){
  console.log(3);
}).then(function(){
  console.log(4);
});

上面的代码输出的是 1, 3, 4, 2;
```
Promise的函数代码的异步任务会优先于setTimeout的延时为0的任务先执行。

原因是任务队列分为 macrotasks 和 microtasks, 而promise中的then方法的函数会被推入到microtasks队列中，
而setTimeout函数会被推入到macrotasks。
任务队列中，在每一次事件循环中，macrotask只会提取一个执行，而microtask会一直提取，直到microsoft队列为空为止。
也就是说如果某个microtask任务被推入到执行中，那么当主线程任务执行完成后，会循环调用该队列任务中的下一个任务来执行，直到该任务队列到最后一个任务为止。而事件循环每次只会入栈一个macrotask,主线程执行完成该任务后又会检查microtasks队列并完成里面的所有任务后再执行macrotask的任务。
###    Http请求中的keep-alive有了解吗。

http keep-alive是为了让tcp活得更久一点，以便在同一个连接上传送多个http，提高socket的效率。
在http早期，每个http请求都要求打开一个tcp socket连接，并且使用一次之后就断开这个tcp连接。
使用keep-alive可以改善这种状态，即在一次TCP连接中可以持续发送多份数据而不会断开连接。通过使用keep-alive机制，可以减少tcp连接建立次数，也意味着可以减少TIME_WAIT状态连接，以此提高性能和提高httpd服务器的吞吐率(更少的tcp连接意味着更少的系统内核调用,socket的accept()和close()调用)。

###    数组扁平化处理：实现一个flatten方法，使得输入一个数组，该数组里面的元素也可以是数组，该方法会输出一个扁平化的数组。
var arr=[1,[2,[[3,4],5],6]];
//利用reduce实现
```
 function flatten(arr){
     return arr.reduce(function(total,next){
        return total.concat(Array.isArray(next) ? flatten(next) : next );
     },[])
}
flatten(arr);
//[1, 2, 3, 4, 5, 6]
```

//利用if判断实现,递归
```
function flatten(arr){
       var result = [];
    if(!Array.isArray(arr)){
        if(arr % 1 === 0){
            result.push(arr);
        }else{
             throw Error('抛出异常，不为整数');
        }
    }else{
        for(let i=0;i<arr.length;i++){
            result = result.concat( Array.isArray(arr[i]) ? flatten(arr[i]) : arr[i])
        }
    }
    return result;
}
```

//扁平化数组(不使用循环，使用字符串)
```
function flatten(arr) {
    let str = JSON.stringify(arr).replace(/\[|\]/g, '');
    //Array.of方法用于将一组值，转换为数组。
    return JSON.parse(Array.of('[' + str + ']')[0]);
}

```
###    实现一个div滑动的动画，由快至慢5s结束（不准用css3)。
```
.sj{
    width:50px;
    height:50px;
    border:1px solid red;
    position:absolute;
    left:0
}
<div class="sj" id="sj"></div>
//ele为要移动的盒子，target为目标位置（像素），spd为计数器的频率
var ele = document.getElementById('sj')
function animate(ele,spd){
    var start = Date.now(); // remember start time
    var timer = setInterval(function() {
        var timePassed = Date.now() - start;
        var step = Math.ceil(Math.abs(timePassed - 5000)/10)  //abs取绝对值，ceil向上取整 
        console.log(step)
        if (timePassed >= 5000) {
            clearInterval(timer); // finish the animation after 2 seconds
            return;
        }
        ele.style.left = ele.offsetLeft + step + 'px'  //offsetLeft 左偏移量
    }, spd);
}
setInterval(ele,1000);
```
###  js浮点数运算不精确 如何解决?
在测试js浮点数进行加减乘除计算时，都可能出现问题，如下：
```
console.log(0.1 + 0.2);//0.30000000000000004
console.log(1.0 - 0.9);//0.09999999999999998
console.log(19.9 * 100);//1989.9999999999998
console.log(6.6 / 0.2);//32.99999999999999
```
不精准原因：计算器做加减乘除运算都是转换为二进制（0和1）运算，存在误差。
0.1 => 0.0001 1001 1001 1001…（无限循环）
0.2 => 0.0011 0011 0011 0011…（无限循环）  

```
//核心思想：化浮点数为整数做运算
var floatObj = function () {
 
        /*
         * 判断obj是否为一个整数
         */
        function isInteger(obj) {
            return Math.floor(obj) === obj
        }
 
        /*
         * 将一个浮点数转成整数，返回整数和倍数。如 3.14 >> 314，倍数是 100
         * @param floatNum {number} 小数
         * @return {object}
         *   {times:100, num: 314}
         */
        function toInteger(floatNum) {
            var ret = {times: 1, num: 0};
            if (isInteger(floatNum)) {
                ret.num = floatNum;
                return ret
            }
            var strfi = floatNum + '';
            var dotPos = strfi.indexOf('.');
            var len = strfi.substr(dotPos + 1).length;
            var times = Math.pow(10, len);
            var intNum = parseInt(floatNum * times , 10);
            ret.times = times;
            ret.num = intNum;
            return ret
        }
 
        /*
         * 核心方法，实现加减乘除运算，确保不丢失精度
         * 思路：把小数放大为整数（乘），进行算术运算，再缩小为小数（除）
         *
         * @param a {number} 运算数1
         * @param b {number} 运算数2
         * @param op {string} 运算类型，有加减乘除（add/subtract/multiply/divide）
         *
         */
        function operation(a, b, op) {
            var o1 = toInteger(a);
            var o2 = toInteger(b);
            var n1 = o1.num;
            var n2 = o2.num;
            var t1 = o1.times;
            var t2 = o2.times;
            var max = t1 > t2 ? t1 : t2;
            var result = null;
            switch (op) {
                case 'add':
                    if (t1 === t2) { // 两个小数位数相同
                        result = n1 + n2
                    } else if (t1 > t2) { // o1 小数位 大于 o2
                        result = n1 + n2 * (t1 / t2)
                    } else { // o1 小数位 小于 o2
                        result = n1 * (t2 / t1) + n2
                    }
                    return result / max;
                case 'subtract':
                    if (t1 === t2) {
                        result = n1 - n2
                    } else if (t1 > t2) {
                        result = n1 - n2 * (t1 / t2)
                    } else {
                        result = n1 * (t2 / t1) - n2
                    }
                    return result / max;
                case 'multiply':
                    result = (n1 * n2) / (t1 * t2);
                    return result;
                case 'divide':
                    result = (n1 / n2) * (t2 / t1);
                    return result
            }
        }
 
        // 加减乘除的四个接口
        function add(a, b) {
            return operation(a, b, 'add')
        }
 
        function subtract(a, b) {
            return operation(a, b, 'subtract')
        }
 
        function multiply(a, b) {
            return operation(a, b, 'multiply')
        }
 
        function divide(a, b) {
            return operation(a, b, 'divide')
        }
 
        // exports
        return {
            add: add,
            subtract: subtract,
            multiply: multiply,
            divide: divide
        }
    }();
console.log(floatObj.add(0.1, 0.2));//0.3
console.log(floatObj.subtract(1.0, 0.9));//0.1
console.log(floatObj.multiply(19.9, 100));//1990
console.log(floatObj.divide(6.6, 0.2));//33
```

### js 二分查找

###   页面内有一个input输入框，实现在数组arr查询命中词并要求autocomplete(自动完成)效果。

###   实现超出整数存储范围的两个大整数相加function add(a,b)。注意a和b以及函数的返回值都是字符串。

###   深入理解TCP

当一台计算机想要与另一台计算机通讯时，两台计算机之间的通信需要畅通且可靠，这样才能保证正确收发数据。例如，当你想查看网页或查看电子邮件时，希望完整且按顺序查看网页，而不丢失任何内容。当你下载文件时，希望获得的是完整的文件，而不仅仅是文件的一部分，因为如果数据丢失或乱序，都不是你希望得到的结果，于是就用到了 TCP。

TCP 协议全称是传输控制协议是一种面向连接的、可靠的、基于字节流的传输层通信协议，由 IETF 的 RFC 793 定义。TCP 是面向连接的、可靠的流协议。流就是指不间断的数据结构，你可以把它想象成排水管中的水流。

1.TCP 连接过程
如下图所示，可以看到建立一个 TCP 连接的过程为（三次握手的过程）:


第一次握手

客户端向服务端发送连接请求报文段。该报文段中包含自身的数据通讯初始序号。请求发送后，客户端便进入 SYN-SENT 状态。

第二次握手

服务端收到连接请求报文段后，如果同意连接，则会发送一个应答，该应答中也会包含自身的数据通讯初始序号，发送完成后便进入 SYN-RECEIVED 状态。

第三次握手

当客户端收到连接同意的应答后，还要向服务端发送一个确认报文。客户端发完这个报文段后便进入 ESTABLISHED 状态，服务端收到这个应答后也进入 ESTABLISHED 状态，此时连接建立成功。

这里可能大家会有个疑惑：为什么 TCP 建立连接需要三次握手，而不是两次？这是因为这是为了防止出现失效的连接请求报文段被服务端接收的情况，从而产生错误。



2.TCP 断开链接

TCP 是全双工的，在断开连接时两端都需要发送 FIN 和 ACK。

第一次握手

若客户端 A 认为数据发送完成，则它需要向服务端 B 发送连接释放请求。

第二次握手

B 收到连接释放请求后，会告诉应用层要释放 TCP 链接。然后会发送 ACK 包，并进入 CLOSE_WAIT 状态，此时表明 A 到 B 的连接已经释放，不再接收 A 发的数据了。但是因为 TCP 连接是双向的，所以 B 仍旧可以发送数据给 A。

第三次握手

B 如果此时还有没发完的数据会继续发送，完毕后会向 A 发送连接释放请求，然后 B 便进入 LAST-ACK 状态。

第四次握手

A 收到释放请求后，向 B 发送确认应答，此时 A 进入 TIME-WAIT 状态。该状态会持续 2MSL（最大段生存期，指报文段在网络中生存的时间，超时会被抛弃） 时间，若该时间段内没有 B 的重发请求的话，就进入 CLOSED 状态。当 B 收到确认应答后，也便进入 CLOSED 状态。

3.TCP 协议的特点
面向连接
面向连接，是指发送数据之前必须在两端建立连接。建立连接的方法是“三次握手”，这样能建立可靠的连接。建立连接，是为数据的可靠传输打下了基础。

仅支持单播传输
每条 TCP 传输连接只能有两个端点，只能进行点对点的数据传输，不支持多播和广播传输方式。

面向字节流
TCP 不像 UDP 一样那样一个个报文独立地传输，而是在不保留报文边界的情况下以字节流方式进行传输。

可靠传输
对于可靠传输，判断丢包，误码靠的是 TCP 的段编号以及确认号。TCP 为了保证报文传输的可靠，就给每个包一个序号，同时序号也保证了传送到接收端实体的包的按序接收。然后接收端实体对已成功收到的字节发回一个相应的确认(ACK)；如果发送端实体在合理的往返时延(RTT)内未收到确认，那么对应的数据（假设丢失了）将会被重传。

提供拥塞控制
当网络出现拥塞的时候，TCP 能够减小向网络注入数据的速率和数量，缓解拥塞

TCP 提供全双工通信
TCP 允许通信双方的应用程序在任何时候都能发送数据，因为 TCP 连接的两端都设有缓存，用来临时存放双向通信的数据。当然，TCP 可以立即发送一个数据段，也可以缓存一段时间以便一次发送更多的数据段（最大的数据段大小取决于 MSS）

TCP 详解参考如下：https://github.com/ljianshu/Blog/issues/61


###   移动端300ms延时的原因? 如何处理?
300 毫秒延迟的主要原因是解决双击缩放。即用手指在屏幕上快速点击两次，iOS 自带的 Safari 浏览器会将网页缩放至原始比例。
1.
引入 fasrClick.js 插件，
fastclick.js的原理是：FastClick的实现原理是在检测到touchend事件的时候，会通过DOM自定义事件立即出发模拟一个click事件，并把浏览器在300ms之后真正的click事件阻止掉。
```
//js写法
 if('addEventListener' in document) {
        document.addEventListener('DOMContentLoaded', function() {
            FastClick.attach(document.body);
        }, false);
    }
// require写法
var attachFastClick = require('fastclick');
attachFastClick(document.body);

```
2.添加viewpoint meta标签。
```
<meta name="viewport" content="width=device-width,initial-scale=1,minimum-scale=1,maximum-scale=1,user-scalable=no" />
```
###  前端跨域方案

什么是跨域？
跨域是指一个域下的文档或脚本试图去请求另一个域下的资源，这里跨域是广义的。

广义的跨域：

资源跳转： A链接、重定向、表单提交
资源嵌入：
 ```
 <link>、<script>、<img>、<frame>等dom标签，还有样式中background:url()、@font-face()等文件外链
 ```
脚本请求： js发起的ajax请求、dom和js对象的跨域操作等
其实我们通常所说的跨域是狭义的，是由浏览器同源策略限制的一类请求场景。

什么是同源策略？
如果缺少了同源策略，浏览器很容易受到XSS、CSFR等攻击。所谓同源是指"协议+域名+端口"三者相同，即便两个不同的域名指向同一个ip地址，也非同源。

同源策略限制以下几种行为：

Cookie、LocalStorage 和 IndexDB 无法读取
DOM 和 Js对象无法获得
AJAX 请求不能发送

跨域解决方案
1、 通过jsonp跨域
2、 document.domain + iframe跨域
3、 location.hash + iframe
4、 window.name + iframe跨域
5、 postMessage跨域
6、 跨域资源共享（CORS）
7、 nginx代理跨域
8、 nodejs中间件代理跨域
9、 WebSocket协议跨域

jsonP 跨域
```
<script>
    var script = document.createElement('script');
    script.type = 'text/javascript';

    // 传参并指定回调执行函数为onBack
    script.src = 'http://www.domain2.com:8080/login?user=admin&callback=onBack';
    document.head.appendChild(script);

    // 回调执行函数
    function onBack(res) {
        alert(JSON.stringify(res));
    }
 </script>
```



###  设置，读取，删除 cookies
```
/**
		 * @function 设置cookie
		 * @param {Array} cname key
		 * @param {String} cvalue value
		 * @param {Number} exdays exdays
		 */
		setCookie(cname, cvalue, exdays) {
			const d = new Date();
			d.setTime(d.getTime() + exdays * 24 * 60 * 60 * 1000);
			const expires = 'expires=' + d.toUTCString();
			document.cookie = cname + '=' + cvalue + '; ' + expires + ';path=/';
		},
		/**
		 * @function 获取cookie
		 * @param {String} cname value
		 * @return {String} String
		 */
		getCookie(cname) {
			const name = cname + '=';
			const ca = document.cookie.split(';');

			for (let i = 0; i < ca.length; i++) {
				let c = ca[i];
				while (c.charAt(0) === ' ') c = c.substring(1);
				if (c.indexOf(name) !== -1) {
					const cnameValue = decodeURIComponent(
						c.substring(name.length, c.length)
					);
					const temp = cnameValue.split('=');

					return temp;
				}
			}
			return '';
		},
		/**
		 *  清空Cookie
		 */
		clearCookie() {
			this.setCookie('', '', -1);
		},

```

###  前端模块化理解
CommonJS
CommonJS规范是由NodeJS发扬光大。

根据CommonJS规范，一个单独的文件就是一个模块。每一个模块都是一个单独的作用域，也就是说，在该模块内部定义的变量，无法被其他模块读取，除非定义为global对象的属性。
CommonJS的风格通过对module.exports或exports的属性赋值来达到暴露模块对象的目的。CommonJS鬼单价在模块是同步的。

AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。
CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。
AMD 是提前执行，CMD 是延迟执行。
AMD推荐的风格通过返回一个对象做为模块对象。

requireJS主要解决两个问题：
一，多个js文件可能有依赖关系，被依赖的文件需要早于依赖它的文件加载到浏览器；
二，js加载的时候浏览器会停止页面渲染，加载文件越多，页面失去响应时间越长。
require()函数在加载依赖的函数的时候是异步加载的，这样浏览器不会失去响应，它指定的回调函数，只有前面的模块都加载成功后，才会运行，解决了依赖性的问题。

CMD模块方式
    define(function(require, exports, module) {

      // 模块代码

    });

###  new String('a') 和 'a' 是一样的么? 
从语法上来说String(a)返回的是基本类型，new String(a)创建的是一个对象，两者是有区别的。
```
var s0 = 'hello';
var s1 = new String(s0);
var s2 = String(s0);
console.log(s1 === s2); // false
console.log(s1 === s0); // false
console.log(s2 === s0); // true
s1.foo = 'bar';
s2.foo = 'bar';
console.log(s1.foo); // bar
console.log(s2.foo); // undefined

```

###  DOM基础知识,添加元素,删除元素等等...

获取元素

（1）getElementByid

根据元素的id属性来获取元素，获取到的是一个元素。

（2）getElementsByTagName

根据标签名来获取元素，结果是一个元素集合。

（3）getElementsByClassName

根据class属性来获取元素，结果是一个元素集合。

（4）getElementsByName
修改元素
 document.getElementById("").innerText = '123';
 document.getElementById("").innerHTML  = '123<br>换行了';

添加删除元素
（1）CreateElement建一个元素节点

CreateElement("p")创建一个段落

（2）createTextNode创建一个文本节点

createTextNode("文本内容")，创建一个值为“文本内容”的文本节点.

（3）appendChild添加子节点

（4）removeChild  删除子节点

### DOM节点类型 


Document：是根节点

ParentNode：获取父节点

childNodes：获取所有子节点

firstChild：第一个子节点

lastChlid：获取最后一个子节点

### 前端安全 xss 和 csrf
XSS

XSS，即 Cross Site Script，中译是跨站脚本攻击；其原本缩写是 CSS，但为了和层叠样式表(Cascading Style Sheet)有所区分，因而在安全领域叫做 XSS。

XSS 攻击是指攻击者在网站上注入恶意的客户端代码，通过恶意脚本对客户端网页进行篡改，从而在用户浏览网页时，对用户浏览器进行控制或者获取用户隐私数据的一种攻击方式。

攻击者对客户端网页注入的恶意脚本一般包括 JavaScript，有时也会包含 HTML 和 Flash。有很多种方式进行 XSS 攻击，但它们的共同点为：将一些隐私数据像 cookie、session 发送给攻击者，将受害者重定向到一个由攻击者控制的网站，在受害者的机器上进行一些恶意操作。


XSS攻击可以分为3类：反射型（非持久型）、存储型（持久型）、基于DOM。

CSRF

CSRF，即 Cross Site Request Forgery，中译是跨站请求伪造，是一种劫持受信任用户向服务器发送非预期请求的攻击方式。

通常情况下，CSRF 攻击是攻击者借助受害者的 Cookie 骗取服务器的信任，可以在受害者毫不知情的情况下以受害者名义伪造请求发送给受攻击服务器，从而在并未授权的情况下执行在权限保护之下的操作。

xss 跨站脚本攻击，主要是前端层面的，用户在输入层面插入攻击脚本，改变页面的显示，或则窃取网站 cookie，预防方法：不相信用户的所有操作，对用户输入进行一个转义，不允许 js 对 cookie 的读写

csrf 跨站请求伪造，以你的名义，发送恶意请求，通过 cookie 加参数等形式过滤

我们没法彻底杜绝攻击，只能提高攻击门槛

参考链接:https://mp.weixin.qq.com/s/IROuODpzxv9nTtnKgMyvsg

### 闭包相关

闭包，官方对闭包的解释是：一个拥有许多变量和绑定了这些变量的环境的表达式（通常是一个函数），因而这些变量也是该表达式的一部分。闭包的特点：
　　1. 作为一个函数变量的一个引用，当函数返回时，其处于激活状态。
　　2. 一个闭包就是当一个函数返回时，一个没有释放资源的栈区。
　　简单的说，Javascript允许使用内部函数---即函数定义和函数表达式位于另一个函数的函数体内。而且，这些内部函数可以访问它们所在的外部函数中声明的所有局部变量、参数和声明的其他内部函数。当其中一个这样的内部函数在包含它们的外部函数之外被调用时，就会形成闭包。
例如：两种常用的闭包
```
var Circle = function() {  
   var obj = new Object();  
   obj.PI = 3.14159;  
     
   obj.area = function( r ) {  
       return this.PI * r * r;  
   }  
   return obj;  
}  
  
var c = new Circle();  
alert( c.area( 1.0 ) );
```

```
var Circle={  
   "PI":3.14159,  
 "area":function(r){  
          return this.PI * r * r;  
        }  
};  
alert( Circle.area(1.0) );
```
### 什么是栈,堆。
堆（heap）：堆内存的简称。 
栈（stack）：栈内存的简称。 
在js中的变量分为基本类型和引用类型。基本类型就是保存在栈中的简单数据段，而引用类型就是那些保存在堆内存中的对象。 
基本类型在内存中分别占有固定大小的空间，会自动释放。引用类型值大小不固定，栈内存中存放地址指向堆内存中的对象，当查询引用类型的变量时候先从栈中读取内存地址，然后再通过地址找到堆中的值。 
```
var arr1 = [0, 1, 2, 3];
var arr2 = arr1;
var str1 = arr1[2];
console.log(arr2); // 0, 1, 2, 3
console.log(str1); // 2

arr2[4] = 4;
str1 = 5;
console.log(arr1); // 0, 1, 2, 3, 4
console.log(arr1[2]); // 2

```
上面例子可以看出，当修改arr2中数据时候，arr1中的数据也发生了改变，而当修改str1的数值时，arr1中数据没发生改变。

因为arr1时数组，属于引用类型，所以它赋值给arr2时候传的是栈中的地址（“指针”’），而不是堆内存中的对象。arr1 arr2都指向同一块堆内存，当arr2修改堆内存时候也就会影响到了arr1。str1得到的是一个基本类型的值，所以str1仅仅是从arr1堆内存中获取了一个数值，保存于栈内存中。str1是直接在栈中修改，并不能影响到arr1堆内存中的数据。

### 解释事件代理，事件流模型
JavaScript 中的事件流模型 事件冒泡 和 事件捕获，以及 事件委托（也叫事件代理）。
事件冒泡(event bubbling) 由内向外，即从 DOM 树的子到父，div -> body -> html -> document
事件捕获(event capturing) 由外向内，即从 DOM 树的父到子，document -> html -> body -> div

事件冒泡&事件捕获同时存在
原则:
从外向内，捕获前进，遇到捕获事件立即执行
非 target 节点，先捕获再冒泡
target 节点，按代码书写顺序执行（无论冒泡还是捕获）



addEventListener
在 JavaScript 中，addEventListener 方法用于向指定元素添加事件句柄。
语法：element.addEventListener(event, function, useCapture)

element	目标元素	
event	事件名，如 click
function	事件触发时执行的函数
useCapture	Bool值，true - 事件句柄在 捕获 阶段执行。false- false- 默认。事件句柄在 冒泡 阶段执行

useCapture 默认为false，事件冒泡阶段执行

冒泡举例：
```
<div class="t3">document
  <div class="t2">html
    <div class="t1">body
      <div class="t0">div</div>
    </div>
  </div>
</div>

/**.js**/
var $t0 = document.getElementsByClassName('t0')[0];
var $t1 = document.getElementsByClassName('t1')[0];
var $t2 = document.getElementsByClassName('t2')[0];
var $t3 = document.getElementsByClassName('t3')[0];

$t0.addEventListener("click", function(){
  alert("click div")
}, false);

$t1.addEventListener("click", function(){
  alert("click body")
}, false);

$t2.addEventListener("click", function(){
  alert("click html")
}, false);

$t3.addEventListener("click", function(){
  alert("click document")
}, false);

// click div -> click body -> click html -> click docuement
```

### 描述下js里面的事件流?为什么一般在冒泡阶段, 而不是在捕获阶段注册监听?  
DOM2级事件模型中规定了事件流的三个阶段：捕获阶段、目标阶段、冒泡阶段。

事件监听addEventListener()第三个参数为false时是设置DOM解析为事件冒泡，事件冒泡可以兼容IE早版本的浏览器。
IE8以前的浏览器只支持事件冒泡，不支持事件捕获，它也不支持addEventListener函数。
### 手写jsonp实现，发送和回调接收
```
 <script>
    /**
     * 手写jsonp并返回Promise对象
     * 参数url，data:json对象，callback函数
     */
    function jsonp(url, data = {}, callback = 'callback') {
      // 处理json对象，拼接url
      data.callback = callback
      let params = []
      for (let key in data) {
        params.push(key + '=' + data[key])
      }
      console.log(params.join('&'))
      // 创建script元素
      let script = document.createElement('script')
      script.src = url + '?' + params.join('&')
      document.body.appendChild(script)
      // 返回promise
      return new Promise((resolve, reject) => {
        window[callback] = (data) => {
          try {
            resolve(data)
          } catch (e) {
            reject(e)
          } finally {
            // 移除script元素
            script.parentNode.removeChild(script)
            console.log(script)
          }
        }
      })

    }
    jsonp('http://photo.sina.cn/aj/index', {
      page: 1,
      cate: 'recommend'
    }, 'jsonpcallback').then(data => {
      console.log(data)
    })
  </script>
```
需要注意：
1、创建script元素，设置src属性，并插入文档中，同时触发AJAX请求。
2、返回Promise对象，then函数才行继续，回调函数中进行数据处理
3、script元素删除清理

### 判断变量类型，如何判断变量是函数
在 JS 中，有 5 种基本数据类型和 1 种复杂数据类型，基本数据类型有：Undefined, Null, Boolean, Number和String；复杂数据类型是Object，Object中还细分了很多具体的类型，比如：Array, Function, Date等等。

总结：
 一般简单的使用 typeof 或 instanceof 检测（这两种检测的不完全准确）
完全准确的使用 原生js中的 Object.prototype.toString.call  或 jquery中的 $.type 检测。

```
var num  = 123;

var str  = 'abcdef';

var bool = true;

var arr  = [1, 2, 3, 4];

var json = {name:'wenzi', age:25};

var func = function(){ console.log('this is function'); }

var und  = undefined;

var nul  = null;

var date = new Date();

var reg  = /^[a-zA-Z]{5,20}$/;

var error= new Error();

console.log(

    typeof num,

    typeof str,

    typeof bool,

    typeof arr,

    typeof json,

    typeof func,

    typeof und,

    typeof nul,

    typeof date,

    typeof reg,

    typeof error

);
// number string boolean object object function undefined object object object object


console.log(

    num instanceof Number,

    str instanceof String,

    bool instanceof Boolean,

    arr instanceof Array,

    json instanceof Object,

    func instanceof Function,

    und instanceof Object,

    nul instanceof Object,

    date instanceof Date,

    reg instanceof RegExp,

    error instanceof Error

)
// num : false

// str : false

// bool : false

// arr : true

// json : true

// func : true

// und : false

// nul : false

// date : true

// reg : true

// error : true


console.log(

    Object.prototype.toString.call(num),

    Object.prototype.toString.call(str),

    Object.prototype.toString.call(bool),

    Object.prototype.toString.call(arr),

    Object.prototype.toString.call(json),

    Object.prototype.toString.call(func),

    Object.prototype.toString.call(und),

    Object.prototype.toString.call(nul),

    Object.prototype.toString.call(date),

    Object.prototype.toString.call(reg),

    Object.prototype.toString.call(error)

);

// '[object Number]' '[object String]' '[object Boolean]' '[object Array]' '[object Object]'

// '[object Function]' '[object Undefined]' '[object Null]' '[object Date]' '[object RegExp]' '[object Error]'
```

### 如何判断一个变量是数组
 var obj = { }
var arr = [];
//用constructor判断,arr和obj本身没有constructor属性,但它的原型上有

console.log(arr.constructor === Array)  //true
console.log(obj.constructor === Array); //false

//用instanceof判断

console.log(arr instanceof Array);   //true
console.log(obj instanceof Array);//false


//用toString方法
console.log(Object.prototype.toString.call(arr));   //true
console.log(Object.prototype.toString.call(obj));   //false

/用Array.isArray方法
console.log(Array.isArray(obj));   //false
console.log(Array.isArray(arr));   //true
### js实现css的:hover效果
在CSS中hover是指鼠标移入和移出两个事件，利用CSS实现这个效果非常的简单，可是如果放在JS中，我们就必须解析成两个事件：onmouseover和onmouseout。
```
<div class="header-mobile" id="headerMobile">
    <a href="">移动客户端</a>
    <ul class="header-mobile-list" id="mobileList">
    <li>新浪微博</li>
    <li>新浪新闻</li>
    <li>新浪体育</li>
    <li>新浪娱乐</li>
    <li>新浪财经</li>
    <li>天气通</li>
    </ul>
</div>
window.onload=function(){
    function $(id){
      return document.getElementById(id);
    }
//鼠标进
    $("headerMobile").onmouseover=function(){
      // this.style.display="none";
      $("mobileList").style.display="block"
      //给当钱的添加样式
      this.style.boxShadow=" 0 2px 2px gray"
    }
//鼠标出
    $("headerMobile").onmouseout=function(){
      $("mobileList").style.display="none"
      this.style.boxShadow='none'
    }
}
```
### 手写debouce(去抖)函数，节流函数。

debounce的特点是当事件快速连续不断触发时，动作只会执行一次。 延迟debounce，是在周期结束时执行，前缘debounce，是在周期开始时执行。但当触发有间断，且间断大于我们设定的时间间隔时，动作就会有多次执行。

```
  <button id="debounce">点我防抖！</button>

  <script>
    window.onload = function() {
      // 1、获取这个按钮，并绑定事件
      var myDebounce = document.getElementById("debounce");
      myDebounce.addEventListener("click", debounce(sayDebounce));
    }

    function debounce(fn) {
      // 4、创建一个标记用来存放定时器的返回值
       let timeout = null;
      return function() {
        // 5、每次当用户点击/输入的时候，把前一个定时器清除
        clearTimeout(timeout);
        // 6、然后创建一个新的 setTimeout，
        // 这样就能保证点击按钮后的 interval 间隔内
        // 如果用户还点击了的话，就不会执行 fn 函数
        timeout = setTimeout(() => {
          fn.call(this);
        }, 1000);
      };
    }

    function sayDebounce() {
      // ... 有些需要防抖的工作，在这里执行
      console.log("防抖成功！");
    }
    // 3、需要进行防抖的事件处理
```


throttling，节流的策略是，固定周期内，只执行一次动作，若有新事件触发，不执行。周期结束后，又有事件触发，开始新的周期。 节流策略也分前缘和延迟两种。与debounce类似，延迟是指 周期结束后执行动作，前缘是指执行动作后再开始周期。


```
 window.onload = function() {
      // 1、获取按钮，绑定点击事件
      var myThrottle = document.getElementById("throttle");
      myThrottle.addEventListener("click", throttle(sayThrottle));
    }

    // 2、节流函数体
    function throttle(fn) {
      // 4、通过闭包保存一个标记
      let canRun = true;
      return function() {
        // 5、在函数开头判断标志是否为 true，不为 true 则中断函数
        if(!canRun) {
          return;
        }
        // 6、将 canRun 设置为 false，防止执行之前再被执行
        canRun = false;
        // 7、定时器
        setTimeout( () => {
          fn.call(this, arguments);
          // 8、执行完事件（比如调用完接口）之后，重新将这个标志设置为 true
          canRun = true;
        }, 1000);
      };
    }

    // 3、需要节流的事件
    function sayThrottle() {
      console.log("节流成功！");
    }

```


###  apply的妙用

1.  什么情况下用apply,什么情况下用call

在给对象参数的情况下,如果参数的形式是数组的时候,比如apply示例里面传递了参数arguments,这个参数是数组类型,并且在调用Person的时候参数的列表是对应一致的(也就是Person和Student的参数列表前两位是一致的) 就可以采用 apply , 如果我的Person的参数列表是这样的(age,name),而Student的参数列表是(name,age,grade),这样就可以用call来实现了,也就是直接指定参数列表对应值的位置(Person.call(this,age,name,grade));

2.apply的一些其他巧妙用法

细心的人可能已经察觉到,在我调用apply方法的时候,第一个参数是对象(this), 第二个参数是一个数组集合, 在调用Person的时候,他需要的不是一个数组,但是为什么他给我一个数组我仍然可以将数组解析为一个一个的参数,这个就是apply的一个巧妙的用处,可以将一个数组默认的转换为一个参数列表([param1,param2,param3] 转换为 param1,param2,param3) 这个如果让我们用程序来实现将数组的每一个项,来装换为参数的列表,可能都得费一会功夫,借助apply的这点特性,所以就有了以下高效率的方法:

a)Math.max 可以实现得到数组中最大的一项

因为Math.max 参数里面不支持Math.max([param1,param2]) 也就是数组

但是它支持Math.max(param1,param2,param3…),所以可以根据刚才apply的那个特点来解决 var max=Math.max.apply(null,array),这样轻易的可以得到一个数组中最大的一项(apply会将一个数组装换为一个参数接一个参数的传递给方法)

这块在调用的时候第一个参数给了一个null,这个是因为没有对象去调用这个方法,我只需要用这个方法帮我运算,得到返回的结果就行,.所以直接传递了一个null过去

b)Math.min  可以实现得到数组中最小的一项

同样和 max是一个思想 var min=Math.min.apply(null,array);

c)Array.prototype.push 可以实现两个数组合并

同样push方法没有提供push一个数组,但是它提供了push(param1,param,…paramN) 所以同样也可以通过apply来装换一下这个数组,即:

```
 
vararr1=new Array("1","2","3");  
  
vararr2=new Array("4","5","6");  
  
Array.prototype.push.apply(arr1,arr2);  

```

也可以这样理解,arr1调用了push方法,参数是通过apply将数组装换为参数列表的集合.

通常在什么情况下,可以使用apply类似Math.min等之类的特殊用法:

一般在目标函数只需要n个参数列表,而不接收一个数组的形式（[param1[,param2[,…[,paramN]]]]）,可以通过apply的方式巧妙地解决这个问题!



