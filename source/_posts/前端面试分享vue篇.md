---
title: 前端面试分享vue篇
date: 2019-02-28 17:48:19
tags: [vue]
categories: 面试
---
### vue的如何实现双向绑定的 ？
vue数据双向绑定是通过数据劫持结合发布者-订阅者模式的方式来实现的。
思路分析
实现mvvm主要包含两个方面，数据变化更新视图，视图变化更新数据：
关键点在于data如何更新view，因为view更新data其实可以通过事件监听即可，比如input标签监听 'input' 事件就可以实现了。所以我们着重来分析下，当数据改变，如何更新视图的。
数据 ===》  更新视图
通过Object.defineProperty( )对属性设置一个set函数，当数据改变了就会来触发这个函数，所以我们只要将一些需要更新的方法放在这里面就可以实现data更新view了。
1.实现一个监听器Observer，用来劫持并监听所有属性，如果有变动的，就通知订阅者。

2.实现一个订阅者Watcher，可以收到属性的变化通知并执行相应的函数，从而更新视图。

3.实现一个解析器Compile，可以扫描和解析每个节点的相关指令，并根据初始化模板数据以及初始化相应的订阅器。
### vue的 nextTick 方法实现原理？
nextTick:
在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

Vue 是异步执行 DOM 更新的。
（1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
（4）主线程不断重复上面的第三步。
```
//改变数据
vm.message = 'changed'

//想要立即使用更新后的DOM。这样不行，因为设置message后DOM还没有更新
console.log(vm.$el.textContent) // 并不会得到'changed'

//这样可以，nextTick里面的代码会在DOM更新后执行
Vue.nextTick(function(){
    console.log(vm.$el.textContent) //可以得到'changed'
})
```

### vue的 组件之间的通讯？

父子组件通讯 props,this.$emit('Fn()', Data);


### vuex 状态管理？

store基本上就是一个容器，它包含着你的应用中大部分的状态 (state)。Vuex 和单纯的全局对象有以下两点不同：

Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。


你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

store.js(主容器)
```
import Vue from 'vue'
import Vuex from 'vuex'
import * as actions from './actions.js'
import * as getters from './getters.js'
import * as mutations from './mutations.js'

Vue.use(Vuex);
export default new Vuex.Store({
  state: {
    //人员身份选项
    identityOptions: [],
    curActiveDoorId: "",  //indexMain.vue组件里的菜单，当前所在的菜单项(当前门禁id)
    isStopLoadRecord: false,  //fristUrl.vue组件里的右侧实施菜单刷新控件，是否停止加载
    doorList:[],  //门禁点列表
    userInfo: {
      name:"",  //用户名
    }
  },
  getters,
  actions,
  mutations
})
```
getters(计算属性)
```
export const identityOptionAll = state => {
  let identityOptionAll = Array.isArray(state.identityOptions) ? state.identityOptions : [];
  identityOptionAll.unshift({identityName:"全部", identity:""})
  return identityOptionAll;
}
```


actions.js(计算属性[支持异步])
```
import http from '@/assets/js/API.js'
import url from '@/assets/js/URL.js'

export const load_IdentityOptions = ({ commit }) => {

  //获取身份选项
  http.methods.apiGetHttp2(url.IDENTITY_LIST_WITH_DOOR_DEVICE).then(
    data => {
      commit({
        type: 'f_setIdentityOptions',
        data: data
      })
    },
    ({errorMsg}) => {
      console.log(errorMsg)
    }
  )

}
```


mutations.js(更改 Vuex 的 store 中的状态的唯一方法是提交 mutation)

```
//设置身份选项
export const f_setIdentityOptions = (state, payload) => {
  state.identityOptions = payload.data;
}

//设置当前门禁id
export const f_setCurActiveDoorId = (state, payload) => {
  state.curActiveDoorId = payload.id;
}
```

Module（分模块）
```
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```