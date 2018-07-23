> 这两天将YTUI内的基础Icon（图标）组件完成了，之前一直对字体图标一直比较懵，也很懒去深入了解，这几天逼着自己学习学习，记录如下。

其实字体图标有很多实现方式，上古时代的`雪碧图`其实就是当时一个非常优的解决方案，但是随着web的发展，雪碧图看起来就比较low了，目前有两种比较流行的实现方式：
- 字体图标（iconfont）
- svg sprite

这次我是基于第一种方式实现的，下面基于svg sprite的方式以后再研究。

## 字体图标原理
字体我们其实都比较熟悉，对应于CSS中就是`font-family`属性，通过其可以指定网页中用到的字体。

但是我们可能会比较容易忽略或者不知道文字和字体在web中显示的原理。

关于`字体`的原理，可以查看下面几篇文章：
- [Design With FontForge](http://designwithfontforge.com/zh-CN/Introduction.html)
- [解密 Iconfont - 前端 - 掘金](https://juejin.im/entry/58d374671b69e6006b9dde70)
- [font-awesome 原理分析](https://blog.csdn.net/dengqui/article/details/51647947)
- [「每日一题」聊一聊字体图标的实现原理](https://zhuanlan.zhihu.com/p/22724856)

简单来讲，字体其实本质上就是一个key-value库，key是Unicode编码，value是需要渲染在页面上的文字的图像。

而Unicode编码与文字又是怎么对应的呢，我们知道其实每个文字都有其对应的Unicode码的，我们如果直接给浏览器Unicode码时，显示在页面上也是对应的文字。

因此这样文字和字体内相应的图像就一一对应了起来，当浏览器渲染时，就会加载字体文件中的图像到页面中进行渲染。

所以，如果我们定义并引用一类特殊字体，其中定义的编码，对应的是不同的**icon图像**，这样我们在页面上**引用对应的编码**，渲染到页面上就是Icon了。

## 如何生成字体文件
网上有许多工具可以使用，可以将设计师生成的icon svg格式的图像转换成字体文件。
- http://www.iconfont.cn/
- https://icomoon.io/

## 如何引入自定义字体文件
通过上面的工具不仅可以下载字体文件，其实引用代码也可以帮助生成。

CSS中`@font-face`属性可以自定特殊字体，语法如下：
```css
@font-face {
    font-family: "ytui-icon";
    src: url('~/iconfont.eot?t=1531298415150#iefix') format('embedded-opentype'), /* IE6-IE8 */
         url('~/iconfont.ttf?t=1531298415150') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
         url('~/iconfont.svg?t=1531298415150#ytui-icon') format('svg'); /* iOS 4.1- */
}
```

这样，我们就定义了一个特殊的字体，名为`ytui-icon`。这样如果在页面中输入该字体文件中包含的编码的字符时，就会渲染成对应的图像（Icon）。

但是，这种方式实在是不太优雅，一般借助伪元素实现，如下代码所示：
```html
<i class="ytui-icon-presc-lookup"></i>
```

```css
.ytui-icon-presc-lookup:before { 
    content: "\e60e"; 
}
```

这样，我们使用时只需要引用对应的类名即可。

## 其他
有一个问题需要注意的是，我在直接使用从iconfont.cn上下载的字体文件和样式时，webpack编译会报错，具体信息及解决方案见[引入自定义字体时webpack打包报错](https://github.com/if2er/blog/issues/34)。

（本篇完）