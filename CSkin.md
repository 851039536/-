# CSkin



下载地址:[CSkin](http://www.cskin.net/)

### 使用教程：

1.工具箱右键-新建项-命名CSkin。

2.将CSkin.dll拖到新建的工具箱栏里。

3.控件添加完毕，控件拖拽到界面即可使用和引用。

4.窗体美化需要继承CCSkinMain。
 **如：**

```c#
 public partial class FrmMain : Form
```

 **改成：**

```c#
 public partial class FrmMain : CCSkinMain
```

**窗体还可以继承以下等多种主题：**

```c#
Skin_Color，Skin_DevExpress，Skin_Mac，Skin_Metro，Skin_VS
```





