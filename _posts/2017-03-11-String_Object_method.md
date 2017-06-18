---
layout: post
title: "String 对象"
date: 2017-03-11
description: "String 对象的属性和方法"
tag: JavaScript
---

### String对象
String对象用于处理文本(字符串)。
### 属性
-	`constructor`对创建改对象的函数的引用
-	`length`字符串的长度
-	`prototype`允许您向对象添加属性和方法

### 方法

| 方法 | 参数 | 作用 | 返回值 |
|------|------|------|--------|
| `a()` | anchorname(为锚点定义名称) | 创建HTML锚 | HTML锚 |
| `big()` | - | 把字符串显示为大号字体 | - |
| `blink()` | - | 把字符串显式为闪动字符串(IE中无效) | - |
| `bold()` | - | 把字符串显示为粗体 | - |
| `fixed()` | - | 把字符串显示为打印机字体 | - |
| `fontcolor()` | color(三种表示方法) | 按照指定的颜色来显示字符串 | - |
| `fontsize()` | size(1 - 7) | 按照指定的尺寸来显式字符串 | - |
| `italics()` | - | 把字符串显示为斜体 | - |
| `link()` | url | 把字符串显示为超链接 | - |
| `small()` | - | 把字符串显示为小号字 | - |
| `strike()` | - | 显示加删除线的字符串 | - |
| `sub()` | - | 把字符串显示为下标 | - |
| `sup()` | - | 把字符串显示为上标 | - |
| `charAt()` | index | 返回指定位置的字符 | 指定位置的字符 |
| `charCodeAt()` | index | 返回指定位置的字符的Unicode编码 | 指定位置的字符的Unicode编码 |
| `concat()` | stringX[,stringX,stringX...] | 连接两个或多个字符串 | 连接后的字符串 |
| `fromCharCode()` | numX,numX... | 接受一个指定的Unicode值，返回一个字符串 | 根据传入的值创建的字符串 |
| `indexOf()` | searchvalue[,fromindex] | 返回某个指定的字符串值在字符串中首次出现的位置(如果有fromindex就从fromindex位置开始检索) | 指定字符串值在字符串中首次出现的位置 &brvbar;&brvbar; -1 |
| `lastIndexOf()` | searchvalue[,fromindex] | 返回某个指定的字符串值在字符串中最后出现的位置，从后往前搜索(如果有fromindex就从fromindex位置开始检索) | 指定字符串值在字符串中最后出现的位置 &brvbar;&brvbar; -1 |
| `localCompare()` | target | 用本地特定的顺序来比较两个字符串 | 说明比较结果的数字(stringObject < target，返回小于0的数字； > 返回大于0的数字；两个字符串相等或者本地排序规则没有区别，返回 0 ) |
| `match()` | searchvalue &brvbar;&brvbar; regexp | 在字符串内检索指定的值，或找到一个或多个正则表达式的匹配 | 存放匹配结果的数组(该数组依赖于regexp是否具有全局标志g) |
| `replace()` | regexp/substr, replacement(可以是字符串，也可以是返回字符串的函数，该函数第一个参数是匹配到的字符串) | 在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串 | 用replacement替换了regexp的第一次匹配或所有匹配之后得到的心字符串(该字符串依赖于regexp是否具有全局标志g) |
| `search()` | regexp | 检索字符串中指定的子字符串，或检索与正则表达式相匹配的子字符串 | 第一个与regexp相匹配的子串的起始位置(追加标志i可执行忽略大小写的检索) |
| `slice()` | start[,end] (可以为负数，为负数时从尾部开始算起的位置) | 提取字符串的某个部分，并以新的字符串返回被提取的部分 | 从start到end的新字符串 |
| `split()` | separator(可以是字符串，也可以是正则表达式)[,howmany(返回的数组的最大长度)] | 把一个字符串分隔成字符串数组 | 通过在sqparator指定的边界处将字符串分隔成子串创建的数组 |
| `substr()` | start(可为负数)[,length] | 在字符串中抽取从start下标开始的指定数目的字符 | 从字符串start出开始的length个字符组成的字符串 |
| `substring()` | start[,stop] (两个都是非负整数) | 用于提取字符串中介于两个指定下标之间的字符 | 从字符串start出到stop出的字符组成的字符串 |
| `toLocaleLowerCase()` | - | 用于把字符串转换为小写 | 字符串所有大写字符全部转化为小写字符的新的字符串 |
| `toLocaleUpperCase()` | - | 用于把字符串转换为大写 | 字符串所有小写字符全部转化为大写字符的新的字符串 |
| `toLowerCase()` | - | 用于把字符串转换为小写 | 字符串所有大写字符全部转化为小写字符的新的字符串 |
| `toUpperCase()` | - | 用于把字符串转换为大写 | 字符串所有小写字符全部转化为大写字符的新的字符串 |
| `toSource()` | - | 代表对象的源代码 | - |
| `toString()` | - | 返回字符串 | 字符串的原始字符串值 |
| `valueOf()` | - | 返回String对象的原始值 | String 对象的原始值 |

### 注意

-	**JavaScript 的字符串是不可变的（immutable），String 类定义的方法都不能改变字符串的内容。**
-	`toLocaleLowerCase()`方法与`toLowerCase()`方法的区别在于它按照本地方式把字符串转换为小写，只有几种语言具有本地特有的大小写映射。该方法的返回值通常与`toLowerCase()`一样。
-	`toLocaleUpperCase()`方法与`toUpperCase()`方法的区别在于它按照本地方式把字符串转换为大写，只有几种语言具有本地特有的大小写映射。该方法的返回值通常与`toUpperCase()`一样。
-	当调用`toString()`方法的对象不是 String 时抛出 TypeError 异常。该方法不常用。
-	当调用`valueOf()`方法的对象不是 String 时抛出 TypeError 异常。该方法通常有 JavaScript 在后台自动进行调用，而不是显式地处于代码中。

> 参考文档
- [Weschool JavaScript String 对象](http://www.w3school.com.cn/jsref/jsref_obj_string.asp)