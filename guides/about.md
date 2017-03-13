# 关于Electron

> 原文：http://electron.atom.io/docs/tutorial/about/   
译者：[Lin](https://github.com/ShmilyLin)   
校订：[Zyan](https://github.com/irockyan)   

##关于Electron
[Electron](http://electron.atom.io/)是一个由GitHub开源的使用HTML，CSS和JavaScript建立的跨平台的桌面应用开发库。Electron通过结合[Chromium](https://www.chromium.org/Home)和[Node.js](https://nodejs.org)来实现在Mac，Windows和Linux上运行。   
Electron最开始是作为GitHub的文本编辑器[Atom](https://atom.io/)的一个框架在2013年被创建的，而后在2014年春天开源。   
它已经在开源开发者、初创公司和一些大公司中成为了一个流行的工具。[看看都谁在使用Electron](http://electron.atom.io/apps/)。   
继续阅读来了解Electron的贡献者们和已经发布的版本，或者阅读[快速开始指南](../tutorials/quick-start.html)来使用Electron创建项目。


###核心团队和贡献者
Electron是由GitHub的一个团队和社区中一群[积极的贡献者]维护的。(https://github.com/electron/electron/graphs/contributors)。Electron的贡献者中，一部分是个人开发者，一部分是在大公司工作的开发者。我们很高兴邀请提交多的贡献者加入到维护团队中。阅读更多关于[Electron的贡献者](https://github.com/electron/electron/blob/master/CONTRIBUTING.md)。


###版本发行
[Electron的版本发布](https://github.com/electron/electron/releases)是比较频繁的。当我们修复了重大的缺陷，增加了或者新的接口，Chromium或者Node.js更新了新的版本我们都会发布新的Electron版本。

##### 依赖更新
当一个新的稳定的Chromium版本发布之后我们通常都会在一到两周之内发布对应Chromium版本的新版Electron，速度取决于更新的复杂程度。   
当一个新的Node.js版本发布之后，Electron通常会为了带来一个更稳定的版本而等待大约一个月的时间之后再发布新的版本。
在Electron中，Node.js和Chromium使用同一个版本的V8实例，通常是chromium使用的版本。大多数情况下都能正常工作，但是有时需要为Node.js打补丁。

##### 版本控制
由于对Node.js和Chromium有着极强的依赖，Electron的版本控制非常复杂，因此[不遵循`semver`](http://semver.org/)。你因此应该经常参考Electron的特定版本。[阅读关于Electron的版本管理](http://electron.atom.io/docs/tutorial/electron-versioning/)或者查看[当前使用的版本的Electron](https://electron.atom.io/#electron-versions)。

#####LTS
Electron并不会长期支持旧的版本。如果你想一直使用你当前正在使用的Electron版本，你可以一直使用下去。如果你想要使用一些新的功能你需要升级到对应的新的版本。   
Electron有一个重大的更新已经更新到了V1.0.0版本。 如果你没有正在使用这个版本，你需要[阅读更多关于v1.0.0版本的改变说明](http://electron.atom.io/blog/2016/05/11/electron-1-0)。

###核心理念
为了保持Electron小型化（文件的大小）和可持续性（依赖和接口的传递）这个项目限制了它的核心的范围。   
例如，Electron只使用Chromium的底层渲染库而不是整个Chromium。这使得Electron更容易升级Chromium，但是这也意味着在Google Chrome中的某些特性功能无法在Electron中使用。   
新添加到Electron中的特性应该主要是是一些本地API。如果一个功能是Node.js自己的模块，那么它将会被添加到Electron中。查看[社区中提供的Electron工具](http://electron.atom.io/community)。

###历史版本
以下是Electron的里程碑。

| 日期 | 说明 |
|:---:|:---:|
| 2013年4月 |  	[`Atom Shell`启动](https://github.com/electron/electron/commit/6ef8875b1e93787fa9759f602e7880f28e8e6b45) |
| 2014年5月 | [`Atom Shell`开放源代码](http://blog.atom.io/2014/05/06/atom-is-now-open-source.html) |
| 2015年4月 | [`Atom Shell`被更名为`Electron`](https://github.com/electron/electron/pull/1389) |
| 2016年5月 | [`Electron`发布v1.0.0版本](http://electron.atom.io/blog/2016/05/11/electron-1-0) |
| 2016年5月 | [`Electron`应用兼容`Mac App Store`](http://electron.atom.io/docs/tutorial/mac-app-store-submission-guide) |
| 2016年8月 | [`Windows Store`兼容`Electron`应用](http://electron.atom.io/docs/tutorial/windows-store-guide) |
