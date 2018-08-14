熟悉“前端晚自修”的朋友们应该知道，我们每期的头图除了上面的文字随着每期变动以外，几乎是一模一样的（因为太懒了~）。这个头图虽然丑了一点，但是也还说的过去，毕竟是我倾尽毕生艺术细胞拼出来的，虽然中间唯一的图片还是从网上找的😂。

前期推送的时候，都是从 psd 原稿生成图片做头图，实在太麻烦了。后来还有朋友和我一起来写文章，要让他们也在电脑上安装 PS ，而且需要安装好看的字体更加是有些不便。因此便萌生了在线生成头图的想法。

TL;DR

最终效果如下：
![online-poster](https://user-images.githubusercontent.com/3774016/44092708-e1ee6168-a003-11e8-9c3b-80bf869d9f8e.png)

在线地址：[online-poster](https://ifeees.github.io/online-poster/index.html)，微信上点不了外链，可点击 **{阅读原文}**

核心功能有：
1. 生成图片（文字居中）
2. 图片预览和导出
3. 自定义字体

### 生成图片
这一部分比较简单，凭着红宝书上对 Canvas 的讲解，就可以实现。不过要实现文字保持居中，倒是需要“特殊”设置一下：

```javascript
context.textAlign = 'center'
```

### 图片预览和导出
图片预览还是比较简单的，直接将 Canvas 画到页面上就可以，这块不多说。对于图片导出，这一块需要注意一下。我是借鉴掘金上大佬的一篇文章，[canvas入门实战--邀请卡生成与下载](https://juejin.im/post/5a31dbc951882510b27563b9)，核心代码如下：

```javascript
var exportImage = canvas.toDataURL('image/png');
var saveLink = document.createElementNS('http://www.w3.org/1999/xhtml', 'a');
saveLink.href = exportImage;
// 设置下载图片的名称
saveLink.download = text + '.png';
//下载图片
var event = document.createEvent('MouseEvents');
event.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
saveLink.dispatchEvent(event);
```

但是这一块需要特别注意的是可能会存在跨域问题，在将图片画到 Canvas 上的时候往往会存在跨域问题：

```javascript
var image = new Image();
image.setAttribute("crossOrigin", 'Anonymous'); // 解决跨域
image.src = 'http://pazgkbbu5.bkt.clouddn.com/bg.png';
context.drawImage(image, 0, 0, canvas.width, canvas.height);
```

### 自定义字体
其实自定义字体原理和字体图标差不多，也不是很难理解。难的是如何创建字体资源，我这里用的是 [有字库](https://www.youziku.com/)，生成字体资源以后，直接引入到页面中就可以了（其实引入的 css 文件就是以下代码）：

```css
@font-face {
    font-family: 'chenjishiguyun-13c94e564b183ba';
    src: url('//cdn.webfont.youziku.com/webfonts/nomal/99258/45811/5b6d9ae4f629d919b4accb33.gif?r=82303333002');
    src: url('//cdn.webfont.youziku.com/webfonts/nomal/99258/45811/5b6d9ae4f629d919b4accb33.gif?r=82303333002?#iefix') format('embedded-opentype'), url('//cdn.webfont.youziku.com/webfonts/nomal/99258/45811/5b6d9ae4f629d919b4accb33.png?r=82303333002') format('woff2'), url('//cdn.webfont.youziku.com/webfonts/nomal/99258/45811/5b6d9ae4f629d919b4accb33.bmp?r=82303333002') format('woff'), url('//cdn.webfont.youziku.com/webfonts/nomal/99258/45811/5b6d9ae4f629d919b4accb33.jpg?r=82303333002') format('truetype');
    font-weight: normal;
    font-style: normal;
}

.css13c94e564b183ba {
    font-family: 'chenjishiguyun-13c94e564b183ba';
}
```

最终，大功告成了。简单功能，粗糙实现，大家不要吐槽哈。

--EOF--