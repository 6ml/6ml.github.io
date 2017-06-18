---
layout: post
title: "浅谈ES6"
date: 2016-12-01
description: "简单讲解一下 ES6 的语法"
tag: JavaScript
---

### ES6
ECMAScript 6(简称ES6)是JavaScript语言的下一代标准。ES6在2015年发布，又称ECMAScript 2015。
### 最常用的ES6特性
`let`,`const`,`class`,`extends`,`super`,`arrow function`,`template string`,`destructuring`,`default`,`rest`,`spread`,`import`,`export`
### let,const
这两个的用途与`var`类似，都是用来声明变量的，但又有各自不同的用途。
由于ES5中只有全局作用域和函数作用域，没有块级作用域，带来了很多不合理的场景。第一种是内层变量覆盖外层变量，第二种是用来计数的循环变量泄漏为全局变量。
例如`for`循环：
```
 var a[];
 
 for(var i = 0;i < 10;i++){
 
    a[i] = function(){
    
    	console.log(i);
    
    }
 
 }
 
 a[6]();//10
```
上面代码中，变量是`var`声明的，在全局范围内都有效。所以每一次循环，新的值都会覆盖旧值，导致最后输出的是最后一轮的值。上述问题可以由闭包来解决。
```
 function iteratorFactory(i){
 
    var onclick = function(e){
    
    	console.log(i);
    
    }
    
    return onclick;
 
 }

 var clickBoxs = document.querySelectorAll('.clickbox');
 
 for(var i = 0;i < clickBoxs.length;i++){
 
	clickBoxs[i].onclick = iteratorFactory(i);
 
 }
```
我们再看看用`let`来解决这个问题。
```
 var a = [];
 
 for(let i = 0;i < 10;i++){
 
    a[i] = function(){
    
    	console.log(i);
    
    }
 
 }
 
 a[6]();//6
```
这是因为`let`为JavaScript新增了块级作用域，用它声明的变量，只在`let`所在的代码内有效。但是`let`没有了`var`所具有变量提升的功能，在`let`定义变量前使用变量会报错。

`const`也是用来声明变量的，但是声明的是常量。一旦声明，常量的值就不能改变。
```
 const PI = Math.PI;
 
 PI = 23; //Module build failed: SyntaxError: /es6/app.js: "PI" is read-only
```
`const`有一个很好的应用场景，就是当我们引用第三方库时声明的变量，用它来声明可以避免未来不小心重命名而导致bug。
```
 const moment = require('moment');
```

### class,extends,super
这三个特性涉及了ES5中最头疼的几个部分：原型、构造函数、继承...
ES6提供了更接近传统语言的写法，引入Class(类)这个概念。新的`class`写法让对象原型的写法更加清晰，更像面向对象编程的写法，也更加通俗易懂。
```
 class Animal{
 
    constructor(){
    
    	this.type = 'animal';
    
    }
    
    says(say){
    
    	console.log(this.type + 'says' + say);
    
    }
 
 }
 
 let animal = new Animal();
 
 animal.says('hello'); //animal says hello
 
 class Cat extends Animal{
 
    constructor(){
    
    	super();
        
        this.type = 'cet';
    
    }
 
 }
 
 let cat = new Cat();
 
 cat.says('hello'); //cat says hello
```
上面代码首先用`class`定义了一个“类”，里面有一个`constructor`方法，这就是构造方法，而`this`关键字则代表实例对象。简单地说，`constructor`内定义的方法和属性是实例对象自己的，而`constructor`外定义的方法和属性则是所有实例对象可以共享的。

Class之间可以通过`extends`关键字实现继承，这比ES5通过修改原型链实现继承要清晰和方便很多。Cat类通过`extends`关键字继承了Animal类所有的属性和方法。

`super`关键字，它指代父类的实例(即父类的this对象)。子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类没有自己的`this`对象，而是继承父类的`this`对象，然后对其进行加工。如果不调用`super`方法，子类就得不到`this`对象。

ES6的继承机制，实质是先创造父类的实例对象this(所以必须先调用super方法)，然后再用子类的构造函数修改`this`。

PS.如果你写react的话，就会发现以上三个东西在最新版React中出现得很多。创建的每个component都是一个继承`React.Component`的类。[详见React文档](https://facebook.github.io/react/docs/components-and-props.html)

### arrow function
也就是我们所说的箭头函数了。这个几乎是ES6最常用的一个新特性了，用它来写function十分的简洁清晰。
```
 function(i){ return i + 1; } //ES5
 
 (i) => i + 1; //ES6
```
当参数只有一个的时候也可以不写括号：
```
 i => i + 1;
```
箭头函数参数还支持 **Rest** 和**默认值**以及**解构**语法。
```
 (param1, param2, ...rest) => { statements }
 
 (param1 = defaultValue1, param2, …, paramN = defaultValueN) => { statements }

 var f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => a + b + c;
 
 f();  // 6
```
如果函数体比较复杂，则需要用`{}`把代码包起来。
```
 function(x,y){
 
    x++;
    
    y++;
    
    return x + y;
 
 }
 
 (x,y) => {x++; y++; return x+y;}
```
除了看上去简洁以外，`arrow function`还有一个很强大的功能！
长期以来，JavaScript语言中的`this`对象一直是一个令人头痛的问题，在对象方法中使用this，必须非常小心，例如：
```
 class Animal{
 
    constructor(){
    
    	this.type = 'animal';
        
    }
    
    says(say){
    
    	setTimeout(function(){
        
        	console.log(this.type + 'says' +say);
        
        },1000);
    
    }
 
 }
 
 var animal = new Animal();
 
 animal.says('hello'); //undefined says hi
```
运行上面的代码会报错，因为`setTimeout`中的`this`指向的是全局对象。为了避免这个问题，传统解决方案有两种：
- 第一种是将this传给self，再用self来只带this

```
 says(say){
	
    var self = this;
    
    setTimeout(function(){
    
    	console.log(self.type + 'says' + say);
    
    },1000)
    
 }
```

- 第二种是用`bind`方法

```
 says(say){
	
    setTimeout(function(){
    
    	console.log(this.type + 'says' + say);
    
    }.bind(this),1000);
    
 }
```

但是现在我们有了箭头函数，就不用这么麻烦了。

```
 says(say){
	
    setTimeout( () => {
    
    	console.log(this.type + 'says' +say);
    
    },1000);
    
 }
```

当我们使用箭头函数时，函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this对象，它的this是继承外面的，因此内部的this就是外层代码块的this。

**注意：箭头函数如果返回对象字面量的话要用括号括起来**,如下：

```
 const func = () => ({foo : '1'});
```

### template string
这个东西也非常有用，当我们要插入大段的html内容到文档中时，传统的写法非常麻烦，所以之前我们通常会引用一些模板工具库，比如mustache等等。
按照传统方法我们会这样写这段代码：
```
 $('#result').append(
	
    "there are <b>" + basket.count + "</b>" +
    
    "item in your basket," +
    
    "<em>" + basket.onSale +
    
    "</em> are on sale"
    
 );
```
我们需要用一大堆的'+'号来连接文本与变量，而使用ES6的新特性模板字符串` `` `后，我们可以直接这么写：
```
 $('#resulr').append(`
	
    there are <b>${basket.count}</b> items in your basket,<em>${basket.onSale}</em> are on sale!
    
 `);
```
用反引号<code class="highlighter-rouge">`</code>来标识起始，用`${}`来引用变量，而且所有的空格和缩进都会被保留在输出之中。

### destructuring
ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构(Destructuing)。
看看下面的例子：
```
 let cat = 'ken';
 
 let dog = 'lili';
 
 let zoo = {cat: cat,dog: dog};
 
 console.log(zoo); //Object {cat: 'ken',dog: 'lili'}
```
用ES6完全可以像下面这样写：
```
 let cat = 'ken';
 
 let dog = 'lili';
 
 let zoo = {cat: cat,dog: dog};
 
 console.log(zoo); //Object {cat: 'ken',dog: 'lili'}
```
反过来可以这么写：
```
 let dog = {type: 'animal', many : 2};
 
 let {type, many} = dog;
 
 console.log(type, many); //animal 2
```

### default,rest,spread
`default`很简单，意思就是默认值。我们调用函数时忘了传参数时默认的方法是加上一句`type = type || 'cat'`来指定默认值。
```
 function animal(type){
	
    type = type || 'cat';
    
    console.log(type);
    
 }
```
如果用ES6我们可以这么写：
```
 function animal(type = 'cat'){
	
    console.log(type);
    
 }
```
`rest`和`spread`操作都是用三个点`...`表示，但作用正好相反。`rest`一般用在函数参数的声明中，而`spread`用在函数的调用中。
直接看例子：
```
 function animals(...types){
	
    console.log(types);
    
 }
 
 animals('cat','dog','fish'); //["cat","dog","fish"]
```
如果不用ES6的话，我们则得使用ES5的`arguments`。但是在严格模式`strict mode`下，对`arguments`做了很多限制，它是个`arrayLike`对象，不能像操作数组那样直接操作它。但用`rest`后，`types`参数就是一个数组了，任何操作数组的方法都可以直接对`types`使用。
需要注意的是：`rest`**只能放在形参最后，其实也很好理解，rest单词就是剩余的，把剩余的都归集到一起**。
```
 function f(x,...y){
	
    console.log(x, y);
    
 }
 
 f('a', 'b', 'c'); //a,['b', 'c']
 
 f('a'); //x = 'a',y = []
 
 f(); //x = undefined, y = []
```
将`rest`放在形参中间，浏览器会报错。
```
 function f(x,...y,z){ //Uncaught SyntaxError : Rest parameter must be last formal parameter!
	
    console.log(x,y,z);
    
 }
 
 f('a', 'b', 'c');
```
`spread`主要用在两种情况下：函数传参和数组生成。
`spread`后面应该是个iterator，在JS中，数组，字符串本身就是iterator，但对象不是。
```
 function foo(x, y, z){
	
    console.log(x, y, z);
    
 }
 
 foo(...[1,2,3]); //1 2 3
 
 foo(3,...[4,5]); // 3 4 5
```
上面是数组的例子，再看个字符串的：
```
 var arr = [];
 
 arr.push(..."abc");
 
 console.log(arr); //['a','b','c']
```

再看看用在数组生成上：
```
 [1,...[2, 3], 4] //[1, 2, 3, 4]
 
 
 let x = ['a', 'b'];
 
 let y = ['c'];
 
 let z = ['d', 'e'];
 
 let arr = [...x, ...y, ...z]; //['a', 'b', 'c', 'd', 'e']
```

### import,export
这两个家伙对应的就是ES6自己的`module`功能。
> ES6模块的设计思想，是尽量的静态化，是得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS和AMD模块，都只能在运行时确定这些东西。

如果我们有两个js文件`index.js`和`content.js`，我们想在`index.js`中使用`content.js`返回的结果。
**require.js**写法：
```
 //content.js
 
 define('content.js',function(){
	
    retuan 'a cat';
    
 })
```
然后require:
```
 //index.js
 
 require({'./content.js'},function(animal){
	
    console.log(animal); //a cat
    
 })
```
**CommonJS**写法：
```
 //index.js
 
 var animal = require('./content.js');
 
 //content.js
 
 module.exports = 'a cat';
```
**ES6**写法：
```
 //index.js
 
 import animal from './content.js';
 
 //content.js
 
 export default 'a cat';
```

#### ES6 module的其他高级用法
```
 //content.js
 
 export default 'a cat';
 
 export function say(){
	
    return 'hello';
    
 }
 
 export const type = 'dog';
```
由上面可以看出，`export`除了输出变量，还可以输出函数，甚至是类(react的模块基本都是输出类)。
```
 //index.js
 
 import animal, { say, type} from './content.js';
 
 let says = say();
 
 console.log(`the ${type} says ${says} to ${animal}`) //the dog says hello to a cat
```
这里输入的时候要注意：**大括号里面的变量名，必须与杯倒入模块对外接口的名称相同，输入content.js输出的默认值(default)，要写在大括号外面**。
#### 修改变量名
在ES6中，可以使用`as`实现一键换名。
```
 //index.js
 
 import animal, { say, type as animalType} from './content.js';
 
 let says = say();
 
 console.log(`the ${animalType} says ${says} to ${animal}`); //the dog says hello to a cat
```
#### 模块的整体加载
除了指定加载某个输出值，还可以使用整体加载，即用`*`指定一个对象，所有输出值都加载到这个对象上。
```
 //index.js
 
 import animal, * as content from './content.js';
 
 let says = content.say();
 
 console.log(`the ${content.type} says ${says} to ${animal}`); //the dog says hello to a cat
```
通常`*`和`as`一起使用比较合适。
#### 终极秘籍
上面的`content.js`一共输出了三个变量(`default`,`say`,`type`),假如我们实际项目当中只需要`type`这一个变量，其余两个暂时不要。我们可以值输入一个变量：
```
 import { type} from './content.js';
```
由于其他两个变量没有被使用，我们希望打包的时候也忽略它们，抛弃它们，这样在大项目中可以显著减少文件的体积。

ES6帮我们实现了，但是目前无论是webpack还是browserify都还不支持这一功能。如果你现在就想实现这个功能，可以尝试使用[roolup.js](http://rollupjs.org/)。他们把这个功能叫做Tree-shaking。

>参考文档：
- [30分钟掌握ES6/ES2015核心内容（上）](https://segmentfault.com/a/1190000004365693) 作者：zach5078
- [30分钟掌握ES6/ES2015核心内容（下）](https://segmentfault.com/a/1190000004368132) 作者：zach5078
- [ES6学习](http://blog.csdn.net/kittyjie/article/category/5965133)作者：kittyjie
