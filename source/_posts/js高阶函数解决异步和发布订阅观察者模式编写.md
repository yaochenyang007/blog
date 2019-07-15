---
title: 'js高阶函数解决异步和发布订阅观察者模式编写'
date: 2019-07-15 10:46:42
tags: [js]
categories: js
---

### 解决异步并发问题 计数器

异步代码会等待同步代码执行完，分别读取到name和age 属性后拿到最后的结果。
<!-- more -->
 - name.txt 文件
``` txt
ycy
```
 - age.txt 文件
``` txt
24
```

```
let fs = require('fs');
function after(times,callback){
    let person = {}
    return function out(key,value){
        person[key] = value;

        if(--times == 0){
            callback();
        }
    }
}

let out = after(2,(person)=>{
    console.log(person)
})


fs.readFile('./name.text','utf-8',function(err,data){
    out('name',data)
})

fs.readFile('./age.text','utf-8',function(err,data){
    out('age',data)
})


//打印  {name:'ycy',age:'24'}

```



###  发布者和订阅者

发布 emit 和 订阅 on 
希望两次异步请求都完成后 一次打印最终结果

```
let fs = require('fs')

class Events{
    construtor(){
        this.stack =[]
    }
    on(callback){
        this.stack.push(callback);
    }
    emit(callback){
        this.stack.forEach(callback=>{
            callback();
        })
    }
}
let events = new Events();
let school ={};

events.on(function(){
    if(Object.keys(school).length ==2){
        console.log(school);
    }
})


fs.readFile('./name.text','utf-8',function(err,data){
    shool.name = data;
    events.emit();
})

fs.readFile('./age.text','utf-8',function(err,data){
    shool.age = data;
    events.emit();
})

```


###  观察者模式

 - 把观察者放到被观察者中
```
class Subject{ //被观察者
    constructor(){
        this.stack = [];
        this.state = "AirPods"
    }
    attach(observer){  
        this.stack.push(observer)
    }
    setstate(newstate){
       this.state = newstate;
       this.stack.forEach(o=>{
           o.update(newstate);
       })
    }
}

class Observer{ //观察者
    constructor(name){
        this.name = name;
    }
    update(newstate){
        console.log(this.name + " for ycy" + " " + newstate)
    }
}
let xgw = new Observer('xgw'); 
let xgp = new Observer('xgp');
let  me  = new Subject();
me.attach(xgw); 
me.attach(xgp);
me.setstate('ipads')


//xgw for ycy ipads
//xgp for ycy ipads


```


### 总结
观察者它是基于发布订阅模式的，发布和订阅两者没有直接关系。 



