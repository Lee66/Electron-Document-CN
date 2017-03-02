# 关于Electron

> 原文：http://electron.atom.io/docs/tutorial/about/
译者：Lin

##关于`Electron`
[Electron](http://electron.atom.io/)是一个在`GitHub`上开源的使用`HTML`，`CSS`和`JavaScript`来建立跨平台的桌面应用的开发库。`Electron`通过结合[Chromium](https://www.chromium.org/Home)和[Node.js](https://nodejs.org/)来实现这一功能，并且使得应用程序可以在`Mac`，`Windows`和`Linux`上运行。
`Electron`最开始是2013年`GitHub`上的文本编辑器[Atom](https://atom.io/)中的一个框架，并且在2014年春天开源。
它在开源开发者、初创公司和已经存在的公司中逐渐成为了一个流行的工具。[看看都谁在使用`Electron`](http://electron.atom.io/apps/)。
继续阅读来了解`Electron`的贡献者们和发布的新版本，或者阅读[快速开始指南](section15.html)来使用`Electron`创建项目。


###核心团队和贡献者
`Electron`是`GitHub`上一个团队来维护的，他们同样也是社区中一群积极的贡献者。`Electron`的贡献者中，一部分是个人开发者，一部分是在大公司工作的开发者。我们很高兴邀请提交多的贡献者加入到维护团队中。阅读更多关于[`Electron`的贡献者](https://github.com/electron/electron/blob/master/CONTRIBUTING.md)。


###版本发行
`Electron`的版本发布是比较频繁的。当我们修复了重大的缺陷，增加了或者新的接口，`Chromium`或者`Node.js`更新了新的版本我们都会发布新的`Electron`版本。

#####依赖更新
当一个新的稳定的`Chromium`版本发布之后我们通常都会在一到两周之内发布对应`Chromium`版本的新版`Electron`，速度取决于更新的复杂程度。
当一个新的`Node.js`版本发布之后，`Electron`通常会为了带来一个更稳定的版本而等待大约一个月的时间之后再发布新的版本。
In `Electron`, `Node.js` and `Chromium` share a single V8 instance—usually the version that `Chromium` is using. Most of the time this just works but sometimes it means patching `Node.js`.

#####版本控制
由于对`Node.js`和`Chromium`有着极强的依赖，`Electron`的版本控制非常复杂因此[不遵循`semver`](semver.org)。你因此应该参考`Electron`的特定版本。[阅读关于`Electron`的版本管理](http://electron.atom.io/docs/tutorial/electron-versioning/)或者查看[你当前使用的版本的`Electron`](https://electron.atom.io/#electron-versions)。

#####LTS
Long term support of older versions of Electron does not currently exist. 如果你想一直使用你当前正在使用的`Electron`版本你可以一直使用下去。如果你想要使用一些新的功能你需要升级到对应的新的版本。
`Electron`有一个重大的更新已经更新到了V1.0.0版本。 如果你没有正在使用这个版本，你需要[阅读更多关于v1.0.0版本的改变说明](http://electron.atom.io/blog/2016/05/11/electron-1-0)。

###核心理念
为了保持`Electron`小型化（指的是文件的大小）和可持续性（依赖和接口的传递）这个项目限制了它的核心的范围。
例如，`Electron`只使用`Chromium`的底层渲染库而不是整个`Chromium`。这使得`Electron`更容易升级`Chromium`，但是这也意味着在`Google Chrome`中的某些特性功能无法在`Electron`中使用。
新添加到`Electron`中的功能应该首先是本地接口。如果一个功能是`Node.js`自己的模块，那么它将会被添加到`Electron`中。查看[社区中提供的`Electron`工具](http://electron.atom.io/community)。

###历史版本
一下是`Electron`的里程碑。

| 日期 | 说明 |
|:---:|:---:|
| 2013年4月 |  	[`Atom Shell`启动](https://github.com/electron/electron/commit/6ef8875b1e93787fa9759f602e7880f28e8e6b45) |
| 2014年5月 | [`Atom Shell`开放源代码](http://blog.atom.io/2014/05/06/atom-is-now-open-source.html) |
| 2015年4月 | [`Atom Shell`被更名为`Electron`](https://github.com/electron/electron/pull/1389) |
| 2016年5月 | [`Electron`发布v1.0.0版本](http://electron.atom.io/blog/2016/05/11/electron-1-0) |
| 2016年5月 | [`Electron`应用兼容`Mac App Store`](http://electron.atom.io/docs/tutorial/mac-app-store-submission-guide) |
| 2016年8月 | [`Windows Store`兼容`Electron`应用](http://electron.atom.io/docs/tutorial/windows-store-guide) |