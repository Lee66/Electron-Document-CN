# 9. 使用Native Node Modules

> 原文：https://github.com/electron/electron/blob/master/docs/tutorial/using-native-node-modules.md   
译者：[Lin](https://github.com/ShmilyLin)

Electron支持native Node modules，但是因为Electron非常有可能从安装在你系统的Node二进制文件中使用一个不同版本的V8，你必须要在编译native modules时手动指定本地Electron头部。

## 如何安装本地模块

三种方法安装native modules：

### 使用`npm`

通过设置一些环境变量，你可以直接使用`npm`来安装模块。

一个Electron所有依赖的例子：

    # Electron的版本。
    export npm_config_target=1.2.3
    # Electron架构，可以是ia32或者x64.
    export npm_config_arch=x64。
    export npm_config_target_arch=x64
    # 下载Electron的头部。
    export npm_config_disturl=https://atom.io/download/electron
    # 告诉node-pre-gyp我们要编译Electron。
    export npm_config_runtime=electron
    # 告诉node-pre-gyp从源码中编译模块。
    export npm_config_build_from_source=true
    # 安装所有依赖，并且将缓存存储在~/.electron-gyp。
    HOME=~/.electron-gyp npm install

### 安装模块并且重新编译Electron

你可以像其他Node项目一样选择安装模块，然后使用[`electron-rebuild`](https://github.com/paulcbetts/electron-rebuild) 包重新编译Electron的模块。这个模块可以得到Electron的版本并且处理手动步骤中的下载头部和为你的应用编译native modules。

安装`electron-rebuild`并且使用它重编译模块的例子：

    npm install --save-dev electron-rebuild

    # Every time you run "npm install", run this:
    ./node_modules/.bin/electron-rebuild

    # Windows中如果你遇到了问题，请尝试：
    .\node_modules\.bin\electron-rebuild.cmd

### 手动编译Electron

如果你是一个开发人员开发了一个native module并且想使用Electron测试它，你可能想要在Electron中手动重新编译这个模块。你可以直接使用`node-gyp`在Electron中编译：

    cd /path-to-module/
    HOME=~/.electron-gyp node-gyp rebuild --target=1.2.3 --arch=x64 --dist-url=https://atom.io/download/electron

`HOME=~/.electron-gyp`是头部地址。`--target=1.2.3`是Electron的版本。`--dist-url=...指明从哪里下载头部。`--arch=x64`说明模块在64位系统中编译。

## 排除故障

如果你安装了一个native modules并且发现它没有工作，你需要检查以下方面：

 * 模块的架构是否和Electron的架构相同（ia32或者x64）。
 * 你升级Electron之后，你是否重新编译了模块。
 * 当有疑问的时候，首先应该重新运行`electron-rebuild`。

## 依赖于`prebuild`的模块

[`prebuild`](https://github.com/mafintosh/prebuild) 提供一个方法，可以非常容易的使用预编译二进制文件发布基于多个Electron和Node版本的native Node modules。

如果模块提供Electron使用的二进制文件，为了充分的利用预编译的二进制文件情确保删掉`--build-from-source`和`npm_config_build_from_source`两个环境变量。

## 依赖于`node-pre-gyp`的模块

[`node-pre-gyp` tool](https://github.com/mapbox/node-pre-gyp)提供一个方法，可以使用预编译二进制文件来展开／有效利用native Node modules，许多流行模块都使用了这个。

通常这些模块可以非常好的在Electron下工作，但是有时候当Electron使用了一个比Node新的版本的V8，并且有ABI改变的时候，不好的事情就会发生。所以通常情况下建议使用源代码编译本地模块。

如果你遵循`npm`的方式，则请在默认情况下安装模块，如果不是，则你需要在`npm`中输入`--build-from-source`或者设置`npm_config_build_from_source`环境变量。