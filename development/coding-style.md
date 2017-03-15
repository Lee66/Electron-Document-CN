# 1.Coding Style

> 原文：https://github.com/electron/electron/blob/master/docs/development/coding-style.md    
译者：[Lin](https://github.com/ShmilyLin)   

这里有一些在Electron中的编码风格指导。

你可以运行`npm run lint`来展示任何风格 to show any style issues detected by `cpplint`和`eslint`.

## C++和Python

对于C++和Python，我们遵循Chromium的[代码风格](http://www.chromium.org/developers/coding-style)。你可以使用[clang-format](https://github.com/electron/electron/blob/master/docs/development/clang-format.md)自动格式化C++代码。这里有一个脚本`script/cpplint.py`来检查是否所有的文件都遵循。

Python的版本我们现在使用的是Python 2.7。

C++代码使用了大量的Chromium的抽象类，所以建议了解一下它们。一个好的开始是Chromium的[Important Abstractions and Data Structures](https://www.chromium.org/developers/coding-style/important-abstractions-and-data-structures)文档。这个文档中提到一些特殊的类型，作用域类型（当在作用域范围之外时自动释放内存），以及日志记录机制等。

## JavaScript

 * 写标准的JavaScript风格。
 * 文件名字应该是使用`-`连在一起的而不是使用`_`，例如，是`file-name.js`而不是`file_name.js`，因为在[github/atom](https://github.com/github/atom)模块中名字通常是`module-name`构成方式。这个规则只适用于`.js`文件。
 * 在适当的地方使用新的ES6/ES2015语法
     * [`const`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)使用在饮用和其他常量上
     * [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)用来定义变量
     * [`Arrow functions`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)代替`function () { }`
     * [`Template literals`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)用来代替使用`+`的字符串连接。

## 命名方式

Electron接口使用和Node.js相同的命名方式：

 * 当模块自己就是一个类时，例如`BrowserWindow`，则使用`CamelCase`（驼峰命名法）。
 * 当模块是一个接口的集合时，例如`globalShortcut`，则使用`mixedCase`。
 * 当接口是一个对象的属性，并且足够复杂到能够单独分出一章，例如`win.webContents`，则使用`mixedCase`。
 * 对于其他的模块接口，使用正常的标题，例如`<webview> Tag`或`Process Object`。

当创建一个新的接口，最好使用getters和setters来代替jQuery的one-function风格。例如，`.getText()`和`.setText(text)`就比`.text([text])`要好。这里有一个关于这个的[讨论](https://github.com/electron/electron/issues/46)。


