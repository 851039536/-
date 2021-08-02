# Blazor 生命周期

### 组件初始化方法

**同步操作**

```c#
protected override void OnInitialized()
{
    ...
}
```

**异步操作**

```c#
protected override async Task OnInitializedAsync()
{
    await ...
}
```

### 设置参数之前

[SetParametersAsync](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.components.componentbase.setparametersasync) 在呈现树中设置组件的父组件提供的参数：

```c#
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

### 设置参数之后

[OnParametersSetAsync](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.components.componentbase.onparameterssetasync) 和 [OnParametersSet](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.components.componentbase.onparametersset) 在以下情况下调用：

- 当组件被初始化并从其父组件收到其第一组参数时。
- 当父组件重新呈现并提供以下内容时：
  - 至少一个参数已更改的唯一已知基元不可变类型。
  - 任何复杂类型的参数。 框架无法知道复杂类型参数的值是否在内部发生了改变，因此，它将参数集视为已更改。

```c#
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

 **备注**

> 应用参数和属性值时，异步操作必须在 `OnParametersSetAsync` 生命周期事件期间发生。

```c#
protected override void OnParametersSet()
{
    ...
}
```

### 组件呈现之后

[OnAfterRenderAsync](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.components.componentbase.onafterrenderasync) 和 [OnAfterRender](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.components.componentbase.onafterrender) 在组件完成呈现后调用。 此时会填充元素和组件引用。 在此阶段中，可使用呈现的内容执行其他初始化步骤，例如激活对呈现的 DOM 元素进行操作的第三方 JavaScript 库。

`OnAfterRenderAsync` 和 `OnAfterRender` 的 `firstRender` 参数：

- 在第一次呈现组件实例时设置为 `true`。
- 可用于确保初始化操作仅执行一次。

```c#
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

 **备注**

呈现后立即进行的异步操作必须在 `OnAfterRenderAsync` 生命周期事件期间发生。

即使从 `OnAfterRenderAsync` 返回 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task)，框架也不会在任务完成后为组件再安排一个呈现循环。 这是为了避免无限呈现循环。 它与其他生命周期方法不同，后者在返回的任务完成后会再安排呈现循环。

```c#
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### 禁止 UI 刷新

替代 [ShouldRender](https://docs.microsoft.com/zh-cn/dotnet/api/microsoft.aspnetcore.components.componentbase.shouldrender) 以禁止 UI 刷新。 如果实现返回 `true`，则刷新 UI：

```c#
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

