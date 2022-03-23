---
title: Vue第三方插件
date: 2019-02-09
comments: false
toc: true
categories: "Vue"
tags: ["Vue第三方插件"]
---



# Vue第三方插件

## 重点汇总

### vuex: 组件间交互的（移动端）

可以完成任意组件间信息交互（移动端） - 了解

```python
"""
1）vuex是实现任何组件间的信息交互的，可以理解为全局的一个单例，为任何一个组件共享vue仓库里的数据
2）在任何一个组件的逻辑里，都可以访问仓库
	i）现在仓库里(store/index.js)定义变量，用来存储共享数据
	state: {
        info: '共享数据的初始值'
    },
    ii）在组件逻辑中获取仓库值
    let 变量 = this.$store.state.info
    iii）在组件逻辑中更新仓库值
    this.$store.state.info = '新值'

注：vuex通常运用在开发移动端项目，pc端项目可以用localStorege和localStorege数据库来替换
原因：vuex中的数据，会在页面刷新后，重置到store/index.js配置的默认值
"""
```

### 前端储存数据大汇总(重点)

```python
"""
1）cookie：以字符串形式存储，数据有过期时间（过期时间到，数据失效吗，否则永远有效）

2）localStorage：以对象形式存储，数据永久保存

3）sessionStorage：以对象形式存储，生命周期同所属页面标签（页面不关闭，数据就有效）

4）vuex(store)：以对象形式存储，当页面刷新数据就重置（移动端不能刷新，所以只有应用大退才会重置）
"""
```

### 前后台交互方式(重点)

```python
"""
1）form表单方式
	i）get | post 两种请求方式，get请求包含直接在浏览器中输入url回车后发送的请求
	ii）该方式的特点是一定会发生页面的跳转（刷新页面叫本页跳转） - 后台决定页面路由
	
2）ajax异步方式
	i）get | post | patch | put | delete 等众多请求方式，请求的目的都是异步获取后台的数据
	ii）该方式的特点是不会刷新页面，只是得到新的数据，前台自己完成页面的局部刷新、整体刷新、页面跳转 - 前台决定页面路由
	
注：
i）前后台不分离项目，采用form表单请求，可以完成页面跳转，同步ajax异步请求完成页面局部刷新
ii）前后台分离项目，不采用form表单请求，页面刷新、页面跳转的请求，都是由ajax完成，只不过页面跳转，后台相应的是跳转的目标url，前台再自己完成跳转

iii）前后台分离项目，前台也会出现大量的form表单，但是form表单的提交按钮，走的不是form表单的submit提交，而是ajax请求
"""
```

## 同源策略- 跨域问题

```python
"""
一、django默认是同源策略，所以前后台分离项目，访问django会出现CORS跨域问题的报错

二、什么叫跨域
i）ip不同：前后台（两个服务器）不在一台主机上运行的
ii）port不同：前后台（两个服务器）是相互独立的，运行在不同的端口之上
iii）协议不同：http与https之间也同样是跨域问题
注：三者满足一个，就是跨域

三、解决跨域
i）伪装：将前台请求伪装成后台自己对自己发生的请求
ii）后台主动允许跨域：后台配置允许跨域即可（在响应头中处理）

四、Django解决跨域
i）安装模块：
	pip install django-cors-headers
ii）注册app：
INSTALLED_APPS = [
	...
	'corsheaders'
]
iii）添加中间件  注意中间件位置, 应该在第三位
MIDDLEWARE = [
	...
	'corsheaders.middleware.CorsMiddleware'
]
iv）允许跨域源
CORS_ORIGIN_ALLOW_ALL = True
v）请求头设置  一下位默认, 可以自己添加.
DEFAULT_HEADERS = (
    "accept",
    "accept-encoding",
    "authorization",
    "content-type",
    "dnt",
    "origin",
    "user-agent",
    "x-csrftoken",
    "x-requested-with",
)

"""
```





## axios插件: 异步请求

```python
"""
1）安装：在前端项目根目录下的终端
cnpm install axios

2）项目配置：main.js
import axios from 'axios'
Vue.prototype.$axios = axios;

3）在任何一个组件的逻辑中，都可以访问 this.$axios()
beforeMount() {
	// 请求后台
	this.$axios({
    	url: this.$settings.base_url + '/test/',
        method: 'delete',
    })
}
"""
```

## 



### 前后端 交互流程

```python
"""
1）启动前后台项目

2）前台配置页面路由，渲染前台页面 | 后台配置数据路由，响应数据（处理好跨域问题）

3）前台通过ajax请求后台接口
	i）将前台数据提交给后台
	ii）得到后台的响应结果
	iii）根据响应结果的数据，最后完成页面的局部刷新、整体刷新、页面跳转
"""
```

### 异步请求 axios 参数细节

```python
"""
1）vue框架用axios完成ajax异步请求
	语法：this.$axios().then().catch();
    解读：$axios()是请求逻辑 | then()是正常响应逻辑 | catch()是错误响应逻辑
    具体语法：
    this.$axios({
    	url: '后台接口链接',
    	method: '请求方式',
    	params: {},  // url拼接参数
    	data: {},  // 数据包参数
    	headers: {}  // 请求头参数
    }).then(response => {
    	// response是http状态2xx的响应结果，响应数据是response.data
    }).catch(error => {
    	// error是http状态4xx、5xx的响应结果，错误响应对象是error.response，错误响应数据是error.response.data
    })

2）前台提交数据的两种方式：
	i）url拼接参数：
		所有请求都拥有的提交数据的方式
		该方式会将数据都在请求url后用?拼接的方式提交给后台
		提交数据只能采用url字符串方式提交给后台，数据是不安全的
		axios插件可以用params属性携带url拼接参数
		
	ii）数据包参数：
		除get请求外的所有请求都拥有的提交数据的方式
		该方式会将数据进行加密，打包成数据包方式提交给后台
		打包加密数据有三种方式：urlencoded(form默认的方式)、form-data(可以提交文件)、json(提交json数据)
		axios插件可以用data属性携带数据包参数
		
"""

"""
注意项：
1）this.$axios({}).then(response => {}).catch(error => {}) 中的then和catch回调函数，不能写function，因为实际vue项目开发，一定会在回调逻辑用到this语法(代表vue对象)，而在function中使用了this语法，function就不是普通函数了(可以理解为类，this就不能代表vue对象了）

2）原生django没有提供所有类型的数据包数据解析规则，但是数据会在request.body中，可以自己解析；Django-rest-framework框架是提供了三种类型的数据包参数解析
"""
```

## element-ui 插件

```python
"""
element-ui就类似于BootStrap框架，前者是在vue项目中运用，后者是在原生项目中运用，当然也可以在vue项目中运用

环境搭建：
1）安装：在前端项目根目录下的终端
cnpm install element-ui

2）配置：main.js
import ElementUI from 'element-ui'
Vue.use(ElementUI);
import 'element-ui/lib/theme-chalk/index.css';

3）使用：根据视频和官方API接口
"""
```



## bootstrap+jQuery

```python
"""
一、jq环境搭建：
1）安装：在前端项目根目录下的终端
cnpm install jquery

2）配置：自己在项目根目录下新建 vue.config.js
const webpack = require("webpack");
module.exports = {
    configureWebpack: {
        plugins: [
            new webpack.ProvidePlugin({
                $: "jquery",
                jQuery: "jquery",
                "window.$": "jquery",
                "window.jQuery": "jquery",
                Popper: ["popper.js", "default"]
            })
        ]
 	}
};

二、bs环境搭建：
1）安装：在前端项目根目录下的终端
cnpm install bootstrap@3

2）配置：自己在项目根目录下新建 vue.config.js
import BootStrap from "bootstrap"
import "bootstrap/dist/css/bootstrap.css"
Vue.use(BootStrap)
"""
```

## Django 国际化

```python
"""
LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'
USE_I18N = True
USE_L10N = True
USE_TZ = False
"""
```

