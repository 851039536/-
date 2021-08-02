# Blazor 入门

### @page

@page "/counter"：  `Counter` 组件呈现其内容

### [Parameter] 特性

定义公共属性

```c#
 [Parameter]  
public int IncrementAmount { get; set; } = 1;
```

在Index组件引用 Counter组件 并对属性赋值

<Counter IncrementAmount="10" />



### @bind

将 `CurrentValue` 属性绑定到文本框的值：

 `private string CurrentValue { get; set; }`

```c#
@* 调用 *@
<p>input CurrentValue： @CurrentValue</p>
<input @bind="CurrentValue" />
```

**@bind:event**

```c#
<input @bind="CurrentValue" @bind:event="oninput" />
```

与在元素失去焦点时激发的 `onchange` 不同，`oninput` 在文本框的值更改时激发。

### 格式字符串

```c#
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

