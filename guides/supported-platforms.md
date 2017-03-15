# 2.支持的平台

> 原文地址：https://github.com/electron/electron/blob/master/docs/tutorial/supported-platforms.md   
译者：[Lin](https://github.com/ShmilyLin)   

以下平台支持Electron：   

## MacOS

对于MacOS只提供64位的二进制文件，支持的最早的版本是MacOS 10.9。   

## Windows

只支持Windows7以及之后的系统，不支持之前的版本（Electron在其上不会工作）。   

对于Windows提供`ia32`（`x86`）和`x64`（`amd64`）的二进制文件。请注意，现在不支持ARM版本的Windows。   

## Linux

Electron的预编译的`ia32`（`i686`）和`x64`（`amd64`）二进制文件是在Ubuntu 12.04的基础上编译的，`arm`二进制文件是在ARM v7 with hard-float ABI和NEON for Debian Wheezy之上编译的。   

预编译二进制文件是否可以运行在一个发行版上取决于这个发行版是否包含了Electron链接的编译平台的库，所以只有Ubuntu 12.04是确定能够工作的，但是下面的平台经过检验能够运行Electron的预编译二进制文件：   

 * Ubuntu 12.04及以上版本   
 * Fedora 21   
 * Debian 8   