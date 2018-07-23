> 昨天早读课分享了一篇 [【第1326期】WebView缓存原理分析和应用](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ%3D%3D&mid=2651229156&idx=1&sn=fdd48acd893ddd11ccd2460c46a3c2d1#wechat_redirect)，原作者去年5月份就已经发出来了，不过针对WebView的缓存分析的还蛮深入的，正好对缓存这一块了解也不是很深，正好学习下。

**_1、WebView_**
提到`WebView`，其实就离不开`Hybrid`模式，是一种混合APP开发模式。将H5嵌入到APP提供的容器——WebView内，通过bridge.js和Native实现通信。

**_2、WebView缓存类型_**
原文中提到“bridge.js”其实一般都不会发生变化，所以将其缓存在APP内，下次再访问H5的时候就不会再发起网络请求，减少流量和资源的占用，从而提供页面的访问速度。

WebView缓存分为两类：
- 浏览器自带的网页数据缓存：这是所有浏览器支持的、由HTTP协议定义的缓存
- H5缓存：这是由Web页面的开发者设置的，包括App Cache、DOM Storage、Local Storage、Web SQL Datebase存储机制等。

**_3、浏览器自带的网页数据缓存_**
浏览器缓存机制是通过 HTTP 协议 `header` 里的`Cache-Control`（或`Expires`）和`Last-Modified`（或`Etag`）等字段来控制文件缓存的机制。
关于这几个字段的作用和浏览器的缓存更新机制，参照：
- [H5 缓存机制浅析 - 移动端 Web 加载性能优化](https://segmentfault.com/a/1190000004132566)
- [Android：手把手教你构建 全面的WebView 缓存机制 & 资源加载方案](https://www.jianshu.com/p/5e7075f4875f)

其中，

`Cache-Control`和`Expires`是**接收响应时，浏览器决定文件是否需要被缓存；或者需要加载文件时，浏览器决定是否需要发出请求的字段**。
- `Cache-Control`：max-age=315360000，这表示缓存时长为315360000秒。如果315360000秒内需要再次请求这个文件，那么浏览器不会发出请求，直接使用本地的缓存的文件。这是HTTP/1.1标准中的字段。
- `Expires`：Thu, 31 Dec 2037 23:55:55 GMT，这表示这个文件的过期时间是2037年12月31日晚上23点55分55秒，在这个时间之前浏览器都不会再次发出请求去获取这个文件。这是HTTP/1.0中的字段，如果客户端和服务器时间不同步会导致缓存出现问题，因此才有了上面的Cache-Control，当它们同时出现在HTTP Response的Header中时，Cache-Control优先级更高。

`Last-Modified`和`Etag`是**发起请求时，服务器决定文件是否需要更新的字段**。
- `Last-Modified`：Wed, 28 Sep 2016 09:24:35 GMT，这表示这个文件最后的修改时间是2016年9月28日9点24分35秒。这个字段对于浏览器来说，会在下次请求的时候，作为Request Header的`If-Modified-Since`字段带上。例如浏览器缓存的文件已经超过了Cache-Control（或者Expires），那么需要加载这个文件时，就会发出请求，请求的Header有一个字段为If-Modified-Since：Wed, 28 Sep 2016 09:24:35 GMT，服务器接收到请求后，会把文件的Last-Modified时间和这个时间对比，如果时间没变，那么浏览器将返回304 Not Modified给浏览器，且content-length肯定是0个字节。如果时间有变化，那么服务器会返回200 OK，并返回相应的内容给浏览器。
- `Etag`:“57eb8c5c-129”，这是文件的特征串。功能同上面的Last-Modified是一样的。只是在浏览器下次请求时，ETag是作为Request Header中的`If-None-Match`:"57eb8c5c-129"字段传到服务器。服务器和最新的文件特征串对比，如果相同那么返回304 Not Modified，不同则返回200 OK。当ETag和Last-Modified同时出现时，任何一个字段只要生效了，就认为文件是没有更新的。

**_4、WebView如何设置才能支持上面的协议_**
只要是个主流的、合格的浏览器，都应该能够支持HTTP协议层面的这几个字段。一般情况下，开发者是不能也不会修改配置的。

在Android中的WebView也支持这几个字段，但可以通过代码去**设置WebView的Cache Mode**，而使得协议生效或者无效。WebView有下面的几个Cache Mode：
- LOAD_CACHE_ONLY: 不使用网络，只读取本地缓存数据。
- LOAD_DEFAULT: 根据cache-control决定是否从网络上取数据。
- LOAD_CACHE_NORMAL: API level 17中已经废弃，从API level 11开始作用同LOAD_DEFAULT模式
- LOAD_NO_CACHE: 不使用缓存，只从网络获取数据。
- LOAD_CACHE_ELSE_NETWORK，只要本地有，无论是否过期，或者no-cache，都使用缓存中的数据。本地没有缓存时才从网络上获取。

设置代码：
```java
WebSettings settings = webView.getSettings();
settings.setCacheMode(WebSettings.LOAD_DEFAULT);
```

具体内部存储路径等问题因为和前端关联不大，需要时可再行研究。

**_5、H5的App Cache在WebView内如何支持_**
上面提到缓存类型时，H5的缓存有一个App Cache，是由开发Web页面的开发者控制的，而不是Native，但**Native**里的WebView需要做一下设置才能支持H5的这个特性。

5.1 设置方法
```java
WebSettings webSettings = webView.getSettings();
webSettings.setAppCacheEnabled(true);
String cachePath = getApplicationContext().getCacheDir().getPath(); // 把内部私有缓存目录'/data/data/包名/cache/'作为WebView的AppCache的存储路径
webSettings.setAppCachePath(cachePath);
webSettings.setAppCacheMaxSize(5 * 1024 * 1024);
```

5.2 前端如何支持
写Web页面代码时，指定manifest属性即可让页面使用App Cache。
```html
<html manifest="xxx.appcache">
</html>
```
> xxx.appcache文件用的是相对路径，这时appcache文件的路径是和页面一样的。也可以使用的绝对路径，但是域名要保持和页面一致。

完整的xxx.appcache文件一般包括了3个section，基本格式如下：
```
CACHE MANIFEST
# 2017-05-13 v1.0.0
/bridge.js
 
NETWORK:
*
 
FALLBACK:
/404.html
```

- CACHE MANIFEST下面文件就是要被浏览器缓存的文件
- NETWORK下面的文件就是要被加载的文件
- FALLBACK下面的文件是目标页面加载失败时的显示的页面

5.3 AppCache工作原理
> 当一个设置了manifest文件的html页面被加载时，CACHE MANIFEST指定的文件就会被缓存到浏览器的App Cache目录下面。当下次加载这个页面时，会首先应用通过manifest已经缓存过的文件，然后发起一个加载xxx.appcache文件的请求到服务器，如果xxx.appcache文件没有被修改过，那么服务器会返回304 Not Modified给到浏览器，如果xxx.appcache文件被修改过，那么服务器会返回200 OK，并返回新的xxx.appcache文件的内容给浏览器，浏览器收到之后，再把新的xxx.appcache文件中指定的内容加载过来进行缓存。

**_6、对比_**
- 相同点：
WebView自带的缓存和AppCache都是可以用来做**文件级别**的缓存的，基本上比较好地满足对于非覆盖式的js、css等文件更新。

- 不同点
	- WebView自带的缓存是是**协议层**实现的（浏览器内核标准实现，开发者无法改变）；而AppCache是**应用层**实现的。
	- WebView的缓存目录在不同系统上可能是不同的；而对于AppCache而言，AppCache的存储路径虽然有方法设置，但是最终都存储到了一个固定的内部私有目录下。
	- WebView自带的缓存可以在缓存生效的时候**不用再发HTTP请求**；而AppCache**一定会发出一个manifest文件的请求**。
	- WebView自带的缓存可以通过设置CacheMode来改变WebView的缓存机制；而AppCache的缓存策略是由manifest文件控制的，也就是说是由web页面开发者控制的。

（本篇完）