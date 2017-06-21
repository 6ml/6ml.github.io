---
layout: post
title: "JS 类型转换"
date: 2016-11-26
description: "js 在一定情况下发生的转换，如 == 或 + 或 ++ 等"
tag: JavaScript
---

### 包容的 JavaScript

JavaScript 是一门弱类型的语言，对于不同变量类型之间的操作，JS 并没有特别严格的要求。比如，我们可以将一个数字和一个字符串相加，或者将一个数字与一个布尔值相乘等等，JS 不会抛出错误，JS 表示：“当然是选择原谅它啦”！

其实，遇到上述情况，JS 会悄悄地将它们转化为同一类型的值，然后比较或者进行其他操作。这也就是我们所说的隐式类型转换。

那么，有哪些操作会引起 JS 隐式类型转换嘞？

### 隐式转换

在我们使用一些特定的操作符并且操作符两边变量类型不同时，会发生隐式转换，还可能在一些特定情况下也会发生隐式转换。

#### == 

在判断变量值是否相等时，会有很多的隐式转换，它们遵循以下顺序：

1.	首先看双等号两边有无 **NaN** ，若有，一律 false
2.	看双等号两边有无**布尔值**，如有，将布尔值转化为数字
3.	看双等号两边有无字符串，如有：
	-	对方是对象，先调用`valueOf`方法，判断返回的值是否相等，相等返回 true ；不相等，再调用`toString`方法继续判断
	-	对方是数字，将字符串转化为数字然后进行比较
	-	对方是字符串，直接比较
	-	对方是其他类型的值，直接返回 false
4.	数字和对象比较，同字符串与对象比较相同，先取`valueOf`返回值比较，然后取`toString`返回值进行比较

**注意**：
-	`null`和`undefined`不会发生类型转换，但是它们相等
-	NaN 不与任何变量相等，包括其本身
-	对象和其他类型变量比较的时候都是先调用其`valueOf`方法，取返回值与其他变量比较，然后再取`toString`方法返回值进行比较。

#### - / * %

在我们用这几个操作符的时候，如果操作符两边有不是数值类型的变量，JS 会将其转化成数值类型，然后进行相应操作。

```JavaScript
'123' - 10;		// 113

true * [3];		// 3	

{valueOf : function(){return 120;}} / {toString: function(){return 20;}};	// 6
```

#### +

对于" + "操作，要稍微复杂一点点。我们首先要判断操作符两边是否有字符串，如果有，则将另一个变量转化为字符串然后拼接起来；如果没有字符串而有数值类型的变量，则像上面的几个操作符一样，将另一个变量转化为数值，然后进行加运算。

```JavaScript
//有字符串的情况：

'str' + 123;		// str123

'str' + false;		// strfalse

'str' + [1, 2];		// str1,2

'str' + {valueOf : function(){return 234;}, toString : function(){return '345';}};	// str234

//没有字符串而有数字的情况：

123 + true + [10];	// 134
```

#### 判断语句

判断语句中，最终结果一律转化成布尔值。

我们必须知道，在 JavaScript 中，**只有以下6个值为假，其余情况全部为真**。

-	空字符串
-	数字 0
-	false
-	undefined
-	null
-	NaN

### 显式转换

看了隐式转换，我们再来看看各个类型变量的显式转换。

#### 其他类型转数值类型

我们用`Number`方法可以将其他类型的变量直接转化成数值类型。

```JavaScript
Number('');		// 0

Number('123');		// 123

Number('str');		// NaN

Number(false);		// 0

Number(true);		// 1

Number([]);		// 0

Number([123]);		// 123

Number([1, 2]);		// NaN

Number(null);		// 0

Number(undefined);	// NaN

Number({});		// NaN

Number({valueOf: function(){return 123;}}); 	// 123

Number({toString: function(){return '1';}}); 	// 1
```

#### 其他类型转字符串

我们用`String`方法可以将其他类型的变量直接转化成数值类型。

大多数类型都是相当于直接把自己“套进”一个双引号中，包括函数，但对象和数组稍微有点不同。

```JavaScript
String(123);			// "123"

String(false);			// "false"

String(null);			// "null"

String(undefined);		// "undefined"

String(NaN);			// "NaN"

String([]);			// ""

String([1, '23', false]);	// "1,23,false"

String({name: zhangsan});	// "[object Object]"
```