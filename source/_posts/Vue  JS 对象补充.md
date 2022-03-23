---
title: Js 对象补充
date: 2019-02-03
comments: false
toc: true
categories: "Vue"
tags: ["Js对象"]
---



# JS 对象补充

## 普通对象与对象简写

```js
// 1: js中没有字典类型, 只有自定义对象. 对象完全可以代替字典来使用
var dic_obj = {
    // 属性：值（数值，函数）
    'name': 'Bob',
    'eat': function () {
        console.log('在吃饭')
    }
};
console.log(dic_obj.name, dic_obj['name']);
dic_obj.eat(); dic_obj['eat']();

// 2: js中对象的属性名,都采用字符串类型, 所以就可以省略到字符串的引号标识
// 3: 对象总属性为函数式, 称之为方法,  方法建议简写: 方法名 () {}
var obj = {
    name: 'Tom',  // 属性名简写
    eat () {     // 方法名简写
        console.log('在吃饭...')
    }
};
console.log(obj.name, obj['name']);
obj.eat(); obj['eat']()

// 如果对象的属性值是一个变量, 并且 变量名和属性名相同 可以简写:{属性,}


var height = 180;
var p = {
    height,  //属性简写
    name: 'Jerry',
    eat() {}
};
console.log(p.name, p.height);

```

##  js 声明类

###  第一种声明类方式

```js
// 第一种方式
class People {
    constructor(name){    // 类似 django的 __init__(name)
        this.name = name
    }
    eat () {
        console.log(this.name + "在跑步")
}
    
    let p1 = new People('Bob');
    let p2 = new People('Tom');
    console.log(p1.name, p2.name);
    p1.eat();
```

### 第二种类(了解)

```js
// 第二种声明类的方法(难点)：在函数内部出现了this语法，该函数就是类，否则就是普通函数
function Teacher(name) {
    this.name = name;
    this.eat =function () {
        console.log(this.name + '在吃饭')
    }
}
let t1 = new Teacher('Tank');
t1.eat();
```

### 类属性的添加

```js
// 类属性：给类属性赋值，所有对象都能访问
function Fn() {}
let f1 = new Fn();
let f2 = new Fn();

// 赋值类属性
Fn.prototype.num = 100;

console.log(f1.num);
console.log(f2.num);

// 类似于单例
Vue.prototype.num = 1000;
let v1 = new Vue();
let v2 = new Vue();
console.log(v1.num);
console.log(v2.num);
```

js 函数补充

```js
// 类属性：给类属性赋值，所有对象都能访问
function Fn() {}
let f1 = new Fn();
let f2 = new Fn();

// 赋值类属性
Fn.prototype.num = 100;

console.log(f1.num);
console.log(f2.num);

// 类似于单例
Vue.prototype.num = 1000;
let v1 = new Vue();
let v2 = new Vue();
console.log(v1.num);
console.log(v2.num);
```

## JS的Array操作

```js
"""
尾增：arr.push(ele)  
首增：arr.unshift(ele)
尾删：arr.pop()
首删：arr.shift()
增删改插：arr.splice(begin_index, count, args)
"""
```



