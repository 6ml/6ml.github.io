---
layout: post
title: "Array 对象"
date: 2017-03-13
description: "Array 对象的属性和方法介绍"
tag: JavaScript
---

### Array 对象
Array对象用于在单一变量中存储多个值。
### 属性
-	`constructor`返回创建此对象的数组函数的引用
-	`length`设置或返回数组中元素的数目
-	`prototype`使您有能力想对象添加属性或者方法

### 方法

| 方法 | 参数 | 作用 | 返回值 | 是否改变原数组 |
|---|----|------|-------|-------|
| `concat()` | 值或数组对象 | 连接两个或更多的数组 | 连接以后的结果 | false |
| `join()` | 分隔符，默认为逗号 | 把数组的所有元素放入一个字符串，元素通过制定的分隔符进行分隔 | 字符串 | false |
| `pop()` | 无 | 删除数组的最后一个元素 | 返回删除的元素 | true |
| `push()` | 要添加到数组的元素，可多个 | 向数组末尾添加元素 | 新的长度 | true |
| `reserve()` | 无 | 颠倒数组中元素的顺序 | 颠倒后的数组 | true |
| `shift()` | 无 | 删除数组的第一个元素 | 返回删除的元素 | true |
| `slice()` | start，[ end ] (为负数表示倒数) | 从已有数组中返回选定的元素 | 选定的元素 | false |
| `sort()` | [ 规定排序顺序的函数 ] | 对数组进行排序 | 排序后的数组 | true |
| `splice()` | index(可负),howmany,[ items1,...(要添加的新项目) ] | 向数组中添加/删除项目 | 被删除的项目 | true |
| `toSource()` | 无 | 返回对象的源代码 | 对象源代码 | false |
| `toString()` | 无 | 把数组转换成字符串 | 字符串 | false |
| `unshift()` | 要添加的元素，可多个 | 向数组开头添加元素 | 新的长度 | true |
|`toLocaleString()`| 无 | 数组对象的本地字符串表示 | 字符串 | false |
| `valueOf()` | 无 | 返回数组对象的原始值 | 原始值 | false |

### 说明
-	`toString()`方法返回的字符串与不加参数的`join()`返回的字符串相同，`toLocaleString()`方法返回的字符串是使用地区特定的分隔符把生成的字符串连接起来。
-	`toSource()`方法和`valueOf()`方法通常由 JavaScript 在后台自动调用，并不显式地出现在代码中。
**注意：**`splice()`**会直接修改数组对象**。

> 参考文档
- [Weschool JavaScript Array 对象](http://www.w3school.com.cn/jsref/jsref_obj_array.asp)
