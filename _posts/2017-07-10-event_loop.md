---
layout: post
title: "event loop 与 micro-task 、macro-task"
date: 2017-07-10
description: "事件循环"
tag: JavaScript
---

### 事件循环机制( Event Loop )

事件循环机制就是告诉我们 JavaScript 代码应当以什么样的顺序去执行。

JavaScript 一大特点是单线程，而这个线程中拥有唯一的事件循环。JavaScript 代码的执行过程中，除了依靠函数调用栈来搞定函数的执行顺序外，还依靠任务队列( task queue )来搞定另外一些代码的执行。

一个线程中，**事件循环是唯一的，但是任务队列可以拥有多个**。

### micro-task 与 macro-task

任务队列分为 macro-task (宏任务) 与 micro-task (微任务)，最新的标准中它们被称为 task 和 jobs 。

-	macro-task 大概包括：script (整体代码) 、setTimeout 、setInterval 、setImmediate (NodeJS) 、I/O 、UI rendering

-	micro-task 大概包括：process.nextTick (NodeJS) 、Promise 、Object.observe (已废弃) 、MutationObserver (html5 新特性)

-	setTimeout / Promise 等我们称为任务源，进入任务队列的是他们指定的具体执行代码。

-	来自不同任务源的任务会进入到不同的任务队列，其中 setTimeout 和 setInterval 是同源的。

### 事件循环的顺序

事件循环的顺序决定了 JavaScript 代码的执行顺序。

-	**从 script (整体代码) 开始第一次循环**。之后进入全局上下文函数调用栈。
-	直到调用栈清空(只剩全局)，**然后执行所有的 micro-task** 。
-	当**所有可执行的 micro-task 执行完毕之后**，循环再次从 macro-task 开始，找到**其中一个任务队列**执行完毕，然后再执行所有的 micro-task ，这样一直循环下去。

### 举个栗子

大家可以尝试着想一想以下代码执行顺序应该是怎样的。

```JavaScript
console.log("-----------global start-----------");

new Promise((resolve, reject) => {
    console.log("promise in global");

    resolve();
}).then(() => {
    console.log("promise.then in global");
});

process.nextTick(() => {
    console.log("nextTick in global");
});

setTimeout(() => {
    console.log("setTimeout1");

    new Promise((resolve, reject) => {
        console.log("promise in setTimeout1");

        resolve();
    }).then(() => {
        console.log("promise.then in setTimeout1");
    });
});

setImmediate(() => {
    console.log("setImmediate1");

    new Promise((resolve, reject) => {
        console.log("promise in setImmediate1");

        resolve();
    }).then(() => {
        console.log("promise.then in setImmediate1");
    });
});

setTimeout(() => {
    console.log("setTimeout2");

    new Promise((resolve, reject) => {
        console.log("promise in setTimeout2");

        resolve();
    }).then(() => {
        console.log("promise.then in setTimeout2");
    });
});

setImmediate(() => {
    console.log("setImmediate2");

    new Promise((resolve, reject) => {
        console.log("promise in setImmediate2");

        resolve();
    }).then(() => {
        console.log("promise.then in setImmediate2");
    });
});

console.log("-----------global end-----------");
```
#### 结果

最后的结果是这个样子的: 
![event_loop](/images/posts/event_loop/event_loop.png)

我们来看看为什么结果是这个样子。

#### 第一次循环：

-	从 script (整体代码) 开始执行，先*打印出 global start*
-	然后遇到 Promise ，立即执行传入构造函数 Promise 的代码，*打印 promise in global* ，并将 promise.then 放入 micro-task 的一个队列中
-	然后遇到 nextTick ，这也是一个 micro-task ，放入 micro-task 另一个队列中
-	再遇到 setTimeout ，将其放入 macro-task 的一个任务队列中
-	又遇到 setImmediate ，将其放入 macro-task 的另一个任务队列中
-	再次遇到一个 setTimeout ，将其放入 macro-task 中已经放了一个 setTimeout 的队列中，此时这个队列中有两个 setTimeout
-	再次遇到 setImmediate ，同上，放入 macro-task 中已经存放了一个 setImmediate 的队列中
-	最后打印 *global end* 

> 此时，第一次循环的 macro-task 的一个任务队列( script )执行完毕，开始执行 micro-task ，此时 micro-task 中有两个任务队列，一个是包含 Promise 的任务队列，另一个是包含 nextTick 的任务队列

-	由于 nextTick 任务队列会比 Proise 任务队列先执行，所以执行 nextTick ，*打印 nextTick in global*
-	其次，执行 Promise.then ，*打印 promise.then in global*

此时，可执行的 micro-task 全部执行完毕，第一次循环结束！

#### 第二次循环

现在 micro-task 中没有任务队列，而 macro-task 中有两个任务队列: 一个是包含两个 setTimeout 的任务队列，另一个是包含两个 setImmediate 的任务队列

由于 setTimeout 任务队列会比 setImmediate 任务队列先执行，所以执行 setTimeout 任务队列。

-	首先，执行第一个 setTimeout ，先*打印 setTimeout1* 
-	然后遇到 Promise ，先传入执行 Promise 构造函数中的函数，*打印 promise in setTimeout1*，其次将 Promise.then 放入 micro-task 任务队列
-	再执行第二个 setTimeout ，同上

> 此时，setTimeout 任务队列执行完毕，开始执行 micro-task ，此时 micro-task 中有一个任务队列，即 Promise 的任务队列

-	按顺序执行 Promise 任务队列中的 Promise.then ，先后*打印 promise.then in setTimeout1 和 promise.then in setTimeout2*

此时，可执行的 micro-task 全部执行完毕，第二次循环结束！

#### 第三次循环

现在 micro-task 中没有任务队列，而 macro-task 中有一个任务队列: 包含两个 setImmediate 的任务队列

-	首先，执行第一个 setImmediate ，先*打印 setImmediate1* 
-	然后遇到 Promise ，先传入执行 Promise 构造函数中的函数，*打印 promise in setImmediate1*，其次将 Promise.then 放入 micro-task 任务队列
-	再执行第二个 setImmediate ，同上

> 此时，setImmediate 任务队列执行完毕，开始执行 micro-task ，此时 micro-task 中有一个任务队列，即 Promise 的任务队列

-	按顺序执行 Promise 任务队列中的 Promise.then ，先后*打印 promise.then in setImmediate1 和 promise.then in setImmediate2*

此时，可执行的 micro-task 全部执行完毕，第三次循环结束！

至此，所有的循环执行完毕，micro-task 任务队列与 macro-task 任务队列皆为空。

### 需要注意

在 micro-task 中，**process.nextTick 队列会比 Promise.then 队列先执行**。 process.nextTick 中的可执行任务执行完毕之后，才会开始执行 Promise 队列中的任务。

在 macro-task 中， **setTimeout 队列会比 setImmediate 队列先执行**。

> 参考文档
- [前端基础进阶（十二）：深入核心，详解事件循环机制](http://www.jianshu.com/p/12b9f73c5a4f) 作者：波同学