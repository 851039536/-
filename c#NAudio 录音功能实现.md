# c#NAudio 录音功能实现

在网上找了很多类似录音教程效果都不好，或根本不能录音，代码由网上借鉴修改（完整实现录音播放功能）

### NAudio

NAudio为.NET平台下的开源库，采用ML-PL协议，开源地址：https://github.com/naudio/NAudio。

NAudio功能强大，且其入门容易。
强大在于：它支持许多音频操作，可实现多种API播放与录制、多种不同音频格式、音频格式转换（重采样、位深、声道等）、音频编码、多通道播放、音频效果处理等等（详细介绍可以看Github readme）。

###  新建RecordController

首先新建引用类  RecordController

```js
public class RecordController 
{
        public WaveIn mWavIn;
        public WaveFileWriter mWavWriter;
        /// <summary>
        /// 开始录音
        /// </summary>
        /// <param name="filePath"></param>
        public void StartRecord(string filePath) 
        {
                mWavIn = new WaveIn();
                mWavIn.DataAvailable += MWavIn_DataAvailable;
                // mWavIn.RecordingStopped += MWavIn_RecordingStopped; 有冲突
                mWavWriter = new WaveFileWriter(filePath, mWavIn.WaveFormat);
                mWavIn.StartRecording();
        }
        /// <summary>
        /// 停止录音
        /// </summary>
        public void StopRecord() 
        {
                mWavIn?.StopRecording();
                mWavIn?.Dispose();
                mWavIn = null;
                mWavWriter?.Close();
                mWavWriter = null;
        }
        //这个方法在调用关闭时会有冲突
        private void MWavIn_RecordingStopped(object sender, StoppedEventArgs e) 
        {
                //mWavIn?.Dispose();
                //mWavIn = null;
                //mWavWriter?.Close();
                //mWavWriter = null;
        }
        private void MWavIn_DataAvailable(object sender, WaveInEventArgs e) 
        {
                mWavWriter.Write(e.Buffer, 0, e.BytesRecorded);
                int secondsRecorded = (int)mWavWriter.Length / mWavWriter.WaveFormat.AverageBytesPerSecond;
        }
}
```

### 使用

在主界面引用  RecordController record = new RecordController();

```js
/// <summary>
/// 耳机sidetone测试
/// </summary>
/// <param name="i"></param>
public void Sidetone(int i) 
{
        string[] row = getlistviewitem(i);
        record.StartRecord("e:\rec.wav");
        if (MessageBox.Show("正在录音，点击是播放录音", "提示", MessageBoxButtons.YesNo, MessageBoxIcon.Information) == DialogResult.No) 
        {
                record.StopRecord();
                ErrorFlag = false;
                row[6] = "FAIL";
        } else 
        {
                record.StopRecord();
                string path = "e:\rec.wav";
                //.wav音频文件路径
                System.Media.SoundPlayer player = new System.Media.SoundPlayer(path);
                player.Play();
                if (MessageBox.Show("是否有录音", "提示", MessageBoxButtons.YesNo, MessageBoxIcon.Information) == DialogResult.No) 
                {
                        ErrorFlag = false;
                        row[6] = "FAIL";
                } else 
                {
                        ErrorFlag = true;
                        row[5] = "OK";
                        row[6] = "PASS";
                }
        }
        listupdate(row, i);
}
```

完成代码后测试是能正常录音