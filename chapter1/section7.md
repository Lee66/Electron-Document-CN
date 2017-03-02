# 7.使用VSCode调试的主要过程

> 原文：https://electron.atom.io/docs/tutorial/debugging-main-process-vscode/   
译者：Lin

###1. 使用VSCode打开Electron项目

    $ git clone git@github.com:electron/electron-quick-start.git   
    $ code electron-quick-start

###2. 项目中添加文件夹`.vscode`，文件夹下新建`launch.json`文件并包含以下配置

    {
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Debug Main Process",
                "type": "node",
                "request": "launch",
                "cwd": "${workspaceRoot}",
                "runtimeExecutable":"${workspaceRoot}/node_modules/.bin/electron",
                "program": "${workspaceRoot}/main.js"
            }
        ]
    }
注意：Windows环境下，`runtimeExecutable`中填写`"${workspaceRoot}/node_modules/.bin/electron.cmd"`。

###3. 调试
在`main.js`中设置一些断点，并开始在Debug View中调试。你应该会击中设置的断点。

这是一个预先配置好的项目，你可以直接下载然后在VSCode中调试：https://github.com/octref/vscode-electron-debug/tree/master/electron-quick-start