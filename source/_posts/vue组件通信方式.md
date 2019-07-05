---
title: vue组件通信方式
date: 2019-06-27 12:05:28
tags: [vue]
categories:  vue
---
### vue组件通信方式


#### 父子通信 

父向子传递数据是通过 props，子向父是通过 events（ $emit）；通过父链 / 子链也可以通信（ $parent / $children）；
ref 也可以访问组件实例


#####  props 和 events

父组件A通过props的方式向子组件B传递，B to A 通过在 B 组件中 $emit, A 组件中 v-on 的方式实现。
父向子传递数据是通过 props，子向父是通过 events（ $emit）

##### $children　＆ ref 

ref：如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素；如果用在子组件上，引用就指向组件实例

$parent / $children：访问父 / 子实例


#### 兄弟通信： Bus；Vuex ;
<!--- more --->

##### Bus 
```
var Event = new Vue();
    
Event.$emit(事件名,数据);
    
Event.$on(事件名,data => {});

```
##### Vuex

Vuex实现了一个单向数据流，在全局拥有一个State存放数据，当组件要更改State中的数据时，必须通过Mutation进行，Mutation同时提供了订阅者模式供外部插件调用获取State数据的更新。而当所有异步操作(常见于调用后端接口异步获取更新数据)或批量的同步操作需要走Action，但Action也是无法直接修改State的，还是需要通过Mutation来修改State的数据。最后，根据State的变化，渲染到视图上。


 dispatch：操作行为触发方法，是唯一能执行action的方法。

 actions：操作行为处理模块,由组件中的 $store.dispatch('action 名称',data1)来触发。然后由commit()来触发mutation的调用 , 间接更新 state。负责处理Vue Components接收到的所有交互行为。包含同步/异步操作，支持多个同名方法，按照注册的顺序依次触发。向后台API请求的操作就在这个模块中进行，包括触发其他action以及提交mutation的操作。该模块提供了Promise的封装，以支持action的链式触发。

 commit：状态改变提交操作方法。对mutation进行提交，是唯一能执行mutation的方法。

 mutations：状态改变操作方法，由actions中的 commit('mutation 名称')来触发。是Vuex修改state的唯一推荐方法。该方法只能进行同步操作，且方法名只能全局唯一。操作之中会有一些hook暴露出来，以进行state的监控等。

 state：页面状态管理容器对象。集中存储Vue components中data对象的零散数据，全局唯一，以进行统一的状态管理。页面显示所需的数据从该对象中进行读取，利用Vue的细粒度数据响应机制来进行高效的状态更新。

 getters：state对象读取方法。图中没有单独列出该模块，应该被包含在了render中，Vue Components通过该方法读取全局state对象。



####  跨级通信： Bus；Vuex；provide / inject API、 $attrs/$listeners

#####  $attrs　/ $listeners

 $attrs：包含了父作用域中不被 prop 所识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，
 这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件。
 通常配合 interitAttrs 选项一起使用。


$listeners：包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件




#####  provide / inject API

祖先组件中通过provider来提供变量，然后在子孙组件中通过inject来注入变量。provide / inject API 主要解决了跨级组件间的通信问题，不过它的使用场景，主要是子组件获取上级组件的状态，跨级组件间建立了一种主动提供与依赖注入的关系。



<!-- https://segmentfault.com/a/1190000016627804 -->