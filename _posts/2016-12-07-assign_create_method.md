---
layout: post
title: "Object对象 assign create 方法"
date: 2016-12-07
description: "Object对象 assign create 方法"
tag: JavaScript
---

### Object.assign()
JavaScript对象属性拷贝的方法。在我们需要将一个或多个对象属性复制到目标对象时，可以使用这个方法。
#### 语法
```
Object.assign(target, ...sources)
```
-	`target`：目标对象
-	`sources`：源对象，可以是一个或多个
-	返回值：复制`sources`属性后的`target`

`Object.assign()`方法会复制一或多个源对象的属性（可枚举）到目标对象，并返回目标对象

`Object.assign()`方法只会拷贝**源对象自身的并且可枚举的属性**（即：该方法会执行源对象的访问器属性`getter`函数），并把复制值拷贝给目标对象，如果你想拷贝访问器属性本身，应该使用`Object.getOwnPropertyDescriptor()`和`Object.defineProperties()`方法（字符串类型和`symbol`类型的属性都会被拷贝）。

**异常**
拷贝对象属性时可能会产生异常（如：目标对象的某个只读属性和源对象的某个属性同名时），这时会抛出一个`TypeError`异常并中断拷贝过程，异常之后的属性不会再被复制，但已复制的属性不受影响。`Object.assign()`会跳过值为`null`和`undefined`的源对象属性。

#### 使用
`assign()`方法可以浅拷贝一个对象，可以合并多个对象，也可以用于复制`symbol`类型的属性，但是**不能用于继承和隐藏属性的复制**。
```
var source = {  //这是一个属性描述符对象

	name: {
    
    	value: 'it笔录'  //name 是不可枚举属性
    
    },
    
    {
    
    	value: 'itbilu',
        
        enumerable : true  //domain 是可枚举属性
    
    }

};

var obj = Object.create({foo : 1}, source);

var copy = Object.assign({}, obj);

console.log(copy);  // {domain : 'itbilu'}
```
这里只有`copy`只有一个属性，因为源对象的属性是继承下来的，所以`foo`属性无法复制，属性描述符对象中的`name`属性属于不可枚举类型，也无法复制，所以可以复制的只有`domain`属性。
### Object.create()
```
Object.create( proto, [ propertiesObject ] )
```
在上面用到了`Object.create()`方法，这个方法接受一个源对象参数和一个可选的属性描述符对象，源对象参数只能为`null`或者是对象。

`Object.create()`方法可以用于创建对象，实现继承，第一个参数即是创建的对象的原型对象。

`propertiesObject`参数对象是一组属性与值，该对象的属性名称将是新创建的对象的属性名称，值是属性描述符。**注意，该参数不能使**`undefined`**，另外只有该对象中自身拥有的可枚举的属性才有效，也就是说该对象原型链上属性是无效的。**