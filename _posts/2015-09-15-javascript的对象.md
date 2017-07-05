

##对象创建的三种方式：

- 对象字面量

```
var hero1={
      name:'jacky',
      say:function(){
              return this.name;
      }
}
//hero1.say()输出:jacky
```
- 构造函数

```
function Hero(name){
	this.name=name;
	this.say=function(){
		return this.name;
	}
}
var hero2=new Hero('jacky');
//hero2.say()输出jacky
```

使用构造函数的优点是可以传入参数，构造函数中的this代表当前对象，使用var hero2=`new Hero()` 的方式。如果单纯调用函数`var hero3=Hero()` 返回的是undefined而不是对象，而且this代表的当前对象是window。

使用`var hero2=new Hero()` 时，hero2对象会增加一个属性constructor,代表构造函数，可以使用`var hero4=new hero2.constructor('zhangsan')` 创建一个新对象。

`hero2 instanceof Hero ` 会输出`true`,`instanceof`用来判断一个对象是否由某个构造函数生成。

- 使用返回值为对象的函数

```
function getHero(name){
	return {
		name:name,
		say:function(){
			return this.name;
		}
	}
}
var hero6=getHero('jacky');
//hero6.say()输出jacky
```


----------
##javascript内置对象
###Array
创建方式：

```
var arr1=[4,2,3];//数组字面量
var arr2=new Array(4,2,3);//构造函数
```
属性和方法：

```
console.log(arr2.length);//4
console.log(arr2.constructor);//Array()

arr2.push(1);//[4,2,3,1]

arr2.pop();//[4,2,3]

arr2.sort();//[2,3,4]

var joinedStr=arr2.join("@");//返回拼接后字符串"2@3@4"

var slicedArr=arr2.slice(1,2);//返回截取出来的数组片段[3] ，参数1表示开始位置(包含),参数2表示结束位置(不包含)

console.log(arr2);//[2,3,4] 截取后源数组不变

var splicedArr=arr2.splice(1,2,5);//返回截取出来的数组片段[3,4] ，参数1表示开始位置(包含),参数2表示截取的长度(包含,注意与slice不一样)，从参数3开始可以定义任意个，用来替换被截取位置

console.log(arr2);//[2,5] 截取后源数组改变
```

###Function
属性：

```
var F=function(){};
console.log(F.constructor);//Function()
console.log(F.prototype);//F {}
```
prototype是函数最重要的属性，会在以后详述，简单的了解下：

- prototype包含一个对象
- 只有函数被用作构造函数时才有用
- 由该构造函数构造的对象拥有这个prototype引用

方法：
有两个有用的方法call()和apply()，让我们看看干啥用的。

```
    var tf={
		name:'屠夫',
		say:function(word){
			return word+" 我是"+this.name;
		}
	}
	console.log(tf.say("fresh meat"));//fresh meat 我是屠夫
	var hnv={
		name:'火女'
	}
	console.log(tf.say.call(hnv,"help me help you"));//help me help you 我是火女
	console.log(tf.say.apply(hnv,["help me help you"]));//help me help you 我是火女
```
call()的作用是改变函数中this的指向,apply()与call()的细微差别是在给函数传参时apply用数组方式。 

###String
常用方法：

```
	var str1="Show Me What u got";
	console.log(str1.toUpperCase());//SHOW ME WHAT U GOT
	console.log(str1.toLowerCase());//show me what u got
	console.log(str1.indexOf('Me'));//5
	console.log(str1.indexOf('hehe'));//-1，如果没有找到返回-1
	console.log(str1.lastIndexOf('o'));//16
	console.log(str1.substring(1,3));//ho
	console.log(str1.split(' '));//["Show", "Me", "What", "u", "got"]
```

###Date
- Date对象的创建：

```
	var date1=new Date(2015,07,26,09,48,01);
	console.log(date1);//Wed Aug 26 2015 09:48:01 GMT+0800 (中国标准时间)
	var date2=Date();
	console.log(date2);//Wed Aug 26 2015 09:57:46 GMT+0800 (中国标准时间)
```
new Date()参数的意义按照顺序如下：
 1. 年
 2. 月（0表示1月，11表示12月）
 3. 日
 4. 时
 5. 分
 6. 秒
 7. 毫秒
 调用getMonth(),setHours()等方法来读取和修改单个参数。
直接调用Date()则返回当前时间。

###JSON

- 序列化

```
	var xiaoming={name:'小明',age:18,skills:['javascript','java']};
	var xiaoming_j=JSON.stringify(xiaoming);//stringify将javascript对象转换为json
	console.log(xiaoming_j);//{"name":"小明","age":18,"skills":["javascript","java"]}
	xiaoming_j=JSON.stringify(xiaoming,['name','skills']);//stringify第二个参数用于过滤哪些进行转换
	console.log(xiaoming_j);//{"name":"小明","skills":["javascript","java"]}
	xiaoming_j=JSON.stringify(xiaoming,convert);//stringify第二个参数也可以穿函数
	console.log(xiaoming_j);//{"name":"小明","age":18,"skills":["JAVASCRIPT","JAVA"]}
	
	//也可以给对象增加如下方法
	xiaoming.toJSON=function(){
		return {'Name':this.name,'Age':this.age};
	}
	xiaoming_j=JSON.stringify(xiaoming);
	console.log(xiaoming_j);//{"Name":"小明","Age":18}
```


- 反序列化

```
	xiaoming=JSON.parse(xiaoming_j);//将json转换为javascript对象
	console.log(xiaoming);//Object {Name: "小明", Age: 18}
```