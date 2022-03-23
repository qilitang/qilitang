---
title: Vue框架概览
date: 2019-02-07
comments: false
toc: true
categories: "Vue"
tags: ["Vue框架概览"]
---



# Vue 框架概览



## vue组件(.vue 文件)

```python
"""
1.  一个vue文件就是一个组件
2.  组件是由3部分构成, html结构, js 逻辑, css 样式
	html结构:  在vue 组件中的 template 标签中. 此标签结构中 有且只能有一个跟标签
	js逻辑  :  在vue 组件中的 script 标签中. 必须设置导出, 才能让组件中相互使用
	css样式 :  在vue 组件中的 style 标签中, 必须设置 scoped 属性, 让样式组件化, 避免其他组件样式冲突 
"""
```

```html
<template>
    <div class="index">
        <h1>主页组件</h1>
    </div>
</template>
<script>
    export default {
        name: "index"
    }
</script>
<style scoped>
    /* scoped可以使样式组件化，只在自己内部起作用 */
</style>
```



### 全局脚本文件 main.js(项目入口)

```python
"""
1. main.js 是项目的入口文件(类似django的 manage.py文件)
2. new Vue() 是创建根组件, 用 render 读取一个.vue 文件, $mount渲染替换 index.html 中的站位
3. 项目所依赖的环境, 比如: vue, 路由, 仓库, 第三方, 自定义都是在main.js 中完成的
"""
```

```js
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

Vue.config.productionTip = false

new Vue({
    router,
    store,
    render: h => h(App)
}).$mount('#app')
```



**改写**

```js
import Vue from 'vue'  // 加载vue环境
import App from './App.vue'  // 加载根组件
import router from './router'  // 加载路由环境
import store from './store'   // 加载数据仓库环境
/*
* import  别名 from "路径"
* @在vue 项目中指代 src文件夹的绝对路径
*/

Vue.config.productionTip = false  // tips小提示
// 文件可以写.vue 后缀, 也可以不写.vue 后缀
import index from "@/views/index.vue"
new Vue({
    el: "#app",
  router: router,
  store: store,
  render: function (loadVue){
      return loadVue(index)
  }
});

```

### App.vue  项目根组件

```python
"""
1: App.vue 是项目的根组件, 是唯一由main.js 加载渲染的组件, 就是替换 index.html 中<div id="app"></div>占位标签的
2: 实际开发中App.vue 文件 只需要写下方5hang代码即可(可以增加全局样式代码等)
3: <router-view/> 是一个占位标签, 由 router插件控制, 可以在router的配置文件中进行配置
4: <router-view/> 就是根据 router 在 index.js 中配置的路由关系, 被制定路径 匹配制定的页面组件渲染
	<router-view/>被不通的页面组件替换 形成了页面跳转
"""
```

```html

<template>
    <div id="app">
        <!-- 前台路由占位标签，末尾的/代表单标签的结束 -->
        <router-view />
    </div>
</template>

<style>
    body {
        margin: 0;
    }
</style>
```

### 路由配置

```python
"""
1: 当用户在浏览器中访问的路由是"/", router插件就会加载Home.vue, 同理访问"/first" 就会加载 First.vue
2: 被加载的 Home.vue 或者First.vue 替换 App.vue 中的<router-view/>占位符
3: 用redirect 配置实现重定向
"""
```

```js
const routes = [
    {
        path: '/',
        name: 'Home',
        component: Home
    },
    {
        path: '/first',
        name: 'First',
        component: First
    },
    {
        path: '/second',
        name: 'Second',
        component: Second
    },
]
// 配置是否使用历史记录
const router = new VueRouter({
    mode: 'history',
    base: process.env.BASE_URL,
    routes
})
```

###  路由跳转

```python
"""
router控制的路由，不是用a标签完成跳转：
 1: a标签会刷新页面，错误的
 2: router-link标签也能完成跳转，且不会刷新页面，就是router提供的a标签(最终会被解析为a标签，还是用a来控制样式)
 3: router-link标签的to属性控制跳转路径，由两种方式
    i) to="路径字符串"
    ii) :to="{name: '路由名'}"
"""
```



```html
<template>
    <div class="nav">
        <img src="" />
        <ul>
            <li>
                <a href="/">主页</a>
            </li>
            <li>
                <router-link to="/about">关于页</router-link>
            </li>
            <li>
                <!-- to="字符串"，v-bind:to="变量"，可以简写为 :to="变量" -->
                <router-link :to="{name: 'First'}">第一页</router-link>
            </li>
        </ul>
    </div>
</template>

<style scoped>
    .nav {
        width: 100%;
        height: 80px;
        background: rgba(0, 0, 0, 0.3);
    }
    img {
        width: 200px;
        height: 80px;
        background: tan;
        float: left;
    }
    ul {
        float: left;
        list-style: none;
        margin: 0;
        padding: 0;
        height: 80px;
        background: pink;
    }
    ul li {
        float: left;
        height: 80px;
        padding: 30px 20px 0;
    }
    a {
        color: black;
    }
</style>
```

