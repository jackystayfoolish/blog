---
layout: post
title: Javascript设计模式
category: JavaScript
---

* 目录
{:toc}

# 面向对象
## 环境搭建
### npm初始化
`node -v`
`npm -v`
`npm init`

### webpack  webpack-dev-server
#### 安装依赖
https://npm.taobao.org/
`npm install webpack webpack-cli --save-dev --registry=https://registry.npm.taobao.org`
`npm install webpack-dev-server html-webpack-plugin --save-dev --registry=https://registry.npm.taobao.org`
#### 源码
init/simplest.js
init/simplest.html
webpack.dev.config.js

注意将webpack.dev.config.js中的入口文件和模板文件改为响应的文件
#### 效果
`npm run dev`
html中包含了打包好的bundle.js,修改simplest.js会自动刷新

### babel解析es6
#### 安装依赖
`npm install babel-core babel-loader@7 babel-polyfill babel-preset-es2015 babel-preset-latest --save-dev --registry=https://registry.npm.taobao.org`
#### 源码
.babelrc
init/es6.html
init/es6.js


## 什么是面向对象
### 继承
#### 源码
oo/extends.js

### 封装
#### 源码
oo/encapsulation-ts.js
粘贴到
http://www.typescriptlang.org/play/

### js应用举例
#### 源码
oo/js-example.js
oo/js-example.html

## UML类图
https://www.processon.com/diagraming/5bffff70e4b034239805ca0e
oo/extends.js

# 设计原则
## 设计的五大原则
S-单一职责
O-开放封闭
L-李氏置换
I-接口独立
D-依赖倒置

## 设计模式
创建型
组合型
行为型

# 工厂模式
## 介绍
将newc操作单独封装

传统类图
![](/blog/assets/jsdesign/factory.png)

js类图
![](/blog/assets/jsdesign/factory1.png)

## 代码
pattern/factory.js

## 场景
jquery中$('div')
书写简单
一旦jQuery名字发生变化不受影响

# 单例模式
## 介绍
系统中被唯一使用，一个类又有一个实例
比如登录框、购物车

传统类图
![](/blog/assets/jsdesign/singleton.png)
![](/blog/assets/jsdesign/singleton2.png)

## 代码
pattern/singleton.js
由于js不支持private，用变通的方式
即靠约定不要直接使用SingleObject的构造函数，定义静态方法getInstance,使用闭包返回对象

## 场景
jquery
```
//jquery中只有一个$
if(window.jQuery!=null){
    return window.jQuery
}else{
    //初始化...
}
```

vuex和redux中的store

# 适配器模式
## 介绍
旧接口格式和使用者不兼容
中间加入一个适配转换接口

传统类图
![](/blog/assets/jsdesign/adapter.png)

js类图
![](/blog/assets/jsdesign/adapter2.png)

## 代码
pattern/adapter.js

## 场景
封装旧接口

vue computed
![](/blog/assets/jsdesign/adapter3.png)

# 装饰器模式
## 介绍
为对象添加新功能
不改变原有的结构和功能

传统类图
![](/blog/assets/jsdesign/decorator.png)

js类图
![](/blog/assets/jsdesign/decorator2.png)

## 代码
pattern/decorator.js

## 场景
ES7装饰器

环境配置
`npm install babel-plugin-transform-decorators-legacy --save-dev --registry=https://registry.npm.taobao.org`
在.babelrc中增加
`"plugins":["transform-decorators-legacy"]`

### 测试装饰器
源码位置
decorator-demo.js
```
//装饰器的原理
@decorator
class A {}
//等同于
class A {}
A=decorator(A||A;
```

### 装饰类
decorator-minxins.js

### 装饰方法
decorator-readonly.js
decorator-log.js

### 装饰器类库core-decorators
安装
`npm install core-decorators --save-dev --registry=https://registry.npm.taobao.org`






