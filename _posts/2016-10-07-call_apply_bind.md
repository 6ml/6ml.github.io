---
layout: post
title: "call apply bind"
date: 2016-10-07
description: "显式绑定 this 对象的方法"
tag: JavaScript
---

### 上下文
JavaScript 的一大特点是，函数存在 [定义时上下文] 和 [运行时上下文] 以及 [上下文是可以改变的] 这样的概念。

`call`和`apply`都是为了改变某个函数运行时的上下文 (context) 而存在的，换句话说就是改变函数内部`this`的指向。

### apply 和 call 的区别
对于`apply`和`call`二者而言，作用完全是一样的，只是接受参数的方式不一样。`call`需要把参数**按顺序**传递进去，而`apply`是把参数**放在数组里**。在非严格模式下，第一个参数传入`null`或`undifined`时，`Global`对象会被作为`this`。

一般来说，当你的参数是明确知道数量的时候用`call`；不确定时用`apply`，然后把参数`push`进数组然后传递进去。当参数不确实时，函数内部**也可以通过**`arguments`**这个类数组对象来遍历所有的参数**。比如：
```
function log(msg){

    console.log(msg);

}

log(1); //1

log(1,2); //1
```


```
function log(){

    console.log.apply(console, arguments);

}

log(1); //1

log(1,2); //1 2
```
接下来我们看看怎么给每个 log 消息都添加一个 “(app)” 前缀：
```
log("hello world"); // (app)hello world

function log() {

    var args = Array.prototype.slice.call(arguments);
    
    args.unshift('(app)');
    
    console.log.apply(console,args);

}
```
### 类数组对象
前面我们提到了类数组对象，类数组对象是什么嘞？我们可以通过看看它具有什么以及不具有什么与数组对象进行对比。
-	具有：指向对象元素的**数字索引下标**以及`length`属性告诉我们对象的元素个数
-	不具有：诸如`push`、`forEach`以及`indexOf`等数组对象具有的方法。

类数组对象典型例子：`getElementsByTagName`，`document.childNodes`等 **DOM** 方法的返回结果，他们返回`NodeList`对象都属于类数组对象；还有就是 **arguments** 。

我们可以调用一些方法将类数组对象转化为数组对象：
```
Array.prototype.slice.call( 类数组对象 )
```
其返回值就是一个数组对象了!
### 常用用法：
-	数组之间追加
-	获取数组中的最大值最小值
-	验证是否为数组
-	类(伪)数组对象使用数组方法

### bind
`bind()`方法与`apply`、`call`很类似，也是可以改变函数体内`this`的指向。

MDN的解释：`bind()`方法会创建一个新的函数，称之为绑定函数，当调用这个绑定函数的时候，绑定函数会以创建它时传入`bind()`方法的**第一个参数作为**`this`，传入`bind()`方法的**第二个以及以后的参数加上绑定函数运行时本身的参数*按照顺序 *作为原函数的参数来*调用原函数* **。

再常见的单体模式中，通常我们会使用 _this , that , self 等保存`this`，这样我们可以在改变上下文之后继续引用到它。比如：
```
var foo = {

    bar ： 1，
    
    eventBind: function(){
    
    	var self = **this**;
        
        $('.someClass').on('click',function(event){
        
        	console.log(self.bar); // 1
        
        })
    
    }

}
```
用于 JavaScript 特有的机制，上下文环境在`eventBind`函数过渡到`onclick`事件函数时发生了改变，我们用上述方法使用变量保存`this`值是有用的。当然使用`bind()`方法可以更加优雅地解决这个问题：
```
var foo = {

    bar : 1,
    
    eventBind : function(){
    
    	$('.someClass').on('click',function(event){
        
        	console.log(this.bar); // 1
        
        }.bind(this));
    
    }

}
```
在上述代码中`bind()`创建了一个函数，当 click 事件绑定在被调用的时候，它的`this`关键词会设置成传入的值（这里指调用`bind()`时传入的参数）。

有个有趣的问题，如果连续`bind()`两次，或者是三次那么输出的值会是什么呢？
```
var bar = function(){

    console.log(this.x);

}

bar(); // undefined

var fst = { x : 3 }

var sed = { x : 4 }

var thd = { x : 5 }

var func = bar.bind(fst)(); // 3

var func = bar.bind(fst).bind(sed)(); // 3

var func = bar.bind(fst).bind(sed).bind(thd)(); // 3
```
三次的结果都是 3 ,而不是 4 或者 5 。原因是，在 JavaScript中，**多次**`bind()`**是无效的**。更深层次的原始，`bind()`的实现，相当于使用函数在内部包了一个 `call`/`apply`，第二次`bind()`相当于再包住第一次`bind()`，所以第二次及以后的`bind`是无法生效的。
### apply call bind 比较
`bind()`方法改变上下文环境之后并非立即执行，而是回调执行的，而`apply`/`call`则会立即执行函数。

最后再总结一下：
-	`apply`、`call`、`bind`三者都是用来改变函数的`this`对象的指向的；
-	`apply`、`call`、`bind`三者第一个参数都是`this`要指向的对象，也就是想指定的上下文；
-	`apply`、`call`、`bind`三者都可以利用后续参数传参；
-	`bind`返回对应函数，便于稍后使用；`apply`、`call`则是立即调用。

> 参考文档：
-	[【优雅代码】深入浅出 妙用Javascript中apply、call、bind](http://web.jobbole.com/83642/) 作者：ChokCoco
-	[JavaScript 的怪癖 8：“类数组对象”](https://github.com/justjavac/12-javascript-quirks/blob/master/cn/8-array-like-objects.md) 作者：justjavac
