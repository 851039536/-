# 使用宝塔搭建NET5Api项目

腾讯云安装 Centos8系统

### NET环境安装

在安装DotNetCore环境之前需要注册Microsoft Key以及Product Repository，并且还需要安装一些依赖，这一步每台机器只需要运行一次，具体命令如下

```
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm
```

安装net5运行时安装

https://dotnet.microsoft.com/download/dotnet/5.0 

测试是否安装成功：

```
dotnet --version
```

### 部署 NET5 API



程序发布过程省略(发布文件系统)，把编译后的程序发布到了本地 桌面\publish 文件夹

然后借助 FTP 工具 XFTP 把程序文件传输到服务器/home/wwwroot文件夹。

上传完毕后，需要先通过`cd`命令进入网站根目录`/home/wwwroot`，再输入如下命令启动网站程序：

```c#
cd /www/wwwroot/net
dotnet Snblog.dll 
```



宝塔面板发布.NET5 API项目,并启动项目

### Nginx设置反代访问

目标url http://localhost:5000

发送域名 localhost

名称 可选随意



### 添加进程守护

宝塔安装 Supervisor

添加进程

启动用户： root 守护目录： /www/wwwroot/net   启动命令：dotnet Snblog.dll



### vue框架刷新出现404的问题

```c#
在网站设置里配置伪静态

if (!-e $request_filename) {
rewrite ^/(.*) /index.html last;
break;
}
```

