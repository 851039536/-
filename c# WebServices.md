# c# WebServices

### 创建

1 创建一个**WebService**的项目

2 添加Web服务（ASMX)

3 启动查看是否正常



#### 实列

```c#
    public class WebService1 : System.Web.Services.WebService
    {
        SoundPlayer player = new SoundPlayer();
        [WebMethod]
        public string PlayMusic()
        {
            player.SoundLocation = @"D:\config\x0pbk-swz4q.wav";
            player.Load();
            player.Play();
            return "播放音乐中";
        }

        [WebMethod]
        public string StopMusic()
        {
            player.Stop();
            player.Dispose();
            return "音乐关闭";
        }
        [WebMethod(Description = "测试")]
        public string Test()
        {
            return "正常";
        }
    }
```

### 发布

选择文件夹

发布方法：文件系统

点击发布即可



### 服务引用

打开项目，右键添加服务引用

左下方点击 高级

输入：http://localhost:8092/WebService1.asmx



#### 实例

```c#
  TE.WebService1 webtest = new TE.WebService1();
  
   MessageBox.Show( webtest.PlayMusic());
```

