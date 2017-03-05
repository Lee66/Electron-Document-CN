# 12.开发工具扩展

> 原文：https://github.com/electron/electron/blob/master/docs/tutorial/devtools-extension.md    
译者：[Lin](https://github.com/ShmilyLin)   


Electron支持[Chrome DevTools Extension](https://developer.chrome.com/extensions/devtools)，可以使用流行的调试网页框架的工具来扩展。   

### 如何加载一个DevTools Extension    

本篇文档概述来手动加载一个扩展的过程。你可能还需要试一下[electron-devtools-installer](https://github.com/GPMDP/electron-devtools-installer)，一个第三方的工具，能够直接从Chrome WebStore下载扩展。   

Electron中加载一个扩展，你需要先在Chrome浏览器中下载它，找到它的下载路径，然后使用`BrowserWindow.addDevToolsExtension(extension)`接口加载它。   

使用[React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)作为例子：   

1. 在Chrome浏览器中安装。   
2. 导航到`chrome://extensions`，然后找到那个扩展的ID，是哈希值形式的，就像这样：`fmkadmapgofadopljbjfkapdkoienihi`。   
3. 找到用于存储Chrome扩展的文件位置：   
 * Windows中是 `%LOCALAPPDATA%\Google\Chrome\User Data\Default\Extensions`；   
 * Linux中可能是：
    * `~/.config/google-chrome/Default/Extensions/`   
    * `~/.config/google-chrome-beta/Default/Extensions/`   
    * `~/.config/google-chrome-canary/Default/Extensions/`   
    * `~/.config/chromium/Default/Extensions/`   
 * MacOS中是`~/Library/Application Support/Google/Chrome/Default/Extensions`。   
4. 将扩展的位置传给`BrowserWindow.addDevToolsExtension`接口，对于React Developer Tools可能是这样的：`~/Library/Application Support/Google/Chrome/Default/Extensions/fmkadmapgofadopljbjfkapdkoienihi/0.15.0_0`

注意：`BrowserWindow.addDevToolsExtension`接口 API 不能在app模块尚未加载完之前被调用。   

扩展的名字将由接口`BrowserWindow.addDevToolsExtension`返回来，你可以将接口的名字传入`BrowserWindow.removeDevToolsExtension`接口来卸载它。   

### 支持的工具扩展

Electron仅支持一小部分`chrome.*`接口，如果一些扩展使用不支持的`chrome.*`接口会导致一些Chrome扩展的功能不能使用。以下工具扩展经过测试保证可以在Electron中工作：   

 * [Ember Inspector](https://chrome.google.com/webstore/detail/ember-inspector/bmdblncegkenkacieihfhpjfppoconhi)   
 * [React Developer Tools](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)   
 * [Backbone Debugger](https://chrome.google.com/webstore/detail/backbone-debugger/bhljhndlimiafopmmhjlgfpnnchjjbhd)   
 * [jQuery Debugger](https://chrome.google.com/webstore/detail/jquery-debugger/dbhhnnnpaeobfddmlalhnehgclcmjimi)   
 * [AngularJS Batarang](https://chrome.google.com/webstore/detail/angularjs-batarang/ighdmehidhipcmcojjgiloacoafjmpfk)   
 * [Vue.js devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)   
 * [Cerebral Debugger](http://www.cerebraljs.com/documentation/the_debugger)   
 * [Redux DevTools Extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd)   

##### 如果一个工具扩展没有工作我应该怎么办？

请确保这个扩展仍然在被维护中，一些扩展不能工作是因为Chorme浏览器的版本问题，而我们不能做任何事情来使它工作。   

然后在Electron的问题列表中记录一个bug，并描述是扩展的哪个部分没有按预期工作。   