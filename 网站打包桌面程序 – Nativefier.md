# 网站打包桌面程序 – Nativefier

### [Nativefier](https://www.smbinn.com/tag/nativefier)简介

Nativefier 是一种命令行工具，可以用最少的配置轻松地为任何网站创建桌面应用程序。它是由 Electron 引擎生成的可执行文件（.app .exe 等），能够运行在Windows，MacOS和Linux平台。

[GitHub](https://www.smbinn.com/tag/github)页面：https://github.com/jiahaog/nativefier

### 安装条件

- macOS 10.9+ / Windows / Linux
- Node.js >= 10和npm>= 6

### 安装

首先需要安装Node.js，最新版本默认集成了npm，进入官网下载并安装到计算机。下载地址：https://nodejs.org/zh-cn/

安装好 Node.js 之后，还需要设置全局变量，因为加载极为缓慢，这里我们使用淘宝源来进行操作会快上不少。找到系统属性 – 高级 – 环境变量 – 新建用户变量。变量名：ELECTRON_MIRROR 变量值：http://npm.taobao.org/mirrors/electron/

接下来就到了安装 Nativefier 的环节，只需在cmd命令提示符中输入 npm install -g nativefier 即可完成安装。

最后，就可以用 Nativefier 来生成你想要的桌面应用程序了。例如要为https://www.smbinn.com创建桌面应用程序，只需在cmd命令提示符中输入 nativefier “https://www.smbinn.com” 生成后的程序默认在C盘用户文件夹。



出处:https://www.cnblogs.com/agressor/p/14186750.html

