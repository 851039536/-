# c# 执行windows模拟登录

## 1.登录代码

```c#
    /// <summary>
        /// 登录权限
        /// </summary>
        /// <param name="path"></param>
        /// <param name="userName"></param>
        /// <param name="passWord"></param>
        /// <returns></returns>
          public  bool ConnectState(string path, string userName, string passWord)
        {
            bool Flag = false;
            Process proc = new Process();
            try
            {
                proc.StartInfo.FileName = "cmd.exe";
                proc.StartInfo.UseShellExecute = false;
                proc.StartInfo.RedirectStandardInput = true;
                proc.StartInfo.RedirectStandardOutput = true;
                proc.StartInfo.RedirectStandardError = true;
                proc.StartInfo.CreateNoWindow = true;
                proc.Start();
                string dosLine = "net use " + path + " " + passWord + " /user:" + userName;
                proc.StandardInput.WriteLine(dosLine);
                proc.StandardInput.WriteLine("exit");
                while (!proc.HasExited)
                {
                    proc.WaitForExit(1000);
                }
                string errormsg = proc.StandardError.ReadToEnd();
                proc.StandardError.Close();
                if (string.IsNullOrEmpty(errormsg))
                {
                    Flag = true;
                }
                else
                {
                    throw new Exception(errormsg);
                }
            }
            catch (Exception ex)
            {
              DisplaylistboxMsg(ex.Message);
            }
            finally
            {
                proc.Close();
                proc.Dispose();
            }
            return Flag;
        }
```

## 2.执行登录

```c#
 private void 登录_Click(object sender, EventArgs e)
        {
             bool userbool = ConnectState(@"\\10.55.2.3",User.Text,Pwd.Text);
           if (userbool)
           {
                DisplaylistboxMsg("登录成功："+User.Text);
           }
           else
           {
                DisplaylistboxMsg("登录失败");
           }
        }
```

