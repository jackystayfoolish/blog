---
layout: post
title: Javascript基础和常见问题
category: JavaScript
---

* 目录
{:toc}

git源码
https://github.com/jackystayfoolish/js-question-basic.git
# 变量类型和计算

## 问题
- JS中typeof能得到哪些类型
```
typeof undefined//undifined
typeof 'abc'//string
typeof 123 //123
typeof true//boolean
typeof {}//object
typeof []//object
typeof null//object
typeof console.log//function
```
- 何时使用===何时使用==
使用==只有如下一种情况，其余都用===
```
if(obj.a==null){
    //这里相当于obj.a===null或者obj.a===undefined,减写形式
    //这时Jquery源码推荐写法
}
```
- JS中有哪些内置函数
Object
Array
Boolean
Number
String
Function
Date
RegExp
Error

- JS按照存储方式区分为哪些类型，并描述其特点
值类型
引用类型

- 如何理解JSON
JSON是一个js对象
JSON.stringify({a:10})
JSON.parse('{"a":10}')

## 变量类型
### 值类型
```
var a=100
b=a
a=200
console.log(b)//100
```
### 引用类型
对象、数组、函数
```
var a={age:10}
var b=a
b.age=15
console.log(a.age)//15
```

### typeof运算符
```
typeof undefined//undifined
typeof 'abc'//string
typeof 123 //123
typeof true//boolean
typeof {}//object
typeof []//object
typeof null//object
typeof console.log//function
```

## 变量计算-强制类型转换
### 字符串拼接
`var b=100+'10'//10010`
### ==运算符
```
100=='100'//true
0==''//true(0和''都会转化为false)
null==undefined//true(null和undefined都会转化为false)
```
### if语句
```
var a=true
if(a){
    //执行
}
var b=100
if(b){
    //执行
}
var c=''
if(c){
    //不执行
}
```
### 逻辑运算
```
console.log(10&&0)//0(10会转换为true)
console.log(''||'abc')//abc(''会转换为false)
console.log(!window.abc)//true
//判断一个变量会被当做true还是false
var a=100
console.log(!!a)
```
# 原型和原型链

## 问题
- 如何准确判断一个变量是数组类型
```
var array=[]
console.log(arr instanceof Array)//true
typeof array;//object,typeof是无法判断数组的
```
- 写一个原型链继承的例子
```
function Animal(){
    this.eat=function(){
        console.log('animal eat')
    }
}
function Dog(){
    this.bark=function(){
        console.log('bark')
    }
}
Dog.prototype=new Animal()
var dog=new Dog()
dog.eat()
```
```
function Elem(id){
    this.elem=document.getElementById(id)
}
Elem.prototype.html=function(val){
    var elem=this.elem
    if(val){
        elem.innerHTML=val
        return this
    }else{
        return elem.innerHTML
    }
}
Elem.prototype.on=function(type,fn){
    var elem=this.elem
    elem.addEventListener(type,fn)
    return this
}
var elem=new Elem('topnav-wrapper')
elem.html('<div>hi</div>').on('click',function(){
    console.log(123)
})
```
- 描述new一个对象的过程
1. 创建一个对象
1. this指向这个对象
1. 执行代码,即对this赋值
1. 返回this

- zepto(或其他框架)源码中如何使用原型链

## 构造函数
```
function Foo(name,age){
    //this={} //默认有这一行
    this.name=name
    this.age=age
    //return this //默认有这一行
}
var f=new Foo('zhangsan',15)
```
this变为空对象
this属性赋值
返回this


var a={}是var a=new Object()语法糖
var a=[]是var a=new Array()语法糖
function Foo(){}是var Foo=new Function(){}
使用instanceof判断一个函数是否是一个变量的构造函数

## 原型规则
```
//所有引用类型(数组、对象、函数),都具有对象特性，即可以自由扩展属性（除了"null"以外）
var obj={};obj.a=100;
var arr=[];arr.a=100;
function fn(){}
fn.a=100;
//所有引用类型(数组、对象、函数),都有一个__proto__属性（隐式原型）,属性值是一个普通对象
console.log(obj.__proto__);
console.log(arr.__proto__);
console.log(fn.__proto__);
//所有的函数,都有一个prototype属性(显式原型),属性值是一个普通对象
console.log(fn.prototype);
//所有引用类型(数组、对象、函数),__proto__属性指向它的构造函数的“prototype”属性
console.log(obj.__proto__===Object.prototype)
```
当试图得到一个对象的某个属性时,如果这个对象本身没有这个属性,那么会去他的__proto__(即它的构造函数的prototype)中找

```
function Foo(name){
    this.name=name
}
Foo.prototype.alertName=function(){
    alert(this.name)
}
var f=new Foo('zhangsan')
f.printName=function(){
    console.log(this.name)
}
//f.alertName以及f.printName的this都是f
f.printName()
f.alertName()
//遍历对象自身的属性
var item
for(item in f){
    //高级浏览器已经在for in中屏蔽了来自原型的属性
    //但是还是建议加上这个判断，保持程序的健壮性
    if(f.hasOwnProperty(item)){
        console.log(item)
    }
}
```
## 原型链
```
function Foo(name){
    this.name=name
}
Foo.prototype.alertName=function(){
    alert(this.name)
}
var f=new Foo('zhangsan')
f.printName=function(){
    console.log(this.name)
}
f.printName()
f.alertName()
f.toString()//要去f.__proto__.__proto__中查找
```
![](/blog/assets/jsinterviews/proto.png)

## instanceof
用于判断引用类型属于哪个构造函数的方法
f.instanceof Foo的判断逻辑是:
f.__proto__一层层往上,能否对应到Foo.prototype

# 作用域和闭包
## 问题
- 说一下对变量提升的理解
在全局范围(每个script标签内),变量定义、函数声明(不同于函数表达式)会提升到最前面
在函数范围,变量定义、函数声明、this、arguments会提升到最前面

- 说明this几种不同的使用场景
- 创建10个a标签，点击的时候弹出对应的序号
```
//错误写法
var i
for(i=0;i<10;i++){
   var a=document.createElement('a')
    a.innerHTML=i+'<br>'
    a.addEventListener('click',function(){
        alert(i)
    })
    document.body.append(a)
}
//正确写法
var i
for(i=0;i<10;i++){
    (function(i){
        var a=document.createElement('a')
        a.innerHTML=i+'<br>'
        a.addEventListener('click',function(){
            alert(i)
        })
        document.body.append(a)
    })(i)
}
```
- 如何理解作用域
自由变量、作用域链(即自由变量的查找)、闭包的两个场景

- 实际开发中闭包的应用
```
//闭包实际应用中主要用于封装变量，收敛权限
function isFirstLoad(){
    var _list=[]
    return function(id){
        if(_list.indexOf(id)>=0){
            return false
        }else{
            _list.push(id)
            return true
        }
    }
}
var isFirstLoad=isFirstLoad()
isFirstLoad(10)//true
isFirstLoad(10)//false
```

## 执行上下文
在全局范围(每个script标签内),变量定义、函数声明(不同于函数表达式)会提升到最前面
在函数范围,变量定义、函数声明、this、arguments会提升到最前面

```
console.log(a)//undefined
var a=100//变量定义会提升到最前面，即var a=undefined
fn('zhangsan')
function fn(name){
    age=20
    console.log(name,age)
    var age
}
```
## this
this要在执行时才能确认,定义时无法确认
```
var a={
    name:'A',
    fn:function(){
        console.log(this.name)
    }
}
a.fn()//this===a
a.fn.call({name:'B'})//this==={name:'B'}
fn1=a.fn
fn1()//this===window
```
- 作为构造函数执行
```
function Foo(name,age){
    //this={} //默认有这一行
    this.name=name
    this.age=age
    //return this //默认有这一行
}
var f=new Foo('zhangsan',15)
```
- 作为对象属性执行
```
var a={
    name:'A',
    fn:function(){
        console.log(this.name)
    }
}
a.fn()
```
- 作为普通函数执行
```
function fn1(){
    console.log(this)
}
fn1()//window
```
- call apply bind
```
function fn1(name,age){
    console.log(name)//zhangsan
    console.log(this)//{x:100}
}
fn1.call({x:100},'zhangsan',18)
fn1.apply({x:100},['zhangsan',18])
var fn2=function(name,age){
    console.log(name)//zhangsan
    console.log(this)//{y:200}
}.bind({y:200})
fn2('zhangsan',18)
```

## 作用域
没有块级作用域
```
if(true){
    var name='zhangsan'
}
console.log(name)
```
只有函数和全局作用域
```
var a=100
function fn(){
    var a=200
    console.log('fn',a)
}
console.log('global',a)
fn()
```
## 作用域链
```
var a=100
function fn(){
    var b=200
    //当前作用域没有定义的变量，即"自由变量"
    console.log(a)

    console.log(b)
}
fn()
```
**函数的父级作用域是函数定义时定义的，不是执行时**
```
var a=100
function f1(){
    var b=200
    function f2(){
        var c=300
        console.log(a)
        console.log(b)
        console.log(c)
    }
    f2()
}
f1()
```
作用域域链指自由变量一直向父级作用域查找形成链式结构
## 闭包
函数作为返回值
```
function f1(){
    var a=100

    return function(){
        console.log(a)//自由变量,从定义时的父级作用域中寻找
    }
}
var f=f1()
var a=200
f()
```
函数作为参数传递
```
function f1(){
    var a=100

    return function(){
        console.log(a)//自由变量,从定义时的父级作用域中寻找
    }
}
var f=f1()
function f2(fn){
    var a=200
    fn()
}
f2(f)
```

# 异步和单线程
## 题目
- 同步和异步的区别是什么?分别举一个同步和异步的例子
同步会阻塞代码的执行，异步不会
alert是同步,setTimeout是异步
- 一个关于setTimeout的笔试题
- 前端使用异步的场景有哪些

## 什么是异步
```
console.log('100');
setTimeout(function(){
    console.log('200');
},1000)
console.log('300');

```
对比同步
```
console.log('100');
alert('200')
console.log('300');

```

## 前端使用异步的场景
可能发生等待的情况
定时任务:setTimeout,setInterval
网络请求:ajax请求,动态<img>加载
```
console.log('start')
$.get('https://reqres.in/api/users',function(data){
    console.log(data)
})
console.log('end')
```
```
console.log('start')
var img=document.createElement('img')
img.onload=function(){
    console.log('loaded')
}
img.src='https://ss0.baidu.com/6ONWsjip0QIZ8tyhnq/it/u=892132191,2796909143&fm=179&app=42&f=JPEG?w=121&h=140'
console.log('end')
```
事件绑定

## 异步和单线程
```
console.log('100');
setTimeout(function(){
    console.log('200');
})
console.log('300');
```
执行第一行打印100
执行setTimeout后，传入的函数会暂存起来
执行最后一行打印300
所有程序执行完,处于空闲状态时,看有没有暂存起来的要执行
发现暂存起来的setTimeout中的函数无需等待时间，就立即执行(如果加入了时间，则过了这个时间才解封)

# 引用类型API
## 题目
- 获取2018-12-12格式的日期
```
function formatDate(dt){
    if(!dt){
        dt=new Date()
    }
    var year=dt.getFullYear()
    var month=dt.getMonth()+1
    var date=dt.getDate()
    if(month<10){
        month='0'+month
    }
    if(date<10){
        date='0'+date
    }
    return year+'-'+month+'-'+date
}
console.log(formatDate())
```

- 获取随机数，要求长度一致的字符串格式
```
var random=Math.random()
random=random+'0000000000'
random=random.slice(0,10)
console.log(random)
```
- 写一个能遍历对象和数组的通用forEach函数
```
function forEach(obj,fn){
    var key
    if(obj instanceof Array){
        obj.forEach(function(item,index){
            fn(index,item)
        })
    }else{
        for(key in obj){
            fn(key,obj[key])
        }
    }
}
var obj=[1,2,3]
//注意这里参数顺序换了,为了和对象的遍历格式保持一致
forEach(obj,function(index,item){
    console.log(index,item)
})
var obj2={x:1,y:2}
forEach(obj2,function(k,v){
    console.log(k,v)
})
```
## 日期
```
Date.now()
var dt=new Date()
dt.getTime()//毫秒
dt.getFullYear()//年
dt.getMonth()//月(0-11)
dt.getDate()//日(1-31)
dt.getHours()//小时(0-23)
dt.getMinutes()//分钟(0-59)
dt.getSeconds()//秒(0-59)
```
## Math
```
Math.random()//0到1间的小数(不含0.0和1.0)，可用于清除缓存
生成1-10间的整数:var num=Math.floor(Math.random()*10+1);
```
## 数组
forEach
```
var a=[1,2,3]
a.forEach(function(item,index){
    console.log(item,index);
})
```
every判断所有元素符合条件
```
var a=[1,2,3]
var result=a.every(function(item){
    if(item<4>){
        return true
    }
})
console.log(result)
```
some判断至少一个元素符合条件
sort
```
var a=[1,2,3]
var b=a.sort(function(a,b){
    //从小到大
    return a-b
})
console.log(b)
```
map对元素重新组装,生成新数组
```
var a=[1,2,3]
var b=a.map(function(item){
    return 2*item
})
console.log(b)
```
filter过滤符合条件的元素
## 对象
```
var a={
    x:1,y:2
}
var key
for(key in a){
    if(a.hasOwnProperty(key)){
        console.log(key,a[key])
    }
}
```

# JS Web API
常说的JS包含两部分:
1. JS基础知识:ECMA 262标准
变量类型
原型
作用域
异步

1. JS-Web-API:W3C标准
- DOM操作
- BOM操作
- 事件绑定
- ajax请求
- 存储

window.alert()浏览器需要做:
定义一个window全局变量，对象类型
给它定义一个alert属性，属性值是一个函数

document.getElementById()浏览器需要做类似

## DOM
### 题目
- DOM是哪种基本的数据结构
- DOM操作的常用API有哪些
- DOM节点的attribute和property有何区别
property是js对象属性,attribute是html标签属性

### DOM节点操作
document.getElementById()
document.getElementsByTagName()
document.getElementsByClassName()
```
var pList=document.querySelectorAll('p')
var p=pList[0]
console.log(p.style.width)
p.style.width='100px'
console.log(p.className)
p.className='aaa'
console.log(p.nodeName)
console.log(p.nodeType)
p.getAttribute('data-name')
```
### DOM结构操作
```
//创建
var p=document.createElement('p')
//移动
p.appendChild(document.getElementById('some-id'))
//父元素
p.parentElement
//子元素
var children=p.childNodes
p.removeChild(children[0])
```
## BOM
### 题目
- 如何检测浏览器类型
- 拆解url各部分

naviagtor
var ua=navigator.agent
var isChrome=ua.indexOf('Chrome')

screen
screen.width
screen.height

location
location.href
location.protocal//http:
location.host
location.pathname// /path1/path2
location.search//?id=1
location.hash//#mid=1

history
history.back()
history.forward()

## 事件
### 题目
编写一个通用的事件监听函数
描述事件冒泡流程
对于一个无限下拉加载图片的页面，如何给每个图片绑定事件

### 事件绑定
内联事件
```
<div onmouseover="this.style.color='red'" onmouseout="this.style.color='black'">hi</div>
```
简单事件函数
```
<div onmouseover="mouseover(this)" onmouseout="mouseout(this)">hi</div>
<script>
    function mouseover(elem){
        elem.style.color='red'
    }
    function mouseout(elem){
        elem.style.color='black'
    }
</script>
```
使用DOM和事件关联
```
<div id="div1">hi</div>
<script>
    function mouseover(e){
        e.target.style.color='red'
    }
    function mouseout(e){
        e.target.style.color='black'
    }
    var div1=document.getElementById('div1')
    div1.addEventListener('mouseover',mouseover)
    div1.addEventListener('mouseout',mouseout)
</script>
```
使用addEventListener可以访问某些高级事件特性,e是Event对象,包含属性
- target:事件指向的元素
- currentTarget:带有当前被触发事件监听器的元素
- stopPropagation():当元素的事件监听器被触发后终止事件在元素树中的流动
- preventDefault():防止浏览器执行与事件关联的默认操作


### 理解事件流
事件流分为三个阶段:
- 捕捉阶段
p1.addEventListener('mouseover',handleDescendantEvent,true)
在后代元素的目标阶段之前调用

- 目标阶段
addEventListener都会含该阶段

- 冒泡阶段
p1.addEventListener('mouseover',handleDescendantEvent)
在后代元素的目标阶段之后调用
![](/blog/assets/jsinterviews/event.png)

举例
```
<p id="p1">
    1111111111111111111111111111111111111
    1111111111111111111111111111111111111
    1111111111111111111111111111111111111
    <span id="s1">aaaaaaaaaaaaaaaaa</span>
    1111111111111111111111111111111111111
    1111111111111111111111111111111111111
    1111111111111111111111111111111111111
</p>
<script>
    
    var s1=document.getElementById('s1')
    s1.addEventListener('mouseover',handleMouseEvent)
    s1.addEventListener('mouseout',handleMouseEvent)
    function handleMouseEvent(e){
        if(e.type==='mouseover'){
            console.log('s1')
            e.target.style.background='red'
        }else{
            e.target.style.removeProperty('color')
            e.target.style.removeProperty('background')
        }
    }
    var p1=document.getElementById('p1')
    p1.addEventListener('mouseover',handleDescendantEvent,true)
    p1.addEventListener('mouseout',handleDescendantEvent,true)
    p1.addEventListener('mouseover',handleBubbleEvent)
    p1.addEventListener('mouseout',handleBubbleEvent)
    function handleDescendantEvent(e){
        if(e.type=='mouseover'&&e.eventPhase==Event.CAPTURING_PHASE){
            console.log('capturing')
            e.target.style.border="thick solid green"
            e.currentTarget.style.border="thick double yellow"
        }else if(e.type=='mouseout'&&e.eventPhase==Event.CAPTURING_PHASE){
            e.target.style.removeProperty('border')
            e.currentTarget.style.removeProperty('border')
        }

    }
    function handleBubbleEvent(e){
        if(e.type=='mouseover'&&e.eventPhase==Event.BUBBLING_PHASE){
            console.log('bubbling')
            e.target.style.textTransform="uppercase"
        }else if(e.type=='mouseout'&&e.eventPhase==Event.BUBBLING_PHASE){
            e.target.style.textTransform="none"
        }

    }
</script>
```
### 代理(事件冒泡的一个具体应用)
```
<div id="div1">
    <a href="#">a1</a>
    <a href="#">a2</a>
    <a href="#">a3</a>
    <a href="#">a4</a>
    <!-- 随时会有更多a标签 -->
</div>
```
```
<script>
    var p1=document.getElementById('p1')
    var body=document.body
    p1.addEventListener('click',function(e){
        e.stopPropagation()
        console.log('激活')
    })
    body.addEventListener('click',function(e){
        console.log('取消')
    })
</script>
```


完善通用绑定事件函数
```
function bindEvent(elem,type,selector,fn){
    if(fn==null){
        fn=selector
        selector=null
    }
    elem.addEventListener(type,function(e){
        var target
        if(selector){
            target=e.target
            if(target.matches(selector)){
                fn.call(target,e)
            }
        }else{
            fn(e)
        }
    })
}
//使用代理
var div1=document.getElementById('div1')
bindEvent(div1,'click','a',function(e){
    console.log(this.innerHTML)
})
//不使用代理
var aa=document.getElementById('aa')
bindEvent(div1,'click',function(e){
    console.log(aa.innerHTML)
})
```

代理好处
- 代码简介
- 减少浏览器内存占用

## ajax
### 题目
手动编写一个ajax，不依赖第三方库
```
var xhr=new XMLHttpRequest()
xhr.open('GET','https://reqres.in/api/users',false)
xhr.onreadystatechange=function(){
    if(xhr.readyState==4){
        if(xhr.status==200){
            console.log(xhr.responseText)
        }
    }
}
xhr.send(null)
```
跨域的几种不同实现方式

三个标签的场景
img用于打点统计，统计网站可能是其他域
link,script可以使用cdn,cdn的也是其他域
script可以用于jsonp

## 存储
### 题目
- cookie,sessionStorage,localStorage的区别
cookie 4k,会携带到ajax中
sessionStorage,localStorage 5M,API易用

### cookie
本身用于客户端和服务端通信
但它有本地存储的功能，于是就被"借用"
使用document.cookie=...获得和修改即可

缺点:
存储量太小,只有4kb
所有的http请求都带着,影响获取资源的效率
API简单，需要封装才能用

### localStorage和sessionStorage
最大容量5M
API简单易用
localStorage.setItem(k,v)
localStorage.getItem(k)

sessionStorage关闭浏览器后会清理

# 开发环境
## 模块化
不使用模块化
```
//util.js
function a(){
    //do sth.
}
//a-util.js
function b(){
    a();
    //do sth.
}
//a.js
function c(){
    c()
    //do sth.
}

//main html
<script src="util.js"></script>
<script src="a-util.js"></script>
<script src="a.js"></script>
<script>
c()
</script>
```


使用模块化可以：
避免全局变量污染
不用在意依赖和导入顺序

### AMD(异步模块定义)规范
比较好的一个实现-require.js
全局define函数
全局require函数
依赖JS会自动加载、异步加载

代码位置
js-question/amd
### CommonJS规范
nodejs模块化规范
前端开发依赖的插件和库,都可以从npm中获取
CommonJS本身不会异步加载JS,而是同步一次性加载出来(通过工具可以异步加载)

需要构建工具支持
一般和npm一起使用

代码位置
js-question/webpack

### AMD和CommonJS的使用场景
需要异步加载JS,使用AMD
使用了npm之后建议使用CommonJS

# 运行环境
## 问题
- window.onload和DOMContentLoaded区别
window.addEventListener('load',function(){
    //页面的全部资源加载完执行,包括图片、视频等
})
document.addEventListener('DOMContentLoaded',function(){
    //DOM渲染完即可,此时图片、视频可能还没加载完，jquery/zepto用这种方式判断页面加载完成
})
- 从输入url到得到html的详细过程
根据DNS服务器获取得到域名的IP
向IP所在服务器发送http请求
服务器处理并返回请求
浏览器得到返回内容

## 渲染过程
HTML结构生成DOM Tree
CSS生成CSSOM
DOM和CSSOM整合形成RenderTree
根据RenderTree渲染
遇到script标签时会执行并阻塞渲染

为何将CSS放在head中
可以在渲染body中内容时,根据RenderTree一次渲染成功，避免重新渲染

为何将scritp放在body最后
不会阻塞html渲染
可以获取所有body中的元素

## 性能优化
### 加载页面和静态资源
静态资源合并
静态资源缓存
使用CDN让资源加载更快
SSR后端渲染,数据直接输出到HTML(jsp,php,asp就是后端渲染)
### 页面渲染
css放前面,js放后面

懒加载(图片下拉逐渐加载)
```
<img id="img1" src="small_preview.png" data-realsrc="abc.png">
<script>
var img=document.getElementById('img1')
img.src=img.getAttribute('data-realsrc')
<script>
```

减少DOM查询,对DOM查询缓存
```
//未使用缓存
var i
for(i=0;i<document.getElementsByTagName('p');i++){
    //do sth.
}
//使用缓存
var i
var plist=document.getElementsByTagName('p')
for(i=0;i<plist.length;i++){
    //do sth.
}
```
减少DOM操作,多个操作尽量合并在一起执行
比如将10次append操作并为一次
```
var listNode=document.getElementById('list')
var frag=document.createDocumentFragment()
var i,li
for(i=0;i<10;i++){
    li=document.createElement('li')
    li.innerHTML="list item:"+i
    frag.appendChild(li)
}
listNode.appendChild(frag)
```

事件节流
比如连续输入时不做事件处理,只有停顿超过100毫秒时才处理:
```
var t=document.getElementById('text')
var timeoutId
t.addEventListener('keyup',function(){
    if(timeoutId){
        clearTimeout(timeoutId)
    }
    timeoutId=setTimeout(function(){
        //触发change事件
    },100)
})
```
尽早执行操作
比如DOMContentLoaded作为页面加载完成事件

## 安全
### XXS跨站请求攻击
在写文章时插入一段script，一旦别人看这篇文章就获取cookie，发送到自己服务器

防御方法:
前端替换关键字,例如替换<为&lt
后端替换
### XSRF跨站请求伪造
你登录了一个购物网站，正在浏览商品
网站付费接口为xx.com/pay?id=100，但没有任何验证
然后你收到一封邮件，隐藏着<img src="xx.com/pay?id=100">
你查看邮件的时候，就悄悄付费购买了

防御方法:
增加验证流程，如输入指纹、密码、短信验证码












