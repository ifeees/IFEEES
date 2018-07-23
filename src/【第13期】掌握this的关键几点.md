> 今天早读课又推送了一篇深入`this`关键字的文章，[【第1318期】深入浅出 JavaScript 关键词 -- this](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ%3D%3D&mid=2651229103&idx=1&sn=3627db879f759f4355730879c148ef38#wechat_redirect)，好好拜读拜读。

由于ES6箭头函数的引入，`this`值在实际工作场景中出错的机会越来越少了，个人觉得这篇文章作者对于`this`介绍的稍微有些零散，因为涉及到的点不仅仅只是this。

结合平时总结学习`this`比较关键的几点如下：
- `this`就是一个指针，指向我们调用函数的对象
- `this`的指向与所在方法的**调用位置**有关，而与方法的声明位置无关
- 在浏览器中，调用方法没有明确对象时，`this`指向`window`
- 在浏览器中，setTimeout、setInterval和匿名函数执行时的当前对象是全局对象window（**所有异步执行的都是如此**，In Node.js this is different. The top-level scope is not the global scope; var something inside a Node.js module will be local to that **module**. 但在Node CLI下，与浏览器的行为保持一致）
- apply和call、bind能够强制改变函数执行时的当前对象，让this指向其他对象
- eval等同于在声明位置填入代码
- 最后：在ES6中的箭头函数中，`this`的值是在**声明**时候绑定的

记住以上几点，然后再深入探析其中的原理，会对`this`理解起来更加简单。

另外，对技术点的学习不要有畏难心理，尤其是对于前端新人来说，经常会被行业术语吓到。还是需要静下心来深入了解其本质，其实也不是很难。

（本篇完）