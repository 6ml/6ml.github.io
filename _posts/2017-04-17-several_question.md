---
layout: post
title: "几道有意思的 JS 题"
date: 2017-04-17
description: "笔试题"
tag: JavaScript
---

### 第一题
```JavaScript
var a = {n : 1};
var b = a;
a.x = a = {n : 2};

console.log(b.x); //{n : 2}
console.log(a.x); //undefined
```
考点：引用类型、连续赋值语句

个人理解：
1.	首先，`a`、`b`同时指向一个对象`{n : 1}`
2.	赋值运算是从右至左(结合)，连续赋值语句可以理解为`a.x = (a = {n : 2})`，即将一个表达式`a = {n : 2}`赋值给`a.x`
3.	取到`a.x`的引用，即`{n : 1}.x`
4.	计算表达式`a = {n : 2}`的值，计算值的过程中将`{n : 2}`赋值给了`a`，得到表达式的结果为`{n : 2}`
5.	将表达式结果赋值给`a.x`，即将`{n : 2}`赋值给`{n : 1}.x`

下图可以证明赋值语句是从右向左的
![连续赋值语句从右向左](/images/posts/question/question_0.png)
`a.x = a = {n : 1}`这句话报错：无法为`undefined`设置属性`x`，但后面打印`a`的时候发现`a`已经成功赋值,说明将`{n : 1}`赋值给`a`先于`a.x = a`执行
> 如果对这个题感兴趣可以参考帖子[写了10年Javascript未必全了解的连续赋值运算](http://www.iteye.com/topic/785445)的讨论

### 第二题
```JavaScript
Object.prototype.a = 1;
Function.prototype.b = 2;
function Foo(){};
var foo = new Foo();

console.log(Foo.a); //1
console.log(Foo.b); //2
console.log(foo.a); //1
console.log(foo.b); //undefined
console.log(Object.b) //2
```
考点：原型链

-	`Foo.__proto__ === Function.prototype`
-	`Foo.prototype.__proto__ === Object.prototype`
-	`foo.__proto__ === Foo.prototype`
-	`Object.__prototype === Function.prototype`
-	`Function.prototype.__proto__ === Object.prototype`

可见，`foo`的原型链是`foo -> Foo.prototype -> Object.prototype`；而`Foo`的原型链是`Foo -> Function.prototype -> Object.prototype`。

因此，`Foo`能够通过原型链查找到`Function.prototyoe`和`Object.prototype`上的属性；而`foo`只能查找到`Foo.prototype`和`Object.prototype`上的属性，无法查找到`Function.prototype`上的属性。

### 第三题
```JavaScript
var a = 1;
a.b = 21;

console.log(a.b);
```
考点：基本类型、包装对象

上述代码运行的结果如下图
![包装对象题](/images/posts/question/question_1.png)
结果显示`a.b`未定义，但我们明明在第二行为`a`定义了属性`b`，并且将 21 赋值给它了，而且奇怪的是我们为一个基本类型增加一个属性居然没有报错。

原因是`a`是一个数字，是一个基本类型，我们无法为一个基本类型增加属性，当我们尝试为一个基本类型增加属性时，后台会创建一个对应的临时包装类型，这里是`Number`，然后将属性`b`增加到这个包装类型上，这条语句执行完后立即删除掉这个临时包装类型，所以在`a`上找不到属性`b`。

### 第四题
```JavaScript
function Foo() {

    getName = function () { console.log(1); };
    return this;
}

Foo.getName = function () { console.log(2);};

Foo.prototype.getName = function () { console.log(3);};

var getName = function () { console.log(4);};

function getName() { console.log(5);}

//请写出以下输出结果：
Foo.getName();  			//2
getName();				//4
Foo().getName();			//1 
getName();				//1
new Foo.getName();			//2
new Foo().getName();			//3
new new Foo().getName();		//3
```
考点：声明提升，new 操作符

上面的代码在声明提升后会变成下面的样子
```JavaScript
function Foo() {

    getName = function () { console.log(1); };
    return this;
}

function getName() { console.log(5);}
var getName;//重复声明，忽略掉
Foo.getName = function () { console.log(2);};
Foo.prototype.getName = function () { console.log(3);};
getName = function () { console.log(4);};
```
现在我们来看结果：
-	第一行：调用`Foo.getName()`，打印 2 
-	第二行：现在全局变量中的函数声明方式创建的`getName`被函数表达式创建的`getName`函数覆盖，所以打印 4 
-	第三行：调用`Foo().getName()`，首先调用`Foo`函数，调用的过程中内部的`getName`函数覆盖了全局变量中的`getName`函数，所以现在全局变量中的`getName`函数打印 1 ;`Foo`调用完毕，返回`this`，即全局变量，再调用全局变量下的`getName`，打印 1
-	第四行：在全局下调用`getName`，打印 1
-	第五行：`new Foo.getName`，`new`操作符会以构造函数调用的方式调用函数：将 this 绑定给一个对象，最后返回这个对象。这里调用`Foo.getName`时打印 2
-	第六行：`new Foo().getName()`，首先调用`new Foo()`，返回一个新对象，这个对象是`Foo`的实例，所以可以通过原型链访问到`Foo.prototype`上的属性和方法，找到`Foo.prototype.getName`，调用它，打印出 3
-	第七行：这一行在第六行的基础上又增加了一个`new`，前面和第六行一样，先找到`Foo.prototype.getName`，区别是这一次调用方式变成了构造函数调用，调用这个函数，打印 3 ，并且返回一个对象。	

#### 声明提升
在 JavaScript 中，存在声明提升，声明提升分为函数声明提升和变量声明提升。

#### 函数声明提升
只有函数声明会被提升，并且将整个声明提升至作用域顶部。

**函数表达式不会被提升。**

#### 变量声明提升
只提升变量声明，不提升变量赋值。

**函数声明提升会在变量声明提升之前**