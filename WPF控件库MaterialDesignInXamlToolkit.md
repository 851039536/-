# WPF控件库MaterialDesignInXamlToolkit



引用 MaterialDesignThemes.Wpf.dll

**第一步：安装控件库**

```c#
Install-Package MaterialDesignThemes
```

**第二步：配置App.xaml**

你需要合并一个主题（Dark或者Light）到你的资源字典，你只需要添如下代码到你的资源字典就能完成此步骤

Light theme：

```c#
<ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Light.xaml" />
```

 Dark theme:

```c#
<ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Dark.xaml" />
```

**第三步：添加色彩**

```c#
<ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Primary/MaterialDesignColor.COLOR_NAME.xaml" />
<ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Accent/MaterialDesignColor.COLOR_NAME.xaml" />
```

**App.xaml文件**

```c#
<Application x:Class="MaterialTest.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>

                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Light.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignThemes.Wpf;component/Themes/MaterialDesignTheme.Defaults.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Primary/MaterialDesignColor.DeepPurple.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MaterialDesignColors;component/Themes/Recommended/Accent/MaterialDesignColor.Lime.xaml" />

            </ResourceDictionary.MergedDictionaries>            
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

**第四步：配置你的主窗口文件**



```c#
<Window [...]
        TextElement.Foreground="{DynamicResource MaterialDesignBody}"
        Background="{DynamicResource MaterialDesignPaper}"
        TextElement.FontWeight="Medium"
        TextElement.FontSize="14"
        FontFamily="pack://application:,,,/MaterialDesignThemes.Wpf;component/Resources/Roboto/#Roboto"
        [...] >
```

