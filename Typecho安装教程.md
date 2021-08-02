# Typecho安装教程

## 安装宝塔

**Linux面板7.1.0安装命令：**

```
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

**面板特色功能：**

- 一键配置服务器环境（LAMP/LNMP）
- 一键安全重启
- 一键创建管理网站、ftp、数据库
- 一键配置（定期备份、数据导入、伪静态、301、SSL、子目录、反向代理、切换PHP版本）
- 一键安装常用PHP扩展(fileinfo、intl、opcache、imap、memcache、apc、redis、ioncube、imagick)
- 数据库一键导入导出
- 系统监控（CPU、内存、磁盘IO、网络IO）
- 防火墙端口放行
- SSH开启与关闭及SSH端口更改
- 禁PING开启或关闭
- 方便高效的文件管理器（上传、下载、压缩、解压、查看、编辑等等）
- 计划任务（定期备份、日志切割、shell脚本）
- 软件管理（一键安装、卸载、版本切换）





## 安装Typecho

**1.准备工作**

> 注意检查你的服务器是否支持Typecho完整运行 1. PHP 5.1以上 2. Mysql, PostgreSQL, SQLite 任意一种数据库支持，并在PHP中安装了相关扩展 3. CURL或者Socket扩展支持 4. mbstring或者iconv扩展支持

**安装Typecho的过程和大多数博客程序没有什么不同，整个安装过程只需要你根据安装向导依次完成即可。安装中若遇到问题，可以随时到我们的[typecho问答社区](http://asks.typechodev.com/)或者入QQ群(`556055042`)中寻求更多帮助。**

**2.下载最新版**

- 1.请访问typecho[官方网站](http://typecho.org/download)获得最新的稳定版本，并下载。
- 2.可以访问[Typechodev.com高速镜像](https://www.typechodev.com/download_typecho.html)进行下载。
- 3.下载Typecho的高速优化版本(即[HPTypecho](https://www.typechodev.com/download_hptypecho.html))。

------

## 安装步骤

**1.解压缩安装包**

解压缩后你会看到如下的目录结构

```
/admin/
/install/
/usr/
/var/
/license.txt
/index.php
/install.php
```

**2.上传至服务器WEB目录**

将上面列出的所有文件和目录上传到服务器上的指定目录，如DocumentRoot目录或者任何你希望安装typecho的目录。

```
使用应用引擎部署程序
如果你正在使用GAE, SAE, BAE等等这些应用引擎来部署程序，由于它们使用GIT或者SVN管理版本。
你可以直接在本地创建以上这些目录和文件，并将它们加入到版本控制系统中，然后部署到应用引擎里。
```

**3.访问你的blog(网站)地址**

上传完毕后使用浏览器直接访问安装目录即可看到Typecho的安装程序。恭喜，你的服务器可以完美支持typecho，点击进入下一步。

![typecho安装示例界面](https://www.typechodev.com/docs/zh_CN/image/example_install.png)

**4.填写配置信息**

按照程序安装向导的要求填写相关服务器参数和初始化设置信息，完成后点击下一步。

```
特别强调:
安装过程中要记录好自己所写的信息,以避免刚安装就忘了登录密码的尴尬。
```

如果真的忘记了密码,可以通过[忘记typecho登录密码怎么办](https://www.typechodev.com/case/how-to-modify-password-of-typecho-admin.html)文章中更改密码。

**5.完成安装**

在安装成功界面中会显示自动生成的初始登录密码，请务必牢记或马上进入后台按提示更改。已经大功告成，欢迎来到Typecho的世界！

```
万一不慎丢失初始密码可以删除安装目录下生成的config.inc.php文件，然后重新安装选择保留原有数据库即可。
```



## 主题安装

https://typecho.me/主题站

将下载的主题放入`/usr/themes/`目录下

登录控制台使用和配置外观即可