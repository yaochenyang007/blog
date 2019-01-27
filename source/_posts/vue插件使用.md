---
title: vue插件使用
date: 2018-10-10 15:35:00
tags: [vue]
categories: vue
---
## Loading 加载 插件开发

通过全局方法 Vue.use() 使用插件。它需要在你调用 new Vue() 启动应用之前完成：

```
// 调用 `MyPlugin.install(Vue)`
Vue.use(MyPlugin)

new Vue({
  //... options
})

```

代码源码（vue-cli 脚手架生成单页面项目）：

@/components/public/varMess.vue 文件

```
<template>
    <transition name="varMess-fade">
            <div class="varMess" v-show="isShow">
                    <img class="message_img" src="../img/loading.png">
                    <div class="message_group">
                        <p>{{messText}}</p>
                        <!-- <el-button @click="isShow = false">123</el-button> -->
                    </div>
            </div>
    </transition>
</template>

<script>
    export default {
        props:['isShow','messText'],
        methods:{

        }

    }
</script>
```
<!--- more --->
@/components/public/index.js 文件

```
import varMess from './varMess.vue'
// 这里是重点
const VarMess = {
    install: function(Vue) {
        Vue.component('varMess', varMess)
    }
}

// 导出组件
export default VarMess
```


main.js文件  注册插件
```
import varMess from '@/components/public/varMess';
Vue.use(varMess);
```

使用插件：

```
  <varMess :isShow="isShow" messText="点位加载中，请稍后..."></varMess>
```
显示效果：

![本地图片](/img/loading_active.png  "loading")