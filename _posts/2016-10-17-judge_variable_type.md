---
layout: post
title: "js 数据类型及判断方法"
date: 2016-10-17
description: "typeof instanceof Object.prototype.toString.call() 三种方法判断变量类型"
tag: JavaScript
---

### 数据类型
JavaScript 原始数据类型有七种：

1.	number
2.	string
3.	boolean
4.	null
5.	undefined
6.	symbol
7.	object

`symbol`类型是 ES6 引入的新的原始数据类型，表示一个**独一无二**的值。

前六种都是简单基本数据类型，最后一种`object`(对象)是复杂数据类型。

对象子类型(内置对象)：

-	String
-	Number
-	Boolean
-	Object
-	Function
-	Array
-	Date
-	RegExp
-	Error

### 判断数据类型方法

一般来说，我们判断数据类型的方法有三种：

-	typeof
-	instanceof
-	Object.prototype.toString.call()

#### typeof

`typeof`运算符把类型信息以字符串形式返回。

`typeof`返回值有以下七种：

-	"number"
-	"string"
-	"boolean"
-	"undefined"
-	"object"
-	"function"
-	"symbol"

注意：**对于**`null`**和其他对象子类型**，`typeof`**都返回"object"**

#### instanceof

`instanceof`返回一个布尔值，该值指示一个对象是否为特定类的一个实例。

`instanceof`可用来判断对象具体的类型。其返回值如下：

```JavaScript
var arr = [],
	obj = {},
	reg = /^\w+$/,
	date = new Date(),
	error = new Error();

function fn(){};

console.log(arr instanceof Array);	// true

console.log(obj instanceof Object);	// true

console.log(reg instanceof RegExp);	// true

console.log(date instanceof Date);	// true

console.log(error instanceof Error);	// true

console.log(fn instanceof Function);	// true

```

#### Object.prototype.toString.call()

这个方法可以精确地判断元素类型，它可以判断数值、字符串、布尔值、`undefined`、`null`、`symbol`这六种简单基本数据类型，还能判断出对象的子类型。其返回值如下：

```JavaScript
Object.prototype.toString.call(123);		// "[object Number]"

Object.prototype.toString.call("str");		// "[object String]"

Object.prototype.toString.call(false);		// "[object Boolean]"

Object.prototype.toString.call([]);		// "[object Array]"

Object.prototype.toString.call({});		// "[object Object]"

Object.prototype.toString.call(function(){});	// "[object Function]"

Object.prototype.toString.call(undefined);	// "[object Undefined]"

Object.prototype.toString.call(null);		// "[object Null]"

Object.prototype.toString.call(new Date());	// "[object Date]"

Object.prototype.toString.call(/^\w+$/);	// "[object RegExp]"

Object.prototype.toString.call(new Error());	// "[object Error]"
```