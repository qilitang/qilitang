---
title: Vue详解
date: 2019-02-08
comments: false
toc: true
categories: "Vue"
tags: ["Vue详解"]
---



# Vue 项目初始化

## 项目初始化

### 根组件

```html
<!--  删除不必要的 -->
<template>
    <div id="app">
        <router-view />
    </div>
</template>
```

### 路由配置

```js
const routes = [
    {
        path: '/',
        name: 'Home',
        component: Home
    }
];
```

### 组件整理

```html
i）删除除Home.vue以为的所有组件
	ii）初始化Home.vue
	<template>
        <div class="home">
        </div>
    </template>
```

### 分类资源管理

```html
建立img、css、js子文件夹，删除原来的资源
```

### 修改页面标签图标

```html
替换public文件夹下的favicon.ico图片文件
```

### 全局配置自定义css 和js

#### global.css

```css
html, body {
    margin: 0;
}

a {
    color: black;
    text-decoration: none;
}

ul {
    margin: 0;
    padding: 0;
}
```

#### 配置后台base_url

```js
export default {
    base_url: 'https://127.0.0.1:8000'
}
```

#### 导入 main.js

```js
//1) 配置全局css
import '@/assets/css/global.css'
// import global_css from '@/assets/css/global.css'  // 资源需要用变量保存,方便以后使用
// require('@/assets/css/global.css')
// let global_css = require('@/assets/css/global.css')  // 资源需要用变量保存,方便以后使用


// 2) 配置自定义js设置文件
import settings from '@/assets/js/settings.js'
Vue.prototype.$settings = settings;
// 在任何一个组件中的逻辑,可以通过 this.$settings访问settings.js文件的{}数据
```







## 组件数据局部化处理

```python
"""
1）不管页面组件还是小组件，都可能会被多次复用
2）复用组件的原因，其实就是复用组件的 页面结构、页面样式、页面逻辑
3）但是页面上的数据需要区分（被复用的两个组件数据多少是有区别的），所以组件的数据要做局部化处理
4）借助函数可以产生局部作用域的特点，为每一次复用组件产生一个独立的作用域
语法:
data () {
	return {
		// 数据们
	}
}
"""
```

### 子组件

```html
<template>
    <div class="beat" @click="count += 1">
        {{ count }}下
    </div>
</template>

<script>
    export default {
        name: "Beat",
        // 不管是页面组件还是小组件，都可能被复用，页面结构与样式都可以采用一套，但是数据一定要相互独立
        data () {
            return {
                count: 0
            }
        }
    }
</script>

<style scoped>
    .beat {
        width: 100px;
        height: 100px;
        background-color: orange;
        text-align: center;
        line-height: 100px;
        border-radius: 50%;
    }
</style>


```

### 父组件

```html
<template>
    <div class="home">
        <Beat/>
        <Beat/>
    </div>
</template>
<script>
    import Beat from '@/components/Beat'
    export default {
        components: {
            Beat,
        }
    }
</script>
```



## 逻辑跳转

```python
"""
1）很多时候，我们需要通过普通按钮的逻辑，或是直接在某些逻辑中完成页面的跳转
2）可以通过在逻辑中用 this.$router.push() 来完成前往目标页，两种语法如下
	this.$router.push('路径')
	this.$router.push({name: '路由名'})
3）在做移动端项目时，没有像浏览器那样的前进后台键，页可以用 this.$router.go() 来完成前进后退，语法如下
	前进后退：this.$router.go(正负整数)，正式代表前进，负数代表后台，数值就是步长
"""
```

### 案例

```html
<template>
    <div class="home">
        <Nav/>
        <h1>主页</h1>
        <button @click="goPage('/first')">前往第一页</button>
        |
        <button @click="goPage('/second')">前往第二页</button>
        |
        <button @click="goBack(-1)">后退一页</button>
        |
        <button @click="goBack(-2)">后退二页</button>
        |
        <button @click="goBack(1)">前进一页</button>
    </div>
</template>

<script>
    import Nav from '@/components/Nav'

    export default {
        methods: {
            goPage(path) {
                // 可以通过 this.$router 完成逻辑跳转
                this.$router.push();
            },
            goBack(num) {
                // 一般在移动端项目上运用
                this.$router.go(num);  
            }
        },
        components: {
            Nav,
        }
    }
</script>
```

## 组件传参

### 父传子

```python
"""
一、组件传参 - 父传子
1）在子组件内部通过props设置组件的自定义属性
    props: ['abc', 'goods']
2）在父组件渲染子组件是对自定义属性赋值即可
    <GoodsBox v-for="goods in goods_list" :abc="goods" :goods="goods"/>
"""
```

#### 子组件

```html
<template>
    <div class="goods-box">
        <img :src="goods.img" alt="">
        <p>{{ goods.title }}</p>
    </div>
</template>

<script>
    export default {
        name: "GoodsBox",
        // 在组件内部通过props定义组件的自定义属性
        props: ['abc', 'goods'],
    }
</script>

<style scoped>
    .goods-box {
        width: 260px;
        height: 300px;
        border: 1px solid black;
        border-radius: 5px;
        margin: 20px;
        float: left;
        overflow: hidden;
        text-align: center;
    }
    img {
        width: 260px;
        height: 260px;
    }
</style>
```



#### 父组件

```html
<template>
    <div class="goods">
        <div class="main">
            <!-- 在使用子组件是对自定义属性赋值即可 -->
            <GoodsBox v-for="goods in goods_list" :abc="goods" :goods="goods" />
        </div>
    </div>
</template>
<script>
    import GoodsBox from "../components/GoodsBox";

    let goods_list = [
        {
            img: require('@/assets/img/001.jpg'),
            title: '小猫',
        },
        {
            img: require('@/assets/img/002.jpg'),
            title: '小猫儿',
        },
        {
            img: require('@/assets/img/003.jpg'),
            title: '小狗',
        },
        {
            img: require('@/assets/img/004.jpg'),
            title: '小狗儿',
        },
    ];

    export default {
        name: "Goods",
        data () {
            return {
                goods_list,
            }
        },
        components: {
            GoodsBox,
        },
    }
</script>


```

### 子传父

```python
"""
二、组件传参 - 子传父
前提：子组件是被父组件渲染的，所以子组件渲染要晚于父组件
1）子组件一定要满足一个条件，才能对父组件进行传参（某个时间节点 === 某个被激活的方法）
    eg：i）子组件刚刚加载成功，给父组件传参 ii）子组件某一个按钮被点击的时刻，给父组件传参 iii）子组件要被销毁了，给父组件传参

2）在子组件满足条件激活子组件的方法中，对父组件发生一个通知，并将数据携带处理（自定义组件事件）
    <div class="goods-box" @click="boxClick"></div>
    methods: {
        boxClick () { this.$emit('receiveData', this.goods.title, '第二个数据', '第三个数据') }
    }

3）在父组件渲染子组件时，为自定义事件绑定方法
    <GoodsBox @receiveData="recFn"/>

4）在父组件实现绑定方法时，就可以拿到子组件传参的内容（接收到了通知并在父组件中相应）
    recFn(title, data2, data3) {
        console.log('接收到了' + title);
    }

组件标签不能绑定系统定义的事件，没有意义，子组件的事件都是在自己内部完成
"""
```

#### 子组件

```html
<template>
    <div class="goods-box" @click="boxClick">
        <img :src="goods.img" alt="">
        <p>{{ goods.title }}</p>
    </div>
</template>

<script>
    export default {
        props: ['abc', 'goods'],
        methods: {
            boxClick () {
                // 通知父级 - 自定义组件的事件
                this.$emit('receiveData', this.goods.title)
            }
        }
    }
</script>
```

#### 父组件

```html
<template>
    <div class="goods">
        <div class="main">
            <!-- 实现自定义事件，接收子组件通知的参数 -->
            <GoodsBox v-for="goods in goods_list" @receiveData="recFn"/>
        </div>
    </div>
</template>
<script>
    import GoodsBox from "../components/GoodsBox";
    export default {
        name: "Goods",
        data () {
            return {
                goodsTitle: '哪个',
            }
        },
        methods: {
            recFn(title) {
                console.log('接收到了' + title);
                this.goodsTitle = title;
            }
        },
        components: {
            GoodsBox,
        },
    }
</script>


```

## 组件的生命周期钩子

```python
"""
一、组件的生命周期：一个组件从创建到销毁的整个过程
二、生命周期钩子：在一个组件生命周期中，会有很多特殊的时间节点，且往往会在特定的时间节点完成一定的逻辑，特殊的事件节点可以绑定钩子
注：钩子 - 提前为某个事件绑定方法，当满足这个事件激活条件时，方法就会被调用 | 满足特点条件被回调的绑定方法就称之为钩子
"""
```

```html
<template>
    <div class="goods">
        <Nav />
    </div>
</template>
<script>
    import Nav from "../components/Nav";
    export default {
        name: "Goods",
        components: {
            Nav,
        },
        beforeCreate() {
            console.log('该组件要被加载了')
        },
        created() {
            console.log('该组件要被加载成功了')
        },
        updated() {
            console.log('数据更新了')
        },
        destroyed() {
            console.log('该组件销毁了')
        }
    }
</script>


```

## 路由传参

### 第一种 

#### 路由配置

```js
const routes = [
    {
        path: '/goods/detail/:pk',
        name: 'GoodsDetail',
        component: GoodsDetail
    },
]
```

#### 传递组件

```html
<router-link class="goods-box" :to="`/goods/detail/${goods.pk}`">
    <img :src="goods.img" alt="">
    <p>{{ goods.title }}</p>
</router-link>

<!------------------- 或者 ------------------->

<div class="goods-box" @click="goDetail(goods.pk)">
    <img :src="goods.img" alt="">
    <p>{{ goods.title }}</p>
</div>
<script>
    export default {
        name: "GoodsBox",
        methods: {
            goDetail (pk) {
                this.$router.push(`/goods/detail/${pk}`);
            }
        }
    }
</script>
```



#### 接收组件

```html
<script>
    export default {
        name: "GoodsDetail",
        data () {
            return {
                pk: '未知',
            }
        },
        // 通常都是在钩子中获取路由传递的参数
        created() {
            this.pk = this.$route.params.pk || this.$route.query.pk;
        }
    }
</script>
```

### 第二种

#### 路由配置

```js
const routes = [
    {
        path: '/goods/detail',
        name: 'GoodsDetail',
        component: GoodsDetail
    },
]
```

#### 传递组件

```html
<router-link class="goods-box" :to="`/goods/detail?pk=${goods.pk}`">
    <img :src="goods.img" alt="">
    <p>{{ goods.title }}</p>
</router-link>

<!------------------- 或者 ------------------->

<div class="goods-box" @click="goDetail(goods.pk)">
    <img :src="goods.img" alt="">
    <p>{{ goods.title }}</p>
</div>
<script>
    export default {
        name: "GoodsBox",
        methods: {
            goDetail (pk) {
                // this.$router.push(`/goods/detail?pk=${goods.pk}`);
                
                // 或者
                this.$router.push({
                    name: 'GoodsDetail',
                    query: {
                        pk,
                    }
                });
            }
        }
    }
</script>
```

#### 接收组件

```html
<script>
    export default {
        name: "GoodsDetail",
        data () {
            return {
                pk: '未知',
            }
        },
        // 通常都是在钩子中获取路由传递的参数
        created() {
            this.pk = this.$route.params.pk || this.$route.query.pk;
        }
    }
</script>
```

