接触过 Node 的同学，对 `npm包` 是再熟悉不过的了。一般在接手一个老项目时，把代码 down 下来之后第一步要做的就是执行`npm install`安装项目所需的`依赖包`，然后我们在项目中直接引用就可以使用了，但对于其中
- “npm”是什么？
- “包”的本质到底是什么？和“模块”是一个东西吗？
- 为什么执行`npm install`就能一次安装所有的“依赖包”？
- 怎么比较规范编写和发布一个自己的“包”？
- 为什么依赖包安装好了之后，在项目中直接`require`或者`import`就可以使用？
- ……
等问题其实了解的并不是特别清楚。

本文先对以上部分问题，结合近期向 `npmjs.org` 提交一个包的过程总结下来。因为对 Node 了解并不是很深入，所以可能仅限于在具体实践层面，要想深入了解node的包管理，可以还需要掌握模块化等知识。

## 什么是`npm`？

首先，`npm`是我们在命令行中经常会敲得一个命令，其实本质上这里是 Node 的一个内置模块，用于包管理。我们在安装 Node 的时候，其实默认就会给我们安装 npm ，但是你完全可以在安装 Node 并配置好 Node 环境后执行`npm install npm`，因为`npm`本质上也是一个包，用`npm install` **npm自身** 对这个过程理解起来就比较清楚了。

另外，`npm`其实也代表着`npmjs.org`这个网站，可以理解为全球最大的一个 Node 包服务器，我们平时项目中的绝大多数包都直接或间接来自于这。在这个网站注册过账号的开发者都能向其提交自己开发的模块。

## 如何发布？
1. 首先你需要在`https://www.npmjs.com/`这个网站上注册一个账号。
2. 在项目中执行`npm adduser`按照提示输入账号信息。
3. 在`package.json`中配置好相应的项。
4. 执行`npm pushlish`即可。

## 其他 npm 命令
- `npm link`
如果你本地的包还没有发布，或者修改还没提交，但想在本地测试，我们就可以使用`npm link`这个命令，他可以把本地的包通过一个钩子挂载在系统安装的包中，当本地项目引用时，会直接使用本地的包。此时对包的修改也能实时反映在项目中。

---
## 参考资料
1. [npm模块管理器](http://javascript.ruanyifeng.com/nodejs/npm.html)
2. [How to Publish & Update a Package](https://docs.npmjs.com/getting-started/publishing-npm-packages)
3. [如何发布node模块到npm社区](https://github.com/muwenzi/Program-Blog/issues/12)

（本篇完）