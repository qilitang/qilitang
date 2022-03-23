---
title: Vue环境配置及项目创建
date: 2019-02-05
comments: false
toc: true
categories: "Vue"
tags: ["Vue环境配置"]
---

# Vue 项目开发

## 环境: 安装vue 脚手架 cli

### 1: 下载安装 node.js

- 下载

```python
在官网:https://nodejs.org/zh-cn/ 下载并安装
```

- 安装 

  傻瓜式安装

- 测试是否安装成功

  在 cmd 界面输入node -v  出现以下结果标识node 安装成功

  ```bash
  C:\Users\zhang>node -v
  v12.15.0
  
  ```

### 2.更换npm源

在cmd 中输入以下命令换源

```bash
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

###  3.安装vue脚手架

```bash
cnpm install -g @vue/cli
```

**注意**:如果第二,三步出现异常,基本上是由于网络导致, 重新执行命令, 若一直出现错误, 可以清理缓存后重复

```bash
npm cache clean --force
```

## 项目创建

### 1. cd 到创建项目的文件夹

```bash
D:\Project>

```

### 2. 创建项目命令

```bash
vue create 项目名
```

![](https://gitee.com//qilitang/Img/raw/master/img/20200213200811.png)

![](https://gitee.com//qilitang/Img/raw/master/img/20200213195859.png)

选择自定义设置

![image-20200213195928203](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20200213195928203.png)

```bash
Babel：将ES6语法解析为ES5语法给浏览器
Router：前台路由
Vuex：前台仓库，相当于单例，完成个组件间传参的
TypeScript: 支持 typescript语法
```

![](https://gitee.com//qilitang/Img/raw/master/img/20200213200228.png)

选择y 可以使用历史记录路由

![](https://gitee.com//qilitang/Img/raw/master/img/20200213200400.png)

可以根据需求选择

![](https://gitee.com//qilitang/Img/raw/master/img/20200213200508.png)

需要下载很多东西, 所以需要等很久

创建完成

### 3. vue 项目 目录结构

```python
├── homework
|	├── node_modules  	// 当前项目所有依赖，一般不可以移植给其他电脑环境
|	├── public			
|	|	├── favicon.ico	// 标签图标
|	|	└── index.html	// 当前项目唯一的页面
|	├── src
|	|	├── assets		// 静态资源img、css、js
|	|	├── components	// 小组件
|	|	├── views		// 页面组件
|	|	├── App.vue		// 根组件
|	|	├── main.js		// 全局脚本文件（项目的入口）
|	|	├── router
|	|	|	└── index.js// 路由脚本文件（配置路由 url链接 与 页面组件的映射关系）
|	|	└── store	
|	|	|	└── index.js// 仓库脚本文件（vuex插件的配置文件，数据仓库）
|	├── README.md
└	└── package.json等配置文件
```

### 4. 项目移植

```python
1）拷贝出环境 node_modules 意外的文件与文件夹到目标文件夹
2）终端进入目标文件夹所在位置
3）执行：npm install 重新构建依赖（npm可以用cnpm替换）
```





### 5. pycharm 启动vue 项目

- 重启pycharm 让pycharm 找到本机的node.js 环境.( **首次安装环境使用**)

- pycharm 打开项目

  ![](https://gitee.com//qilitang/Img/raw/master/img/20200213201447.png)

- 配置 启动服务器

  ![](https://gitee.com//qilitang/Img/raw/master/img/20200213201743.png)

![](https://gitee.com//qilitang/Img/raw/master/img/20200213202117.png)

![](https://gitee.com//qilitang/Img/raw/master/img/20200213202139.png)



### 6. cmd 命令启动vue项目

![](https://gitee.com//qilitang/Img/raw/master/img/20200213201848.png)



### 7. pycharm 安装 vue 环境

![](https://gitee.com//qilitang/Img/raw/master/img/20200213202419.png)

