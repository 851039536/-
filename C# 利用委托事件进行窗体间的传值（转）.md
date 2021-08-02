原文：[https://www.cnblogs.com/arcticfish/archive/2020/02/12/12297675.html][1]

引言：

　　窗体间传值是每个学习WinForm新手的常见问题，最初级的方法就是 在窗体中先获取到要接受值窗体。然后通过.得到某个空间或者属性，直接赋值，这个需要接收放的窗体属性或者空间必须是public ，是极不安全的一种方式。在升级一下的新手做法就是在接受窗体中定义一个方法然后通过 调用这个方法来给某个控件或者属性赋值。这两种方法都是非安全的低级方式。非常不建议使用。本文我们讲讲如何通过委托事件（非单纯的委托）来解决窗体间信息传递的问题。

场景：


 通过点击主主窗体From_Main的发送按钮，所有子窗体中显示出发送的内容


学习前提：
    

 对委托有初步了解。如果你对委托一点不了解，建议先看一下本人前一篇关于委托的基础介绍文章。

正文：

第1步：定一个委托 

代码：　   

     public delegate void SendMessageToChildForms(string s); //定义了一个参数是string ，无返回值的委托，名为SendMessageToChildForms。

说明：1.他可以定义在项目中任何可以被访问到的地方（如主窗体，某一个子窗体，或者一个类） 。但原则上定义在一直存在的窗体中，本例在主窗体中的命名空间标题下。

​        2.系统内置了两个定义好的委托类型 Action 和Func ,你也可以直接省略这一步。在第3步实例化他就可以了。其中Action是无返回值的委托，Func是带返回值的委托。他们的输入参个数数都是0~16个。

第2步：实例化一个此委托类型的事件

代码：

　　　　

    public event SendMessageToChildForms smtcf_event; 

说明：1.可以省略event ,public有时也不写，就可以简写成 SendMessageToChildForms smtcf;不利于新手理解，其实他本质就是实例化了一个事件event

　　　2.如果我们使用了微软内置的委托类型Action或者Func 则可以写成这样 public event Action<string> SendMessageToChildForms;  这里string 必须要写，因为没有第1步所以这里需要指明参数列表。

​             3.委托与委托事件的关系，本例中使用了委托事件 ，相对于委托更安全。更低耦合。委托是一个类型，例如SendMessageToChildForms类，事件是委托类型的一个实例 如smtcf_event。

第3步：定义具体执行的方法 

代码：

    public void ToShowGetMessage(string s)
    {
         this.lb_收到内容.Text=s;
    }

说明：1.具体的功能方法，这里那个窗体需要执行就写在哪个窗体里，比如本例是更新子窗体的显示，所以写到子窗体里，如果换成另一场景：子窗体中删除了当前这条信息，需要更新，那么就定义到主窗体中 

​      

 2.方法的参数列表及返回类型必须与委托完全一致。比如本例，参数列表是一个string消息字符串，返回类型是void空，方法必须是public；

第4步：方法绑定到委托上

代码：　

    Form_Child frm_child = new Form_Child();
    smtcf_event+= new SendMessageToChildForms(frm_child.ToShowGetMessage); //在一实例化的一个委托事件上绑定子窗体的具体方法
    frm_child.Show();
    
    Form_Child frm_child2 = new Form_Child();
    smtcf_event+= new SendMessageToChildForms(frm_child2.ToShowGetMessage); //在一实例化的一个委托事件上绑定子窗体的具体方法
    frm_child2.Show();

注意：1.把要执行的方法在新窗体初始化时绑定到他里面定义好的委托类型事件上，必须要在子窗体Show()时间之前

​             2.一个委托事件smtcf 可以绑定多个方法。



第5步：触发委托

代码：

    //委托第5步：触发委托
    if (smtcf_event!= null) //判断委托事件是否为空，如果委托不为空才执行
    {
    smtcf_event.Invoke(this.textBox1.Text.Trim());// 可以省略Invoke 简写为smtcf(this.textBox1.Text.Trim());
    }

说明：1.必须首先判断一下委托事件smtcf是否为空。再执行；

　  　  2.一般 简写为smtcf_event(this.textBox1.Text.Trim());

  


[1]: https://www.cnblogs.com/arcticfish/archive/2020/02/12/12297675.html