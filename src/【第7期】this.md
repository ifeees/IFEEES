> 前几天，阮大大在其博客文章上更新了一篇文章（[JavaScript 的 this 原理](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)），通俗易懂地介绍了`this`的原理。

在这篇文章介绍之前，我们在教科书或者网上看到介绍`this`的文章都会是类似下面的这种定义：**this指向函数调用时所处的环境对象**，即取决于运行时产生的执行上下文，所以就会区分函数的几种不同执行情况：
- 作为构造函数执行 this -> 构造实例
- 作为对象属性执行 this -> 对象
- 作为普通函数执行 this -> 函数执行所处环境
- call、apply、bind this -> 传入参数（当不传或传入null、undefined时）

还有一些特殊情况：
- 在浏览器中setTimeout、setInterval和匿名函数执行时，其中 this -> window（Node中global/module）

但是具体为什么是这样的，环境对象到底是什么，就理解的不是很清楚：

在阮老师的这篇文章里，首先引出`内存中数据结构`，以`var obj = {foo: 5}`为例，其在内存中结构如下图所示：

![](https://www.wangbase.com/blogimg/asset/201806/bg2018061801.png)

而对象本质上在内存中的存储，是对应一个属性描述对象的，如下图：

![](https://www.wangbase.com/blogimg/asset/201806/bg2018061802.png)

如果属性的值是原始类型，那么就直接存储在[[value]]属性中，但如果值为引用类型，那这里会继续嵌套下去，而[[value]]值保存引用类型值存储的地址。

那么，加入这个值是一个函数的话，因为函数也是引用类型，那**函数会单独存储在内存中的一块区域，[[value]]属性保存的函数的地址**。

由于函数是一个单独的值，所以它可以在不同的环境（上下文）执行。**JavaScript是允许在函数体内部，引用当前环境的其他变量**，那如果能够能够在函数内部获得当前的运行环境（context）——`this`，其设计目的就是在函数体内部，指代函数当前的运行环境。

那运行环境具体指的是什么意思呢？就是因为函数是内存中单独的一块区域，所以通过谁访问到这个函数，那么谁就是函数执行的允许环境。

参考资料：
- [JavaScript 的 this 原理](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)
- [Javascript 的 this 用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)
- [this 关键字](http://javascript.ruanyifeng.com/oop/this.html)
- [Javascript中this关键字详解](http://www.cnblogs.com/justany/archive/2012/11/01/the_keyword_this_in_javascript.html)

（本篇完）