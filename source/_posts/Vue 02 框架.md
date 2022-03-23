---
title: Vue框架简述
date: 2019-02-06
comments: false
toc: true
categories: "Vue"
tags: ["Vue框架简述"]
---



# Vue 框架

## vue 框架介绍

###  定义

```python
"""
渐进式javascript 框架
	渐进式: 可以控制一个标签, 也可以控制一系列的标签, 也可以控制整个页面, 甚至可以控制整个前端项目. 		
"""
```

### 优势

- 指令( 分支结构, 循环结构),  页面的复用
- 是实例成员(el, data, delimiters,...)  可以对渲染的数据做二次格式化
- 有组件(模块的复用或者组合), 快速搭建页面



- 虚拟DOM 
  - 虚拟DOM就是为了**解决浏览器性能问题**而被设计出来的
  - 页面的更新可以先全部反映在JS对象(虚拟DOM)上，操作内存中的JS对象的速度显然要更快，等更新完成后，再将最终的JS对象映射成真实的DOM，交由浏览器去绘制。

- 数据的双向绑定
- 单页面应用
- 数据驱动

### 为什么学vue

- 前台框架: Angular(庞大)、React（精通移动端）、Vue（吸取前两者优势，轻量级）
- vue 有一首的中文文档
- 实现前后台分离开发, 节约开发成本

### 如何使用vue

- 很简单的使用

## vue环境的搭建

###  方式1: CDN

```html
<script src="https://cn.vuejs.org/js/vue.js"></script>
```

### 方式2: 本地导入

在本地下载后 使用 script 导入

```html
<script src="js/vue.js"></script>
```

## Vue 基础语法

### 实例成员

#### 挂载点 el

```html
<!--
1: 挂载点只能控制一个页面结构(优先匹配到的结构)
2: 挂载点后 的页面标签建议使用id属性匹配, 一般习惯用id="app"
3: html标签与body 标签不能作为挂载点. 
4: 根据需求选择是否需要用变量接受vue对象
-->

<div id="app">
    <p>{{ num }}</p><script>
    new Vue({
        el: "#app",
        data:{
            num:"231"
        }
    })
</script>
</div><script>
    new Vue({
        el: "#app",
        data:{
            num:"231"
        }
    })
</script>

<script>
    new Vue({
        el: "#app",
        data:{
            dada:"32131"
        }
    })
</script>


```

#### 插值表达式

```python
"""
知识点:
	1: 插值表达式标识: {{ }}
	2: 需要渲染的变量 在data实例成员中初始化
	3: 插值表达式可以进行简单的逻辑运算(+, *, .length, split())
    4: 更改插值表达式标识 delimiters: ["[[","]]"]
"""
```

```html
<div id="app">
    <p> {{ num }}</p>
    <a href=""> {{ url }}</a>
    <span> {{ msg }}</span>

    <!-- 加法运算 -->
    <p>{{ num + 10 }}</p>
    <!-- 乘法运算 -->
    <p>{{ num + 10 * 100 }}</p>

    <!-- 长度统计+计算-->
    <p>{{ msg.length + num }}</p>

    <!-- 索引取值  -->
    <p>{{ msg[3] }}</p>
    <!--    切片-->
    <p>{{ msg.split("")[3] }}</p>
    <!--更换标识符-->
    <p>[[ num ]]</p>
</div>

<script>
    new Vue({
        el: "#app",
        data: {
            num: 123,
            url: "这是url",
            msg: "我是你爸爸"
        },
        delimiters: ["[[","]]"]
    })
</script>

```

#### 过滤器

```python
"""
知识点: 
	1: 用实例成员filters 来定义过滤器
	2: 在页面结构中 使用 | 来标识过滤器
	3: 过滤方法的返回值 就是过滤器过滤后的结果
	4: 过滤器可以对1个或多个变量进行过滤, 同时 还可以传入辅助变量
		过滤器的传参是按照位置先后顺序进行传参
"""
```

```html
<body>
    <div id="app">
        <!-- 简单使用：过滤的对象会作为参数传给过滤器 -->
        <p>{{ num | add(20) }}</p>
        <!-- 串联使用：将第一个过滤器结果作为参数给第二个过滤器 -->
        <p>{{ num | add(100) | jump(2) }}</p>
        <!-- 究极使用 -->
        <p>{{ n1, n2 | fn(99, 77) }}</p>
        <!-- 你品，你细品 -->
        <p>{{ n1, n2 | fn(99, 77), n1, n2 | fn(100) }}</p>
    </div>
</body>
<script src="js/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            num: 10,
            n1: 66,
            n2: 88
        },
        filters: {
            add: function (a, b) {
                console.log(a, b);
                return a + b;
            },
            jump: function (a, b) {
                return a * b
            },
            fn: function (a, b, c, d) {
                console.log(a, b, c, d);
                return a + b + c + d;
            }
        }
    })
</script>
```

#### 计算属性

```python
"""
/** 计算属性：
* 1）其实就是vue中的方法属性，方法名可以作为属性来使用，属性值为方法的返回值
* 2）在computed中声明的方法属性，不能在data中重复声明，比data中声明的属性要多出写逻辑的地方
* 3）方法属性，自带监听机制，在方法属性中出现的变量，都会被监听，一旦有任何被监听的变量值发生更新，
*      方法属性都会被调用更新方法属性的值
* 4）方法属性一定要在页面中渲染一次，方法属性采用意义，多次渲染，方法属性只会被调用一次
* 案例：计算器
* 方法属性的应用场景：一个变量依赖于多个变量，且需要进行一定的逻辑运算
*/
"""
```

```html

<div id="app">
    <!-- type="number"表示只能写数字 -->
    <input type="number" v-model="num1" max="100" min="0">
    +
    <input type="number" v-model="num2" max="100" min="0">
    =
    <button>{{ sum }}</button>
</div>
<script>
    new Vue({
        el: '#app',
        data: {
            // sum: '',  // 重复声明
            num1: '',
            num2: '',
        },
        computed: {
            sum () {
                // num1和num2都在该方法属性中，所以有一个更新值，该方法都会被调用
                if (this.num1 && this.num2) {
                    return +this.num1 + +this.num2;  // +this.num1是将字符串快速转换澄数字
                }
                return '结果';
            }
        }
    })
</script>
```





### 指令

`v-*` 是vue指令, 会被vue解析,  `v-text`="name" 中 name是变量

#### 文本指令(v-text)

```python
"""
1: 语法:  v-text="name"  name是变量
2: v-text 是输出文本内容,   标签内部的文本内容会被name变量替换掉
3: v-html 是可以渲染html 语法的内容
"""

```

```html
<body>
    <div id="app">
        <p v-text="name"></p>
        <p v-text="name">123</p>
        <p v-text="info"></p>
        <p v-html="info"></p>
    </div>
</body>
<script src="js/vue.js"></script>
<script>
    new Vue({
        el: '#app',
        data: {
            name: "张三",
            info: '<i style="color: red">info内容</i>'
        }
    })
</script>
```

#### 事件指令(v-on )

```python
"""
 一、数据驱动
   1: 操作是一个功能，使用需要一个方法来控制 
   2: 方法名是变量，所以控制变量就可以控制该方法

 二、事件指令
   1: 在实例成员methods中声明事件方法
   2: 标签通过事件指令绑定声明的方法： v-on:事件名="事件方法名"
       <button v-on:click="btnClick">按钮</button>
   3: 标签通过事件指令绑定声明的方法，且自定义传参： v-on:事件名="事件方法名()"
       <button v-on:click="btnClick()">按钮</button>  不传任何参数
       <button v-on:click="btnClick($event)">按钮</button>  传入事件对象，同不写()
       <button v-on:click="btnClick(10)">按钮</button>  只传入自定义参数，当然也可以传入事件对象
   4: 可简写为 @
"""
```



```html
<body>
    <div id="app">
        <button v-on:click="btnClick">{{ btn1 }}</button>

        <button v-on:click="btnClick">{{ btn2 }}</button>
        <hr>

        <!-- 直接绑定事件名：系统会在触发事件时(点击时)调用事件方法(fn1)，传给事件方法一个参数(事件对象) -->
        <button v-on:click="fn1">按钮3</button>

        <!-- 绑定的事件名后跟着()，不是主动调用事件方法，而是表示在触发事件调用时，传入的参数全由用户自己决定 -->
        <button v-on:click="fn2($event, 10, 20)">按钮4</button>

        <hr>
        <button v-on:click="fn(btn1)">{{ btn1 }}</button>

        <button v-on:click="fn(btn2)">{{ btn2 }}</button>
    </div>
</body>
<script src="js/vue.js"></script>
<script>
    // 对比DOM驱动：1）js选择器获取目标标签 2）为目标标签绑定事件 3）在事件中完成相应逻辑
    // var btn = document.getElementsByTagName('button')[0];
    // btn.onclick = function () {
    //     console.log(111111111111);
    // };
    new Vue({
        el: '#app',
        data: {
            btn1: '按钮1',
            btn2: '按钮2',
        },
        methods: {
            btnClick () {
                console.log(666)
            },
            fn1 (ev) {
               console.log(ev.clientX, ev.clientY);
            },
            fn2(ev, n1, n2) {
                console.log(ev, n1, n2);
                console.log(ev.clientX, ev.clientY);
            },
            fn (msg) {
                console.log(msg);
            }
        }
    })
</script>
```

#### 属性指令(v-bind)

```python
"""
1: 语法  v-bind:属性名="变量"
2: 针对不通的属性, 使用方式不一样
	i: 自定义属性和单值属性,(类似 title,type), 直接用变量赋值.
	ii: class 属性(重点)
		绑定变量: 值可以是一个类名"p1", 也可以是多个类名的中间空格隔开的字符串"p1 p2"
		绑定数组: 数组中的每一个成员都是一个变量, 进而都是一个类名
		绑定字典: key 就是类名,  value是决定该类是否起作用
	iii: style属性(了解)
		绑定变量: 值是一个字典. 
		
3: 可以简写为:

"""
```

- 绑定变量

```vue
<p v-bind:title="t" v-bind:owen="'o'">段落</p>
<script>
    new Vue({
        el: '#app',
        data: {
            t: '悬浮提示',
        },
    })
</script>
```

- 绑定数组,字典

```vue
<!-- 
a是变量，值就是类名
b就是类名，不是变量
c是变量，值为布尔，决定b类是否起作用
d是变量，值可以为一个类名 'p1' 也可以为多个类名 "p1 p2 ..."
calss="p1 b p2 p3"
-->
<p v-bind:class="[a, {b: c}]" v-bind:class="d"></p> 
<script>
    let app = new Vue({
        el: '#app',
        data: {
            a: 'p1',
            c: true,
            d: 'p2 p3',
        },
    })
</script>
```

- style

```vue
<p v-bind:style="myStyle"></p>
<script>
    let app = new Vue({
        el: '#app',
        data: {
            myStyle: {
                width: '50px',
                height: '50px',
                backgroundColor: 'pink',
                borderRadius: '50%'
            }
        },
    })
</script>
```

- 案例

```vue
<button v-bind:class="{live: isLive == 1}" v-on:click="changeLive(1)">1</button>
<button v-bind:class="{live: isLive == 2}" v-on:click="changeLive(2)">2</button>
<button v-bind:class="{live: isLive == 3}" v-on:click="changeLive(3)">3</button>
<script>
    let app = new Vue({
        el: '#app',
        data: {
            isLive: 1,
        },
        methods: {
            changeLive (index) {
                // this就代表当前vue对象，和app变量等价
                // app.isLive = index;
                this.isLive = index;
            }
        }
    })
</script>  
```

- 事件指令和属性指令都可以简写

```html
<!--
1）v-bind: 可以简写为 :
2）v-on: 可以简写为 @
-->

<button v-bind:class="{live: isLive == 1}" v-on:click="changeLive(1)">1</button>
<button :class="{live: isLive == 2}" @click="changeLive(2)">2</button>
<button :class="{live: isLive == 3}" @click="changeLive(3)">3</button>
```

##### 事件补充

```html
<style>
    body {
        /* 不允许文本选中 */
        user-select: none;
    }
    .d1:hover {
        color: orange;
        /* 鼠标样式 */
        cursor: pointer;
    }
    /* 只有按下采用样式，抬起就没了 */
    .d1:active {
        color: red;
    }
    /* div标签压根不支持 :visited 伪类 */
    .d1:visited {
        color: pink;
    }

    .d2.c1 {
        color: orange;
    }
    .d2.c2 {
        color: red;
    }
    .d2.c3 {
        color: pink;
    }
</style>
<div id="app">
    <div class="d1">伪类操作</div>
    <br><br><br>
    <!--
    click: 单击
    dblclick：双击
    mouseover：悬浮
    mouseout：离开
    mousedown：按下
    mouseup：抬起
    -->
    <div :class="['d2', c]" @click="hFn('c1')" @mouseover="hFn('c2')" @mousedown="hFn('c3')">事件处理</div>
</div>
<script>
    new Vue({
        el: '#app',
        data: {
            c: '',
        },
        methods: {
            hFn (c) {
                this.c = c
            }
        }
    })
</script>

```



#### 表单指令(v-model)

```python
"""
1: 语法  v-mode="变量"
2: v-model 绑定的变量控制的就是 value属性
3: v-model 要比v-bind:value 多了一个监听机制
4: 数据的双向绑定:
	v-model 可以将绑定的变量值映射给表单元素 value
	v-model  还可以将表单元素新的 value 映射给绑定的变量 即赋值后随时监听value
"""
```

```html
<!-- 两个输入框内容会同时变化 -->
<input name="n1" type="text" v-model="v1">
<input name="n2" type="text" v-model="v1">
<script>
    new Vue({
        el: '#app',
        data: {
            v1: ''
        }
    })
</script>
```

#### 条件指令

```python
"""
1: 语法  v-show="变量" | v-if="变量"
2: 区别
	v-show 是在css层面进行标签的隐藏, 当变量值为false时 采用display:none的方式渲染
	v-if  在隐藏标签时 是不会渲染标签
	
3: v-if 是由家族的:  v-if | v-else-if | v-else
	v-if  是必须的, 必须设置条件
	v-else-if 可以有多个,  后面必须有条件(变量)
	v-else 可有可无
	
	以上分支只能有一个生效
"""
```

```html
<div id="app">
    <div>
        <p v-show="isShow">show控制显隐</p>
        <p v-if="isShow">if控制显隐</p>
    </div>

    <div>
        <p v-if="0">你是第1个p</p>
        <p v-else-if="0">你是第2个p</p>
        <p v-else>你是第3个p</p>
    </div>

</div>
<script>
    new Vue({
        el: '#app',
        data: {
            isShow: false,
        }
    })
</script>
```

##### 案例

```html
<style>
    body {
        margin: 0
    }
    button {
        width: 60px;
        line-height: 40px;
        float: right;
    }
    .bGroup:after {
        display: block;
        content: '';
        clear: both;
    }
    .box {
        /* vw: view width  vh: view height*/
        width: 100vw;
        height: 200px;
    }
    .red {
        background-color: red;
    }
    .green {
        background-color: green;
    }
    .blue {
        background-color: blue;
    }

    button.active {
        background-color: cyan;
    }
</style>

<div id="app">
    <div class="bGroup">
        <button :class="{active: isShow === 'red'}" @click="isShow = 'red'">红</button>
        <button :class="{active: isShow === 'green'}" @click="isShow = 'green'">绿</button>
        <button :class="{active: isShow === 'blue'}" @click="isShow = 'blue'">蓝</button>
    </div>
    <div>
        <div v-if="isShow === 'red'" class="box red"></div>
        <div v-else-if="isShow === 'green'" class="box green"></div>
        <div v-else class="box blue"></div>
    </div>
</div>

<script>
    new Vue({
        el: '#app',
        data: {
            isShow: 'red'
        }
    })
</script>
```

#### 循环指令(v-for)

```python
"""
1: 语法 v-for="i in obj"  obj 是被遍历对象. i 是遍历后得到的每一次结果
2: 特点
	obj 是可迭代对象
	遍历不仅可以得到数据还可以得到索引
	  字符串：v-for="v in str"  |  v-for="(v, i) in str"
      数组：v-for="v in arr"  |  v-for="(v, i) in arr"
      对象：v-for="v in obj"  |  v-for="(v, k) in obj"  |  v-for="(v, k, i) in obj"
注：v-for遍历要依赖于一个所属标签，该标签及内部所有内容会被遍历复用
"""
```



```html
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <title>循环指令</title>
</head>
<body>
    <div id="app">
        <!-- 遍历数字
		5
		【1】【2】【3】【4】【5】
		-->
        <p>{{ d1 }}</p>
        <i v-for="e in d1">【{{ e }}】</i>
        <hr>

        <!-- 遍历字符串
		abc
		【a】【b】【c】
		【0a】【1b】【2c】
		-->
        <p>{{ d2 }}</p>
        <i v-for="e in d2">【{{ e }}】</i>
        <i v-for="(e, i) in d2">【{{ i }}{{ e }}】</i>
        <hr>

        <!-- 遍历数组
		[ 1, 3, 5 ]
		【1】【3】【5】
		【01】【13】【25】
		-->
        <p>{{ d3 }}</p>
        <i v-for="e in d3">【{{ e }}】</i>
        <i v-for="(e, i) in d3">【{{ i }}{{ e }}】</i>
        <hr>

        <!-- 遍历对象
		{ "name": "Bob", "age": 17.5, "gender": "男" }
		【Bob】【17.5】【男】
		【name-Bob】【age-17.5】【gender-男】
		【name-Bob-0】【age-17.5-1】【gender-男-2】
		-->
        <p>{{ d4 }}</p>
        <i v-for="e in d4">【{{ e }}】</i>
        <i v-for="(e, k) in d4">【{{ k }}-{{ e }}】</i>
        <i v-for="(e, k, i) in d4">【{{ k }}-{{ e }}-{{ i }}】</i>
        <hr>

    </div>
</body>
<script>
    new Vue({
        el: '#app',
        data: {
            d1: 5,
            d2: 'abc',
            d3: [1, 3, 5],
            d4: {
                name: "Bob",
                age: 17.5,
                gender: "男"
            }
        }
    })
</script>
```

##### 商品循环案例

```html
<style>
    .box {
        width: 280px;
        border: 1px solid #eee;
        border-radius: 5px;
        overflow: hidden; /* 隐藏超出父级显示范围外的内容 */
        text-align: center; /* 文本相关的属性大多默认值是inherit */
        float: left;
        margin: 10px;
    }
    .box img {
        width: 100%;
    }
</style>

<div id="app">
    <div class="box" v-for="obj in goods">
        <img :src="obj.img" alt="">
        <p>{{ obj.title }}</p>
    </div>
</div>

<script>
    let goods = [
        {
            "img": "https://***1.jpg",
            "title": "纯种拆家专家1"
        },
        {
            "img": "https://***2.jpg",
            "title": "纯种拆家专家2"
        },
    ];
    
    new Vue({
        el: '#app',
        data: {
            goods,
        }
    })
</script>
```

### 面试题

#### js的Array操作

```python
"""
尾增：arr.push(ele)  
首增：arr.unshift(ele)
尾删：arr.pop()
首删：arr.shift()
增删改插：arr.splice(begin_index, count, args)
"""
```

#### 前台数据库

```python
"""
// 存
// 持久化化存储，永远保存
localStorage.name = "Bob";
// 持久化化存储，生命周期同所属标签(页面)，页面关闭，重新打开就会丢失
sessionStorage.name = "Tom";

// 取
console.log(localStorage.name);
console.log(sessionStorage.name);

// 清空
localStorage.clear();
sessionStorage.clear();

// 短板：只能存储字符串，所有对象和数组需要转换为json类型字符串，再进行存储
let a = [1, 2, 3];
localStorage.arr = JSON.stringify(a);
let b = JSON.parse(localStorage.arr);
console.log(b);
"""
```

#### 留言板

```html
<style>
    li:hover {
        color: red;
        cursor: pointer;
    }
</style>

<div id="app">
    <form>
        <input type="text" v-model="info">
        <button type="button" @click="sendInfo">留言</button>
    </form>
    <ul>
        <li v-for="(info, index) in info_arr" @click="deleteInfo(index)">{{ info }}</li>
    </ul>
</div>

<script>
    new Vue({
        el: '#app',
        data: {
            info: '',
            // 三目运算符： 条件 ? 结果1 : 结果2
            info_arr: localStorage.info_arr ? JSON.parse(localStorage.info_arr) : [],
        },
        methods: {
            sendInfo () {
                // 完成留言：将info添加到info_arr
                // 增 push unshift | 删 pop shift
                if (this.info) {
                    // 留言
                    this.info_arr.push(this.info);
                    // 清空输入框
                    this.info = '';
                    // 前台数据持久化(缓存)
                    localStorage.info_arr = JSON.stringify(this.info_arr);
                }
            },
            deleteInfo(index) {
                // 删
                this.info_arr.splice(index, 1);
                // 同步给数据库
                localStorage.info_arr = JSON.stringify(this.info_arr);
            }
        }
    })
</script>
```









