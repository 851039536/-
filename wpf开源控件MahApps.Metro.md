# wpf开源控件MahApps.Metro

### 安装

您可以通过NuGet GUI（右键单击您的项目，单击**Manage NuGet Packages**，选择**Online**并搜索**MahApps.Metro**）或使用Package Manager控制台安装MahApps.Metro。

```c#
PM> Install-Package MahApps.Metro
```

或使用软件包管理器控制台：

```c#
PM> Install-Package MahApps.Metro -Pre
```

### 造型窗口

您可以使用两种方法使用MahApps.Metro设置Window的样式：

#### 修改XAML文件

安装MahApps.Metro之后：

- 打开 `MainWindow.xaml`
- 在打开的Window标记内添加此属性。（这是您在XAML中引用其他名称空间的方式）：
  `xmlns:Controls="clr-namespace:MahApps.Metro.Controls;assembly=MahApps.Metro"`
  或
  `xmlns:Controls="http://metro.mahapps.com/winfx/xaml/controls"`
- 将`标签更改为`（请记住也要更改结束标签！）

您应该有类似以下内容（请勿复制和粘贴）：

```xaml
<Controls:MetroWindow x:Class="WpfApplication.MainWindow"
                      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                      xmlns:Controls="clr-namespace:MahApps.Metro.Controls;assembly=MahApps.Metro"
                      Title="MainWindow"
                      Height="600"
                      Width="800">

  <!-- your content -->

</Controls:MetroWindow>
```

#### 修改CodeBehind文件

您还需要修改`MainWindow.xaml.cs`文件，以使其基类`MainWindow`与`MetroWindow`XAML文件的类匹配。

```c#
// To access MetroWindow, add the following reference
using MahApps.Metro.Controls;

namespace WpfApplication
{
  public partial class MainWindow : MetroWindow
  {
    public MainWindow()
    {
      InitializeComponent();
    }
  }
}
```

但是在大多数情况下，您可以删除基类（因为这是`partial`XAML应该处理的类）：

```c#
namespace WpfApplication
{
  public partial class MainWindow
  {
    public MainWindow()
    {
      InitializeComponent();
    }
  }
}
```

#### 使用内置样式

MahApp.Metro的所有资源都包含在单独的资源词典中。为了使大多数控件采用MahApps.Metro主题，您需要将ResourceDictionaries添加到`App.xaml`。

**App.xaml（v2.0.0及更高版本）**

```xaml
<Application x:Class="WpfApplication.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
  <Application.Resources>
    <ResourceDictionary>
      <ResourceDictionary.MergedDictionaries>
        <!-- MahApps.Metro resource dictionaries. Make sure that all file names are Case Sensitive! -->
        <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Controls.xaml" />
        <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Fonts.xaml" />
        <!-- Accent and AppTheme setting -->
        <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Themes/Light.Blue.xaml" />
      </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
  </Application.Resources>
</Application>
```

**App.xaml（v1.6.5和更低版本）**

```xaml
<Application x:Class="WpfApplication.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
  <Application.Resources>
    <ResourceDictionary>
      <ResourceDictionary.MergedDictionaries>
        <!-- MahApps.Metro resource dictionaries. Make sure that all file names are Case Sensitive! -->
        <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Controls.xaml" />
        <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Fonts.xaml" />
        <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Colors.xaml" />
        <!-- Accent and AppTheme setting -->
        <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Accents/Blue.xaml" />
        <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Accents/BaseLight.xaml" />
      </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
  </Application.Resources>
</Application>
```