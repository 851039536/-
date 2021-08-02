# ReSharper

### ReSharper和谐详细方法：

1.先安装好Resharper；

2.下载完补丁后解压，复制到%LOCALAPPDATA%\JetBrains\Installations文件夹下，以管理员身份运行和谐补丁（补丁只支持resharper以用户模式安装，不支持管理员模式安装）；

3.打开VS，打开ReSharper的注册窗口：ReSharper->Help->License Information...



### Resharper首选项配置

Resharper->Manage Options->Import from File,选择配置文件导入即可。

### Ctrl+Shift+R万般兼重构

1. 封装字段 —— Introduce Field
2. 提取方法 —— Extract Method
3. 提取接口 —— Extract Interface （另增加了Extract Superclass提取为基类）
4. 提升局部变量 —— Introduce Variable
5. 移除参数 —— 移到Change Signature（改变方法签名）中
6. 重命名 —— Rename （Resharper会根据对象的类型名称，提供你几个可选的最合适的名称）
7. 重新排列参数 —— 移到Change Signature（改变方法签名）中

Ctrl +Alt + F 格式化代码

Alt+Insert自动生成代码

Ctrl + D复制当 前行或选定的块

Ctrl + Alt + Insert 添加类

Ctrl +F11 查看定义方法



快速代码定位的核心就是三个功能：

转到定义：Ctrl+B;

查找所有使用：Alt+F7;

从这里浏览：Ctrl+Shift+G.

用Ctrl+F12 找到需要的方法