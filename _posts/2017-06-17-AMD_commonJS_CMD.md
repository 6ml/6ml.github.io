---
layout: post
title: "AMD CommonJS CMD"
date: 2017-06-17
description: "AMD CommonJS CMD"
tag: Others
---

### JS 模块规范
有了"JS 模块化"这个概念后，我们编写程序变得更加方便、高效，想要什么功能，就加载对应的模块。但是使用模块化编程就必须遵循同样的规范。

在 JS 中，模块规范包括 CommonJS 、AMD 和 CMD。

### CommonJS
2009年，美国程序员 Ryan Dahl 创造了 node.js 项目，将 javascript 语言用于服务器端编程。这标志"Javascript 模块化编程"正式诞生。

NodeJS 是 CommonJS 规范的实现，webpack 也是以 CommonJS 的形式来书写的。

#### 使用
在 CommonJS 中，有一个全局性方法`require()`，用于加载模块。如果我们需要加载一个 math.js ，我们应该像这样写：
```JavaScript
var math = require('math');
//加载模块后，我们就可以调用模块提供的方法
math.add(2, 3); //5
```

**CommonJS 定义的模块分为：{模块引用(require)} {模块定义(exports)} {模块标识(module)}**

`require()`用来引入外部模块，`exports`对象用于导出当前模块的变量和方法，这是**唯一的导出口**；`module`对象就是模块本身。

在 NodeJS 中，为了方便，Node 为每一个模块的提供一个 `exports`变量，指向`module.exports`，这等同于在每个模块头部输入如下代码：
```JavaScript
var exports = module.exports;
```
这样的话，我们在对外输出模块接口时，可以直接向 exports 对象添加方法。
```JavaScript
exports.area = function (r) {
	return Math.PI * r * r;
};
```
注意：**不能直接将 exports 对象指向一个值，这样等于切断了 exports 和 module.exports 的联系**。

### AMD
CommonJS 是基于服务器端产生的，但是我们也想在客户端使用这种模块化的编程方法，但是我们不能将 CommonJS 直接用在浏览器端。

**因为 CommonJS 是同步加载模块的**，在服务器端，我们的模块都存放在本地硬盘，即使我们同步加载等待时间也就是硬盘读取时间，这是非常快的。但是在浏览器中同步加载就会存在一个问题，因为模块都在服务器端，等待时间取决于网速，可能要等很长时间，这对于用于体验是非常不好的。

由此，AMD 产生了，它就主要为前端 JS 的表现指定规范。

AMD(Asynchronous Module Definition)，异步模块定义。它采用**异步方式加载模块**，模块的加载不影响后面语句的运行。所以依赖这个模块的语句，都定义在一个**回调函数**中，等到加载完成，这个回调函数才会执行。

AMD 也采用`require()`语句加载模块，但是不同于 CommoonJS ，它要求两个参数：
```JavaScript
/* Paramater
// [module] ，这是一个数组，里面的成员就是要加载的模块
// callback ，加载成功后的回调函数
*/
require([module], callback);
```
上面 CommonJS 中的例子用 AMD 的形式写就是下面的样子：
```JavaScript
require(['math'], function (math) {
	math.add(2, 3);
})
```
math.add() 与 math 模块不是同步的，浏览器不会发生家私。所以，AMD 比较适合浏览器环境。目前，主要由两个 JavaScript 库实现了 AMD 规范：[require.js](http://requirejs.org/) 和 [curl.js](https://github.com/cujojs/curl)
#### require.js
require.js 诞生是为了解决这两个问题
-	实现 js 文件的异步加载，避免页面失去响应
-	管理模块之间的依赖性，便于代码的编写和维护

要用 require.js ，首先得引入它，去官网下载最新版本，然后在页面中引入
```HTML
<script src="js/require.js" data-main="js/main"></script>
//data-main 是指定网页程序的主模块。这个文件会第一个被 require.js 加载。
```

##### 模块的加载

如果我们要依赖于其他模块，这时就要使用 AMD 规定定义的`require()`函数。
```JavaScript
require(['moduleA', 'moduleB', 'moduleC'], function(moduleA, moduleB, moduleC) {
	//do something when all modules are loaded successfully
})
```
回调函数会在所有模块成功加载后执行。在回调函数中，加载的模块会议参数的形式传入该函数，从而在回调函数中就可以使用这些模块。

##### 模块的写法

require.js 加载的模块，采用 AMD 规范。也就是说，模块必须按照 AMD 的规范来写。

模块必须采用特定的`define()`函数来定义。如果一个模块不依赖其他模块，那么可以直接定义在`define()`函数中。

比如有一个 math.js 文件，定义了一个 math 模块，那么 math.js 就要这样写
```JavaScript
define(function() {
	var add = function (x, y) {
		return x + y;
	};
	return {
		add : add
	};
});
```
如果这个模块还依赖其他模块，那么`define()`函数的第一个参数，必须是一个数组，指明该模块的依赖性。
```JavaScript
define(['myLib'], function(myLib){
	function foo(){
		myLib.doSomething();
	}
	return {
		foo : foo
	};
});
```

### CMD
CMD 是 SeaJs 在推广过程中对模块定义的规范化产出。CMD 和 AMD 相近，不过更加方便。

```JavaScript
define(function(require, exports, module){...});
```

CMD 和 Require.js 差别：
-	定位有差异
-	遵循的规范不同
-	推广理念有差异
-	对开发调试的支持有差异
-	插件机制不同

### ES6 的模块机制
讲到模块化，我们就不得不提一下 ES6 的模块机制。

ES6 模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。

ES6 模块不是对象，而是通过`export`命令显式指定输出的代码，再通过`import`命令输入。

#### 输出
`export`可以输出变量、函数、类、对象。
需要注意：`export`**规定的是对外的接口，必须与模块内部的变量建立一一对应的关系**。

比如我们要输出一个变量`m`，可以通过以下三种方式：

```JavaScript
export var m = 1;

var m = 1;
export {m};

var n = 1;
export {n as m};
```

另外，`export`语句输出的接口，与其值的值是动态绑定换洗，即通过该接口，可以去到模块内部实时IDE值。

```JavaScript
export var foo = 'bar';
setTimeout(() => foo = 'baz', 500);
//上述代码输出变量 foo ，值为 bar，500ms 后变成 baz
```
这一点与 CommonJS 规范完全不同。CommonJS 模块输出的是值的缓存，不存在动态更新。

`export default`，这条命令可以让我们在引入模块时可以不用知道输出模块的名字是什么，可以自己随意指定一个名字：

```JavaScript
export default function add(x,y) => x + y;
```

#### 输入
```JavaScript
// ES6模块
import { stat, exists, readFile } from 'fs';
```
上面代码的实质是从fs模块加载3个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。当然，这也导致了没法引用 ES6 模块本身，因为它不是对象。

在输入时，我们可以通过`as`来修改变量的名字：

```JavaScript
import { lastname as firstname } from './profile';
```

我们可以逐一指定加载需要的方法：

```JavaScript
import { area, circumference } from './circle';

console.log('圆面积：' + area(4));
console.log('圆周长：' + circumference(14));
```

还可以通过`*`来将这个模块一起加载：

```JavaScript
import * as circle from './circle';

console.log('圆面积：' + circle.area(4));
console.log('圆周长：' + circle.circumference(14));
```
可见，ES6 的模块机制非常的方便、实用。

> 参考文档
- [彻底弄懂CommonJS和AMD/CMD！](http://www.cnblogs.com/chenguangliang/p/5856701.html) 作者：方便以后复习
- [与 RequireJS 的异同](https://github.com/seajs/seajs/issues/277) 来自Github
- [CommonJS规范](http://javascript.ruanyifeng.com/nodejs/module.html#toc3) 作者：阮一峰
- [Module 的语法](http://es6.ruanyifeng.com/#docs/module) 作者：阮一峰