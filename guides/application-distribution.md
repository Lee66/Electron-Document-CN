# 5.应用发布

> 原文：https://github.com/electron/electron/blob/master/docs/tutorial/application-distribution.md   
译者：[Lin](https://github.com/ShmilyLin)   


使用Electron来发布你的应用，你需要下载Electron的预构建二进制文件。接下来，包含你的应用的文件夹应该被命名为`app`并且放置在Electron的资源文件夹中，就像下面例子中的那样。注意，在下面的例子中，本地的Electron的预构建二进制文件使用`electron/`标识。   

macOS:     

    electron/Electron.app/Contents/Resources/app/
    ├── package.json
    ├── main.js
    └── index.html

Windows和Linux:   

    electron/resources/app
    ├── package.json
    ├── main.js
    └── index.html

然后执行`Electron.app`（或者在Linux中的`electron`，在Windows中的`electron.exe`），Electron将会运行你的应用程序。`electron`文件夹将会将会因为你的发布而交付给最终的用户。    

### 将你的应用程序打包到文件中   

发布你的应用除了将应用的源文件全部复制之外，你还可以将你的应用程序打包到一个[asar](https://github.com/electron/asar)文档中来避免暴漏你应用程序的源代码。   

使用一个`asar`文档来代替应用程序文件夹，你需要重命名文档为`app.asar`，然后像下面展示的那样将它放置到Electron的预构建二进制文件下，Electron将会尝试读取文档并运行它。   

macOS:   

    electron/Electron.app/Contents/Resources/
    └── app.asar

Windows和Linux:   

    electron/resources/
    └── app.asar

更多细节可以在[打包应用程序](https://electron.atom.io/docs/tutorial/application-packaging)中查看。    

### 重新包装下载的二进制文件   

使用Electron创建你的应用程序后，你在将应用程序发给用户之前会想要重新打包Electron。   

##### Windows   

你可以使用工具，例如[rcedit](https://github.com/atom/rcedit)，来重命名`electron.exe`为任何你喜欢的名字，编辑它的图标和其他信息。   

##### macOS   

你可以重命名`Electron.app`为任何你想要的名字，并且你也同样需要在下列文件中重命名`CFBundleDisplayName`，`CFBundleIdentifier `和`CFBundleName`中的内容：   

* `Electron.app/Contents/Info.plist`   
* `Electron.app/Contents/Frameworks/Electron Helper.app/Contents/Info.plist`   

你也可以重命名助手应用来避免在活动监视器中展示`Electron Helper`，但是请确保你已经重命名助手应用的可执行文件的名字。
一个重命名的应用程序的结构应该像这样：   

    MyApp.app/Contents
    ├── Info.plist
    ├── MacOS/
    │   └── MyApp
    └── Frameworks/
        ├── MyApp Helper EH.app
        |   ├── Info.plist
        |   └── MacOS/
        |       └── MyApp Helper EH
        ├── MyApp Helper NP.app
        |   ├── Info.plist
        |   └── MacOS/
        |       └── MyApp Helper NP
        └── MyApp Helper.app
            ├── Info.plist
            └── MacOS/
                └── MyApp Helper

##### Linux   

你可以将`electron`可执行文件重命名为任何你喜欢的名称。   

### 打包工具   

除了手动打包你的应用程序外，你还可以选择使用第三方打包工具来为你工作。   

* [electron-builder](https://github.com/electron-userland/electron-builder)   
* [electron-packager](https://github.com/electron-userland/electron-packager)   

### 通过源代码重新打包重构的Electron   

也可以通过重新包装Electron来改变产品的名字，为此你需要修改`atom.gyp`文件并且有一个干净的重建环境。   

##### grunt-build-atom-shell   

手动检查Electron代码和重建的工作是复杂的，所以一个Grunt任务被创建将会自动处理这些：[grunt-build-atom-shell](https://github.com/paulcbetts/grunt-build-atom-shell)。   

这个任务将会自动操作编辑`.gyp`文件，编译源代码，然后重建你的应用的原生Node模块来比配信的可执行文件的名字。   

##### 创建自定义Electron复刻   

为了编译你的应用甚至是“生产级”的应用而创建一个Electron复刻几乎可以确定是你不需要的事情。使用类似`electron-packager`或`electron-builder`的工具可以让你跳过这个步骤来重命名Electron。   

当你在Electron使用自定义的C++代码时，或者不使用官方的版本时你需要复刻Electron。作为Electron的维护人员，我们非常想让你的方案能够工作起来，所以希望你尽可能的将你的版本换成官方的版本，这对你来说将会是很容易的，我们很感激你的帮助。   

##### 使用surf-build创建一个自定义版本   

1.通过npm安装[Surf](https://github.com/surf-build/surf)： `npm install -g surf-build@latest`   
2.创建一个新的S3 bucket然后创建一个空的文件夹结构：   

    - atom-shell/
        - symbols/
        - dist/

3.设置下列环境变量：   

 * `ELECTRON_GITHUB_TOKEN` - 一个能够创建发布到GitHub上的token   
 * `ELECTRON_S3_ACCESS_KEY`，`ELECTRON_S3_BUCKET`，`ELECTRON_S3_SECRET_KEY` - 这个地方需要你上传node.js的头部作为标识   
 * `ELECTRON_RELEASE` - 设置为true上传部分将会开始运行，如果不设置`surf-build`将会仅仅做CI-type检查，在每次拉取后都会运行   
 * `CI` - 设置为`true`否则它将会失败   
 * `GITHUB_TOKEN` - 设置这个的效果等同于设置`ELECTRON_GITHUB_TOKEN`   
 * `SURF_TEMP` - Windows下设置为`C:\Temp`来放置路径过长问题   
 * `TARGET_ARCH` - 设置为`ia32`或者`x64`   

4.In `script/upload.py`，你必须要给你的复刻（`MYORG/electron`）设置`ELECTRON_REPO`，特别是如果你是Electron的贡献者。   
5.`surf-build -r https://github.com/MYORG/electron -s YOUR_COMMIT -n 'surf-PLATFORM-ARCH'`
6.等待非常长的一段时间来完成编译。   