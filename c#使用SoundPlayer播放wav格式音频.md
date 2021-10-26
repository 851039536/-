# c#使用SoundPlayer播放wav格式音频







### 使用

System.Media名称空间下的类`SoundPlayer` 可以让我们很方便的播放wav波形声音文件。`SoundPlayer`类其实就是对winmm.dll文件中API函数的封装。

`SoundPlayer`类的使用很简单。如下：

```js
SoundPlayer player = new SoundPlayer();
player.SoundLocation = "音乐文件名";
player.Load();
player.Play();
```


> 其中Play方法是异步方法，会在另一个线程中播放。如果我们有时候需要等声音播放完毕之后再进行下一步操作。即声音播放需要阻塞当前线程。就可以使用PlaySync()

### 方法

- SoundPlayer类的缺点：只能播放wav文件；在winxp下播放文件比较大或位率比较高的情况，PlaySync同步播放会有播放不完全的问题。

- 这个问题的产生是由于winmm.dll的版本问题引起的。在xp下winmm.dll的版本是5。在win7下是6。win7下就没有问题。如果要解决在

- xp下播放不完全的问题。可以使用xp下的录音机打开声音文件，把声音文件另存为7kbit/s的位率格式，但这样声音效果就很差了。

### 实现

引用System.Media名称空间下的类SoundPlayer 

```js
  SoundPlayer player = new SoundPlayer();
```

 

### 方法调用Play();

 

```js
public void Play() 
{
	**player.SoundLocation** = @".x0pbk-swz4q.wav";
	//读取音频文件
	**player.Load();
	**//同步模式
	**player.Play();
	**
	 if (MessageBox.Show("播放音樂中，通过旋转耳机上的旋钮测试音量,若聲音播放正常则选是，反之否", "提示", MessageBoxButtons.YesNo, MessageBoxIcon.Information) ==  DialogResult.No) 
	{
		DisplaylistboxMSG("Headset測試結果為異常");
	} else 
	{
		DisplaylistboxMSG("Headset測試結果為正常");
	}
	player.Stop();
	//停止播放
	player.Dispose();
}
```

 