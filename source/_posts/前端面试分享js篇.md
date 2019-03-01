---
title: 前端面试分享js篇
date: 2019-02-22 14:30:06
tags:  [javascript，继承,promise,call,prototype,事情委托]
categories: 面试
---



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


###    Promise原理




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
###  彻底理解js中的&&和||

在javascript中：

以下内容会被当成false处理："" , false , 0 , null , undefined , NaN

其他都是true。注意：字符串"false"也会被当做true处理，在未转型的情况下他是字符串，属于一个对象，所以是true。


所以：

a || b：如果a是true，那么b不管是true还是false，都返回true。因此不用判断b了，这个时候刚好判断到a，因此返回a。

　　　如果a是false，那么就要判断b，如果b是true，那么返回true，如果b是false，返回false，其实不就是返回b了吗。

a && b：如果a是false，那么b不管是true还是false，都返回false，因此不用判断b了，这个时候刚好判断到a，因此返回a。

　　　如果a是true，那么就要在判断b，和刚刚一样，不管b是true是false，都返回b。

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



###    typeof和instanceof的区别



###    new和instanceof的内部机制


###    说一下你对generator的了解？

###    说一下macrotask 和 microtask？并说出下面代码的运行结果。

###    Http请求中的keep-alive有了解吗。

###    数组扁平化处理：实现一个flatten方法，使得输入一个数组，该数组里面的元素也可以是数组，该方法会输出一个扁平化的数组。

###    实现一个div滑动的动画，由快至慢5s结束（不准用css3)。

###   页面内有一个input输入框，实现在数组arr查询命中词并要求autocomplete效果。

###   实现超出整数存储范围的两个大整数相加function add(a,b)。注意a和b以及函数的返回值都是字符串。

###   深入理解TCP

###   浏览器事件有哪些过程? 为什么一般在冒泡阶段, 而不是在捕获阶段注册监听? addEventListener 参数分别是什么 ? 

###   移动端300ms延时的原因? 如何处理?

###  前端跨域方案

###  js浮点数运算不精确 如何解决?

###  new String('a') 和 'a' 是一样的么? 

###  DOM基础知识,添加元素,删除元素等等...

### DOM节点类型 

### 前端安全 xss 和 csrf

https://mp.weixin.qq.com/s/IROuODpzxv9nTtnKgMyvsg

xss 跨站脚本攻击，主要是前端层面的，用户在输入层面插入攻击脚本，改变页面的显示，或则窃取网站 cookie，预防方法：不相信用户的所有操作，对用户输入进行一个转义，不允许 js 对 cookie 的读写

csrf 跨站请求伪造，以你的名义，发送恶意请求，通过 cookie 加参数等形式过滤

我们没法彻底杜绝攻击，只能提高攻击门槛

### 闭包相关
### 简述事件流，事件代理

### js 二分查找

### 什么是栈
### 解释事件代理，事件流模型
### 数据统计，比ajax更简单的方法
### 手写jsonp实现，发送和回调接收
### 判断变量类型，如何判断变量是函数
### 如何判断一个变量是数组

### js实现css的:hover效果


### 手写debouce(去抖)函数