---
layout: post
title: "JavaScript 原型 继承"
date: 2017-03-16
description: "JavaScript 实现继承的几种方法"
tag: JavaScript
---

### 如何创建一个类/对象
-	**工厂模式**(看看就好，别用)
-	**构造函数 + 原型模式**(常用)
-	**ES5 的 Object.create() 方法**(不常用)
-	**ES6 的 class**(现代前端开发必备)
-	**寄生构造函数模式**

#### 工厂模式(函数模式)
```Javascript
function createPerson(name) {  
    var o = new Object();

    o.name = name;
    o.sayName = function () {
        alert(this.name);
    };

    return o;
}

var person = createPerson("张三");

person.sayName();   // 张三

alert(person instanceof createPerson); //false 
```
缺点：
-	instanceof 无法检测原型

#### 构造函数 + 原型模式
```JavaScript
function Person(name){  
    this.name=name;
}
Person.prototype = {  
    constructor: Person, // 关于此行代码的解释请看文章最后一个问题中constructor的描述
    sayName: function() {
        alert(this.name);
    }
}

var person1 = new Person("张三"),  
    person2 = new Person("李四");  

person1.sayName();  // 张三  
person2.sayName();  // 李四

alert(person1 instanceof Person);  // true  
alert(person2 instanceof Person);  // true 
```
优点：
-	灵活
	1.	没有显式地创建对象	
	2.	直接将属性和方法赋给了 this 对象
	3.	利用 new 操作符，无 return
-	无工厂模式的对象方法函数冗余问题
-	无工厂模式中对象类别的识别问题
-	符合面向对象思想

缺点：
-	封装性较差(可通过分析原型链动态添加原型方法)

```JavaScript
// 动态原型
function Person(name) {  
    this.name = name;

    if (typeof this.sayName != "function") {
        // 注意：这时不能使用对象字面量定义原型
        // 因为这时的原型定义放到了构造函数里，用字面量
        // 定义会重写原型从而会切断构造函数和原型的联系。
        Person.prototype.sayName = function () {
            alert(this.name);
        }
    }
}

var person = Person("张三");  
person.sayName();  // 张三  
```
#### ES5 的 Object.create()方法
用这个方法，"类"就是一个对象，不是函数。
```JavaScript
var Person = {  
    name: "张三",
    sayName: function () {
        alert(this.name);
    }　　
};
``` 
然后，直接用 Object.create() 生成实例，不需要用到 new 。
```JavaScript
var person = Object.create(Person);  
person.sayName(); // 张三
```
目前，各大浏览器的最新版本（包括IE9）都部署了这个方法。如果遇到老式浏览器，可以用下面的Polyfill自行部署。
```JavaScript
if (!Object.create) {  
    Object.create = function (o) {　　　　
        function F() {}　　　　　　　　
        F.prototype = o;　　　　　　
        return new F();　　　　
    };　　
}
```
#### ES6 的 class
ES6 新加入的 class 关键词是语法糖，其本质还是函数。
```JavaScript
class Person {  
    constructor(name) {
        this.name = name;
    }
    sayName() {
        return this.name;
    }    
}

var person = new Person('张三');

person.sayName();   // 张三
```
class 声明体在严格模式下运行。构造函数(constructor)是可选的。

class 声明不可以提升(这点和函数声明不同)。
#### 寄生构造函数模式
《JavaScript高级程序设计》上讲得很玄幻。但一句话解释，它就是工厂模式 + new
```JavaScript
function Person(name){  
    var o = new Object();

    o.name = name;
    o.sayName = function(){
        alert(this.name);
    };

    return o;
}

var person = new Person("zhangsan");  
alert(person instanceof Person);  //false
```
实际应用场景：当你需要去基于一个原生 Js 对象进行功能扩充，但又不想在原生对象的原型链上添加新属性，则这就是一个两全其美的办法。
```JavaScript
function SpecialArray(){

    //创建一个默认数组
    var values = new Array();

    //添加值
    values.push.apply(values, arguments);

    //添加方法
    values.toPipedString = function(){
        return this.join("|");
    };

    //返回新的数组
    return values;
}

var colors = new SpecialArray("red", "blue", "green");  
alert(colors.toPipedString()); //"red|blue|green"

alert(colors instanceof SpecialArray);  //false
```
### JS 实现继承的方法
-	原型链继承
-	借用构造函数继承
-	组合继承
-	原型继承
-	寄生继承
-	寄生组合式继承
-	ES6 extends 实现继承

#### 原型链继承
基本思想：利用原型让一个引用类型继承另一个引用类型的属性和方法。
```JavaScript
function SuperType() {  
    this.property = true;
}
SuperType.prototype.getSuperValue = function() {  
    return this.property;
}
function subType() {  
    this.property = false;
}
//继承了SuperType
SubType.prototype = new SuperType();  
SubType.prototype.getSubValue = function (){  
    return this.property;
}

var instance = new SubType();  
console.log(instance.getSuperValue());//true
```
缺点：
-	所有实例共享超类中定义的引用类型
-	创建子类实例时，无法向超类传参

#### 借用构造函数
基本思想：在子类型构造函数的内部调用超类构造函数，通过使用 call()和 apply()方法可以在新创建的对象上执行构造函数。
```JavaScript
function SuperType() {  
    this.colors = ["red","blue","green"];
}
function SubType() {  
    SuperType.call(this);//继承了SuperType
}
var instance1 = new SubType();  
instance1.colors.push("black");  
console.log(instance1.colors);//"red","blue","green","black"

var instance2 = new SubType();  
console.log(instance2.colors);//"red","blue","green"  
```
缺点：
-	方法都在构造函数中定义，无法函数复用
-	超类的原型上定义的方法，子类不可见

#### 组合继承
基本思想：将原型链和借用构造函数的技术组合在一块，从而发挥两者之长的一种继承模式。
```JavaScript
function SuperType(name) {  
    this.name = name;
    this.colors = ["red","blue","green"];
}
SuperType.prototype.sayName = function() {  
    console.log(this.name);
}
function SubType(name, age) {  
    SuperType.call(this,name);//继承属性
    this.age = age;
}
//继承方法
SubType.prototype = new SuperType();  
Subtype.prototype.constructor = Subtype;  
Subtype.prototype.sayAge = function() {  
    console.log(this.age);
}

var instance1 = new SubType("EvanChen",18);  
instance1.colors.push("black");  
consol.log(instance1.colors);//"red","blue","green","black"  
instance1.sayName();//"EvanChen"  
instance1.sayAge();//18

var instance2 = new SubType("EvanChen666",20);  
console.log(instance2.colors);//"red","blue","green"  
instance2.sayName();//"EvanChen666"  
instance2.sayAge();//20  
```
优点：
-	既通过在原型上定义方法实现了函数复用，又能保证每个实例都有自己的属性。

#### 原型式继承
基本想法：借助原型可以基于已有的对象创建新对象，同时还不必须因此创建自定义的类型。

原型式继承的思想可用以下函数来说明：
```JavaScript
function object(o) {  
    function F(){}
    F.prototype = o;
    return new F();
}
```
例子：
```JavaScript
var person = {  
    name:"EvanChen",
    friends:["Shelby","Court","Van"];
};

var anotherPerson = object(person);  
anotherPerson.name = "Greg";  
anotherPerson.friends.push("Rob");

var yetAnotherPerson = object(person);  
yetAnotherPerson.name = "Linda";  
yetAnotherPerson.friends.push("Barbie");

console.log(person.friends);//"Shelby","Court","Van","Rob","Barbie"
//ECMAScript5 通过新增 Object.create()方法规范化了原型式继承，这个方法接收两个参数：一个用作新对象原型的对象和一个作为新对象定义额外属性的对象。

var person = {  
    name:"EvanChen",
    friends:["Shelby","Court","Van"];
};

var anotherPerson = Object.create(person);  
anotherPerson.name = "Greg";  
anotherPerson.friends.push("Rob");

var yetAnotherPerson = Object.create(person);  
yetAnotherPerson.name = "Linda";  
yetAnotherPerson.friends.push("Barbie");

console.log(person.friends);//"Shelby","Court","Van","Rob","Barbie"
```
缺点：
-	包含引用类型值的属性始终都会共享相应的值

#### 寄生式继承
基本思想：创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后再像真正是它做了所有工作一样返回对象。
```JavaScript
function createAnother(original) {  
    var clone = object(original);
    clone.sayHi = function () {
        alert("hi");
    };
    return clone;
}

var person = {  
    name:"EvanChen",
    friends:["Shelby","Court","Van"];
};
var anotherPerson = createAnother(person);  
anotherPerson.sayHi();///"hi"
```
缺点：
-	未做到函数复用

#### 寄生组合式继承
基本思想：通过借用函数来继承属性，通过原型链的混成形式来继承方法 其基本模型如下所示：
```JavaScript
function inheritProperty(subType, superType) {  
    var prototype = object(superType.prototype);//创建对象
    prototype.constructor = subType;//增强对象
    subType.prototype = prototype;//指定对象
}
```
例子：
```JavaScript
function SuperType(name){  
    this.name = name;
    this.colors = ["red","blue","green"];
}
SuperType.prototype.sayName = function (){  
    alert(this.name);
};

function SubType(name,age){  
    SuperType.call(this,name);
    this.age = age;
}
inheritProperty(SubType,SuperType);  
SubType.prototype.sayAge = function() {  
    alert(this.age);
}
```
优点：
-	高效率(只调用了一次 SuperType 构造函数，并且因此避免了在 SubType.prototype 上创建不必要的属性)
-	原型链保持不变(还能正常用 instanceof 和 isPrototypeOf()方法)

#### ES6 extends 实现继承
```JavaScript
//定义类
class Point {
    constructor(x, y) {    //constructor 构造方法
        this.x = x;
        this.y = y;
    }

    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }
}

var p = new Point(1, 2);
```
class 之间可以通过 extends 关键词时间继承。
```JavaScript
class ColorPoint extends Point {
    constructor(x, y, color) {
        super(x, y); // 调用父类的constructor(x, y)
        this.color = color;
    }

    toString() {
        return this.color + ' ' + super.toString(); // 调用父类的toString()
    }
}
```
在上述代码中，`constructor`和`toString`方法之中，都出现了`super`关键词，它在这里表示父类的构造函数，用来新建父类的`this`对象。

子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。因为子类没有自己的`this`对象，而是继承父类的`this`对象，然后对其进行加工。如果不调用`super`方法，子类就得不到`this`对象。

> 参考文档：
- 《JavaScript 高级程序设计》
- [ES6新特性5：类(Class)和继承(Extends)](http://www.cnblogs.com/lishuxue/p/6097575.html) 作者：一只前端路上的小白