### 定义委托

```c#
 public delegate void SendMessageToChildForms(string s); //定义了一个参数是string ，无返回值的委托，名为 SendMessageToChildForms。
```

### 委托实例化

```c#
// 本质就是实例化了一个事件event
 public event SendMessageToChildForms smtcf_event;
```



###   定义具体执行的方法
```c#
public void ToShowGetMessage(string s)
{
     this.lb_收到内容.Text=s;
}
```

### 绑定方法

```c#
 Parameter frm_child = new Parameter();
            smtcf_event += frm_child.ToShowGetMessage; //在一实例化的一个委托事件上绑定子窗体的具体方法
            frm_child.Show();
```

### 触发委托

```c#
        if (smtcf_event != null) //判断委托事件是否为空，如果委托不为空才执行
        {
            smtcf_event.Invoke("12212");// 可以省略Invoke 简写为smtcf(this.textBox1.Text.Trim());
        }
```
