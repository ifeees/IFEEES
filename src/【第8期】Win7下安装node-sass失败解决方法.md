> 本机环境：Window 7

在使用`antd-mobile`时经常会安装不上，其实因为antd-mobile依赖了`node-sass`和`node-gyp`包，具体原因见[安装 node-sass 的正确姿势](https://github.com/lmk123/blog/issues/28)，正确安装后一般都没什么问题。

但是在 Windows 系统里还是能够遇到很多坑，特此将相应解决方法记录下来：

1、缺少Python
![image](https://user-images.githubusercontent.com/3774016/41887612-0854ee18-7934-11e8-91a6-a3501ac23fec.png)

解决方案：安装Python后执行如下命令（替换为自己电脑的路径）：
```
npm config set python C:\Programs\Python27\python.exe
```

2、
![image](https://user-images.githubusercontent.com/3774016/41887714-7af9121e-7934-11e8-9999-747966d65886.png)

~~解决方案：下载.Net Framework后安装后重启电脑生效。~~（仅安装这个没用）
解决方案：参照[npm install socket.io 提示缺少“VCBuild.exe”，一定要装VS C++吗？](https://cnodejs.org/topic/510a98acdf9e9fcc58ee157b)

看答案里有一个比较简单的方法是：
[https://github.com/nodejs/node-gyp/issues/307#issuecomment-240556824](https://github.com/nodejs/node-gyp/issues/307#issuecomment-240556824)
```
npm install --global --production windows-build-tools
```

3、
![image](https://user-images.githubusercontent.com/3774016/41888107-572c74b4-7936-11e8-967e-e5d9be8c724d.png)

看到报错觉得可能是`node_modules`的问题，然后就；
```
rm -rf node_modules
```

成功啦！不容易~

![image](https://user-images.githubusercontent.com/3774016/41888280-3e9e45ac-7937-11e8-91d2-ecdea70abb54.png)


**MAC大法好！**

（本篇完）