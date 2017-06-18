---
layout: post
title: "浏览器渲染页面过程"
date: 2017-04-20
description: "浏览器渲染页面过程 reflow repaint"
tag: Others
---

### 解析
浏览器得到后台返回的数据后，开始渲染页面，首先解析数据，其次渲染页面。

浏览器工作大致流程
![浏览器工作大致流程](/images/posts/browser/browser_0.png)

浏览器会解析三个东西：
-	HTML / SVG / XHTML ，浏览器会将HTML解析成一个 DOM Tree ，构建过程是深度遍历：当前节点的所有子节点都构建好后才会去构建当前节点的下一个兄弟节点；
-	CSS ，浏览器解析 CSS 生成 CSS Rule Tree 。*对 CSS 代码中非法的语法她会直接忽略掉。* **解析CSS的时候会按照如下顺序来定义优先级：浏览器默认设置 < 用户设置 < 外链样式 < 内联样式 < html 中的 style**；
-	JavaScript ，脚本，主要是通过 DOM API 和 CSSOM API 来操作 DOM Tree 和 CSS Rule Tree。

### 渲染
-	解析完成后，浏览器会根据 DOM Tree 和 CSS Rule Tree 来构建 Rendering Tree ；
-	Layout – 定位坐标和大小，是否换行，各种 position , overflow , z-index 属性 ……

**Rendering Tree 不等于 DOM Tree ，DOM Tree 和 html 标签是完全一一对应的；而Rendering Tree 会去除一些不显示的元素，如 Head 或设置 display: none 的元素。而且一大段文本中的每一个行在 Rendering Tree 中都是独立的一个节点。Rendering Tree 中的每一个节点都存储有对应的 css 属性。**

CSS 的 Rule Tree 主要是为了完成匹配并把 CSS Rule附加上 Rendering Tree 上的每个 Element 。也就是 DOM 结点。也就是所谓的 Frame 。
然后，计算每个 Frame (也就是每个 Element )的位置，这又叫 Layout 或 Reflow 过程。

### Repaint
重绘，指页面的一部分需要重画，但是没有大小、位置上的改变，如：background-color 等的改变。

### Reflow
回流，指元素的几何尺寸发生变化，如宽高、位置等变化，需要重新验证并计算渲染树，是渲染树的一部分或者是全部发生了变化。

DOM Tree里的每个结点都会有reflow方法，一个结点的reflow很有可能导致子结点，甚至父点以及同级结点的reflow。

**HTML 使用的是流式布局(flow based layout)，所以，如果元素的几何尺寸发生变化，就会影响到后面的其他元素。**

**发生 Reflow 必定发生 Repaint ，但发生 Repaint 不一定发生 Reflow**

#### 可能导致 Reflow 的原因
1.	Initial ，网页初始化；
2.	Incremental ，一些 Javascript 在操作 DOM Tree；
3.	Resize ，其些元素的尺寸改变；
4.	StyleChange ，CSS 属性发生变化；
5.	Dirty ，几个 Incremental 的 reflow 发生在同一个 Frame(即元素) 的子树上。

#### 成本很高的操作
-	增加、删除、修改 DOM 结点时，会导致 Reflow 或 Repaint ；
-	移动 DOM 的位置，或是搞个动画的时候；
-	内容发生变化；
-	修改 CSS 样式的时候；
-	Resize 窗口的时候(移动端没有这个问题)，或是滚动的时候；
-	修改网页的默认字体时。

#### 聪明的浏览器
浏览器不会当我们每改一次样式，它就 Reflow 或 Repaint 一次。一般来说，浏览器会把这样的操作积攒一批，然后做一次 Reflow，这又叫异步 Reflow 或增量异步 Reflow 。

但是有些情况浏览器是不会这么做的，比如：resize 窗口，改变了页面默认的字体等。对于这些操作，浏览器会马上进行 Reflow。

### 减少 Reflow 方法
-	不要一条一条地修改 DOM 的样式。通过修改 DOM 的 className 来一次修改多个样式；
-	把DOM离线后修改；
	1.	使用 documentFragment 对象在内存里操作 DOM；
	2.	先把 DOM 给 display: none ，然后修改完成后再设置 display ，只会发生两次 Reflow；
	3.	clone 一个 DOM 结点到内存里，修改完成后再替换，只会发生一次 Reflow。
-	不要把DOM结点的属性值放在一个循环里当成循环里的变量，不然这会导致大量地读写这个结点的属性；
-	尽可能的修改层级比较低的 DOM 。当然，改变层级比较底的 DOM 有可能会造成大面积的 Reflow ，但是也可能影响范围很小；
-	为动画的 HTML 元素使用 fixed 或 absoult 的 position ，这样就不会造成其他元素的 Reflow；
-	千万不要使用 table 布局，因为可能很小的一个小改动会造成整个 table 的重新布局。

### 浏览器加载资源流程
-	浏览器向服务器发出请求，服务器返回 html 文件，浏览器开始载入html代码并解析；
-	&lt;head&gt;标签内有一个&lt;link&gt;标签引用外部 CSS 文件，下载 CSS 文件。但**解析器没有阻塞**，HTML 依然继续解析但是不会显示，因为没有 CSS 还没到手呢；
-	如果 CSS 是嵌入式的，没关系，只是不用去下载了。CSS 拿到手，为了更好的用户体验，渲染引擎将会尽可能早的将内容呈现到屏幕上，并不会等到所有的 html 都解析完成之后再去构建和布局 render 树。它是**解析完一部分内容就显示一部分内容**，同时，可能还在通过网络下载其余内容；
-	接下来遇到了 script 标签，如果是外部文件，好了，添加到下载的队列中。由于 **script 是必须立刻执行的，所以下载完之前，解析器也阻塞了**。如果是嵌入式的就会立刻执行；
-	接下来在代码中发现一个&lt;img&gt;标签引用了一张图片，向服务器发出请求。**此时浏览器不会等到图片下载完，而是继续渲染后面的代码**。 等**下载完图片文件**后，由于图片占用了一定面积，影响了后面段落的排布，因此浏览器需要**回过头来重新渲染这部分代码**；
-	别忘了，后面也许还会有 CSS 内联代码和 js 内联代码。如果影响了布局浏览器就需要回去重新渲染，即 Reflow ；如果不影响布局只是改变了某个元素的背景颜色，文字颜色等，将只会引起浏览器的 Repaint ，重画某一部分。

### 减少 JavaScript 对性能的影响
-	 将标签放在body标签里的最后的位置，确保在脚本执行前页面已经完成了 DOM 树渲染；
-	尽可能地合并脚本。页面中的script标签越少，加载也就越快，响应也越迅速。无论是外链脚本还是内嵌脚本都是如此；
-	采用无阻塞下载 JavaScript 脚本的方法：
	1.	设置 defer = "defer" ，脚本会被延迟到整个页面都解析完毕后（遇到）再运行，如果有多个脚本的话，H5 规定顺序执行。但实际中不一定顺序执行，不同的浏览器支持程度也不同;
	2.	设置 async，效果和 defer 一样，只是他是肯定不支持顺序执行多个脚本的

### 编写 CSS 需要注意
CSS 匹配 HTML 元素是一件相当复杂和费性能的事。

CSS 选择符是**从右到左**进行匹配的。所以 .class li 在匹配时先找到所有 li 元素，然后再去确定其父元素是否有对应的 class 。

所以，我们在编写 CSS 时应注意：
-	DOM 深度尽量浅；
-	减少 inline JavaScript 、CSS 的数量；
-	使用现代合法的 CSS 属性；
-	不要为 id 选择器指定类名或是标签，因为 id 可以唯一确定一个元素；
-	避免后代选择符，尽量使用子选择符，因为子元素匹配符的概率要大于后代元素匹配符；
-	避免使用通配符。

> 参考文档
- [浏览器渲染原理简介](http://www.cnblogs.com/aaronjs/archive/2013/06/27/3159789.html) 作者：艾伦 Aaron
- [从输入url到页面加载完成发生了什么？——前端角度](http://www.cnblogs.com/daijinxue/p/6640153.html) 作者：踮起脚尖眺望3
- [前端 浏览器页面渲染的过程](http://www.cnblogs.com/cuncunjun/p/6531495.html) 作者：寸寸君
