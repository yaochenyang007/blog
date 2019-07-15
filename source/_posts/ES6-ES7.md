---
title: 'ES6,ES7'
date: 2019-03-06 18:21:11
tags: [ES6,ES7]
categories: ES6
---
### Promise原理

### 说一下你对generator的了解？

语法上，可以把理解成，Generator 函数是一个状态机，封装了多个内部状态。

形式上，Generator 函数是一个普通函数。

整个Generator函数就是一个封装的异步任务，或者说是异步任务的容器，异步操作需要暂停的地方，都用yield语句。

Generator函数特征：
<!--- more --->
（1）function 关键字和函数之间有一个星号(*),且内部使用yield表达式，定义不同的内部状态。

（2）调用Generator函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象。

```
function* fn(){   // 定义一个Generator函数
    yield 'hello';
    yield 'world';
    return 'end';
}
var f1 =fn();           // 调用Generator函数
console.log(f1);        // fn {[[GeneratorStatus]]: "suspended"}
console.log(f1.next()); // {value: "hello", done: false}
console.log(f1.next()); // {value: "world", done: false}
console.log(f1.next()); // {value: "end", done: true}
console.log(f1.next()); // {value: undefined, done: true}
```
但是，调用Generator函数后，函数并不执行，返回的也不是函数执行后的结果，而是一个指向内部状态的指针对象。

下一步，必须调用遍历器对象的next方法，使得指针移向下一个状态。即：每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。

Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。

Generator函数的暂停执行的效果，意味着可以把异步操作写在yield语句里面，等到调用next方法时再往后执行。这实际上等同于不需要写回调函数了，因为异步操作的后续操作可以放在yield语句下面，反正要等到调用next方法时再执行。所以，Generator函数的一个重要实际意义就是用来处理异步操作，改写回调函数。

### ES6 扩展运算符...使用

1.通过push函数，将一个数组添加到另一个数组的尾部。
```
// ES5的 写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
Array.prototype.push.apply(arr1, arr2);

// ES6 的写法
let arr1 = [0, 1, 2];
let arr2 = [3, 4, 5];
arr1.push(...arr2);

```
上面代码的 ES5 写法中，push方法的参数不能是数组，所以只好通过apply方法变通使用push方法。有了扩展运算符，就可以直接将数组传入push方法。
2.将一个数组转为用逗号分隔的参数序列。

```
function push(array, ...items) {
  array.push(...items);
}

function add(x, y) {
  return x + y;
}

const numbers = [4, 38];
add(...numbers) // 42
```

3.复制数组

（1）复制数组

数组是复合的数据类型，直接复制的话，只是复制了指向底层数据结构的指针，而不是克隆一个全新的数组。
```
const a1 = [1, 2];
const a2 = a1;

a2[0] = 2;
a1 // [2, 2]
```
上面代码中，a2并不是a1的克隆，而是指向同一份数据的另一个指针。修改a2，会直接导致a1的变化。

ES5 只能用变通方法来复制数组。
```
const a1 = [1, 2];
const a2 = a1.concat();

a2[0] = 2;
a1 // [1, 2]
```
上面代码中，a1会返回原数组的克隆，再修改a2就不会对a1产生影响。

扩展运算符提供了复制数组的简便写法。
```
const a1 = [1, 2];
// 写法一
const a2 = [...a1];
// 写法二
const [...a2] = a1;
```
上面的两种写法，a2都是a1的克隆。

（2）合并数组

扩展运算符提供了数组合并的新写法。
```
const arr1 = ['a', 'b'];
const arr2 = ['c'];
const arr3 = ['d', 'e'];

// ES5 的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]

// ES6 的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]

```
不过，这两种方法都是浅拷贝，使用的时候需要注意。   

```
const a1 = [{ foo: 1 }];
const a2 = [{ bar: 2 }];

const a3 = a1.concat(a2);
const a4 = [...a1, ...a2];

a3[0] === a1[0] // true
a4[0] === a1[0] // true
```
上面代码中，a3和a4是用两种不同方法合并而成的新数组，但是它们的成员都是对原数组成员的引用，这就是浅拷贝。如果修改了原数组的成员，会同步反映到新数组。

（3）与解构赋值结合

扩展运算符可以与解构赋值结合起来，用于生成数组。

```
var list = [1,2,3]
// ES5
a = list[0]; 
rest = list.slice(1)  //    2,3
// ES6
[a, ...rest] = list  //2,3

```


### ES6  startsWith,includes,endsWith

- includes(searchvalue, start)：返回布尔值，表示是否找到了参数字符串。

- startsWith(searchvalue, start)：返回布尔值，表示参数字符串是否在原字符串的头部。

- endsWith(searchvalue, start)：返回布尔值，表示参数字符串是否在原字符串的尾部。

参数说明：
searchvalue	必需，要查找的字符串。

start	可选，查找的开始位置，默认为 0。