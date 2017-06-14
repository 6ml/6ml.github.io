---
layout: post
title: "数组迭代方法"
date: 2016-12-07
description: "数组迭代的六种方法"
tag: JavaScript
---

### 数组迭代方法
数组迭代方法有六种:`map`、`forEach`、`reduce`、`filter`、`every`、`some`。

话不多说，直接上图。
![图解JavaScript中数组的迭代方法](/images/posts/array_iteration/array_iteration.png)

**六种方法的区别**：
-	`map`和`forEach`的区别就是`map`会返回一个新的数组，而`forEach`只是让每一项做一件事，原数组不会改变。
-	`reduce`是让每一项参与运算，最后返回一个结果。
-	`filter`是过滤，返回一个新的数组，新的数组是由原数组经过过滤后的剩下的项的集合。
-	`every`和`some`都是检测数组中的项是否符合条件，最后返回一个`boolean`值。

> 图片来自知乎的讨论--[如何形象地解释 JavaScript 中 map、foreach、reduce 间的区别？](https://www.zhihu.com/question/24927450)

