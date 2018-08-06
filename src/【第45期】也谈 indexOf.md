# 也谈 indexOf
最近早读课推送文章的风格有些不一样了，有些看起来实在是基础了些，所以下面评论里也是颇有微词。不过对我等菜鸟来说，早读课的文章，总是有可以学习和深究的价值的。前天推送文章[【第1349期】谈谈JS数组中的indexOf方法](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ%3D%3D&mid=2651229415&idx=1&sn=5058ce9c3a2a3fa6348e72349c0e1f7b#wechat_redirect)里提到了我们经常用到的`indexOf`方法，所以就打算对着 MDN 深究一番。

TL;DR

`indexOf`在原生 js 中，分别有两处定义：
- [String.prototype.indexOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/indexOf)
- [Array.prototype.indexOf()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)

其实，有的时候就是对着文档（ MDN ）认真梳理一遍，就能学到不少东西。

## Array.prototype.indexOf()
为什么先说数组的 indexOf() 方法呢？因为对于我来说，提到 indexOf，第一反应就是数组（和早读课文章的作者的反应恰好相反）。

官方定义是这样的：
> The indexOf() method returns the first index at which a given element can be found in the array, or -1 if it is not present.

语法形式为：
```javascript
arr.indexOf(searchElement[, fromIndex])
```

接受两个参数：
- searchElement：要查找的项
- fromIndex：可选（默认0），开始查找的位置。
    - 如果值大于或等于数组长度，返回-1，意味着不会执行查询
    - 如果值小于0，就会从数组末尾往前计算开始查找的位置，如果计算结果小于0，意味着会查询整个数组。

返回值：元素在数组中匹配到的第一个索引位置，没匹配返回-1

匹配方式：**严格等于（===）**

扩展了解：
- Array.prototype.lastIndexOf()
- Polyfill

## String.prototype.indexOf()
官方定义如下：
> The indexOf() method returns the index within the calling String object of the first occurrence of the specified value, starting the search at fromIndex. Returns -1 if the value is not found.

语法形式如下：
```javascript
str.indexOf(searchValue[, fromIndex])
```

类似的是，其也接受两个参数：
- searchValue：要查找的字符串（**此处接受字符串类型值，所以其他类型值会进行类型转换**）
- fromIndex：可选（默认0）。
    - 如果小于0，会从首部（也即0）开始查找
    - 如果大于字符串长度，会从`str.length`开始查找

返回值：元素在数组中匹配到的第一个索引位置，没匹配返回-1

匹配方式：**==**

扩展了解：
- String.prototype.charAt()
- String.prototype.lastIndexOf()

--EOF--

有事没事多查文档。