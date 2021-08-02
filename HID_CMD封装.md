# HID常用封装

### ButtonTest

```c#
using System;
using System.IO;
using System.Runtime.InteropServices;
using System.Threading;
using System.Windows.Forms;
using Microsoft.Win32.SafeHandles;

namespace HDT668_HID_CMD.Interface
{
    class ButtonTest
    {
        #region 参数及外部引用
        string value = "";
        int[] arr;
        bool ErrorFlag = true;
        string type = "";
        private const uint GenericRead = 0x80000000;
        private const uint GenericWrite = 0x40000000;
        private const uint FileShareWrite = 0x2;
        private const uint FileShareRead = 0x1;
        private const uint FileFlagOverlapped = 0x40000000;
        private const uint OpenExisting = 3;
        public const int WmClose = 0x10;
        private FileStream _fsDeviceRead;
        private AsyncCallback _asyRead;
        static string _str;
        static bool errorflag = true;
        static int lenght;
        static IntPtr intPtr;
        static bool commandflag;
        ProgressBars msgbox;
        string[] readdata;
        int[] flag;
        string formname = "";
        [DllImport("kernel32.dll", SetLastError = true)]
        private static extern IntPtr CreateFile([MarshalAs(UnmanagedType.LPStr)] string strName, uint nAccess, uint nShareMode, IntPtr lpSecurity, uint nCreationFlags, uint nAttributes, IntPtr lpTemplate);
        [DllImport("user32.dll", EntryPoint = "FindWindow")]
        private extern static IntPtr FindWindow(string lpClassName, string lpWindowName);
        [DllImport("User32.dll", EntryPoint = "PostMessage")]
        public static extern int PostMessage(
            IntPtr hWnd,        // 信息发往的窗口的句柄
            int Msg,            // 消息ID
            int wParam,         // 参数1
            int lParam            // 参数2
        );
        #endregion
        #region 创建异步读取
        private bool CT_CreateFile(string handle)
        {
            IntPtr _createFileHandle = CreateFile(handle,          //文件位置
                            GenericWrite | GenericRead,          //允许对设备进行读写访问
                            FileShareRead | FileShareWrite,    //允许对设备进行共享访问
                            IntPtr.Zero,                           //指向空指针（SECURITY_ATTRIBUTES定义文件的安全特性）
                            OpenExisting,                         //文件必须已存在
                            FileFlagOverlapped,                  //允许对文件进行重叠操作
                            IntPtr.Zero);                          //指向空指针（如果不为零，则指定一个文件句柄。新文件将从这个文件中复制扩展属性）

            _fsDeviceRead = new FileStream(new SafeFileHandle(_createFileHandle, false), FileAccess.Read | FileAccess.Write, 36, true);
            if (_fsDeviceRead == null) return false;
            else
            {
                _asyRead = ReadCompleted;
            }

            return true;
        }
        private bool FWCT_CreateFile(string handle)
        {
            IntPtr _createFileHandle = CreateFile(handle,          //文件位置
                            GenericWrite | GenericRead,          //允许对设备进行读写访问
                            FileShareRead | FileShareWrite,    //允许对设备进行共享访问
                            IntPtr.Zero,                           //指向空指针（SECURITY_ATTRIBUTES定义文件的安全特性）
                            OpenExisting,                         //文件必须已存在
                            FileFlagOverlapped,                  //允许对文件进行重叠操作
                            IntPtr.Zero);                          //指向空指针（如果不为零，则指定一个文件句柄。新文件将从这个文件中复制扩展属性）

            _fsDeviceRead = new FileStream(new SafeFileHandle(_createFileHandle, false), FileAccess.Read | FileAccess.Write, 62, true);
            if (_fsDeviceRead == null) return false;
            else
            {
                _asyRead = FwReadCompleted;
            }

            return true;
        }
        private void BeginAsyncRead()
        {
            byte[] arrInputReport = new byte[62];
            if (_fsDeviceRead != null)
            {
                _fsDeviceRead.BeginRead(arrInputReport, 0, 62, _asyRead, arrInputReport);
            }
        }
        #endregion
        #region 进度条
        private bool MessgBox(string name)
        {
            try
            {
                msgbox = new ProgressBars(name, true);
                if (msgbox.ShowDialog() == DialogResult.OK)
                {
                    errorflag = true;
                    return true;
                }
                else
                {
                    errorflag = false;
                    return false;
                }
            }
            catch (Exception)
            {
                return false;
            }
        }
        #endregion
        #region 异步读取发生时执行的方法
        private void ReadCompleted(IAsyncResult iResult)
        {
            bool testflag = true;
            try
            {
                byte[] arrBuff = (byte[])iResult.AsyncState;
                if (_fsDeviceRead != null)
                {
                    _fsDeviceRead.EndRead(iResult);
                }
                for (int i = 0; i < readdata.Length; i++)
                {
                    if (Convert.ToString(arrBuff[flag[i]]) != readdata[i]) { testflag = false; break; }
                }
                if (testflag)
                {
                    IntPtr ptr = FindWindow(null, formname);
                    if (ptr != IntPtr.Zero)
                        Thread.Sleep(50);
                    msgbox.DialogResult = DialogResult.OK;
                    Thread.Sleep(50);
                    PostMessage(ptr, WmClose, 0, 0);
                    return;
                }
                BeginAsyncRead();
                if (!commandflag)
                {
                    Thread.Sleep(200);
                    Command.command(_str, lenght, intPtr);
                }
            }
            catch
            {
                // ignored
            }
        }
        #endregion
        #region FW异步读取发生时执行的方法
        private void FwReadCompleted(IAsyncResult iResult)
        {
            bool testflag = true;
            try
            {
                byte[] arrBuff = (byte[])iResult.AsyncState;
                if (_fsDeviceRead != null)
                {
                    _fsDeviceRead.EndRead(iResult);
                }
                for (int i = 0; i < readdata.Length; i++)
                {
                    if (Convert.ToString(arrBuff[flag[i]]) != readdata[i]) { testflag = false; break; }
                }
                if (testflag)
                {

                    switch (type)
                    {
                        case "FW": value = ""; break;
                        case "电压":
                            foreach (int a in arr)
                            {
                                value = value + string.Format("{0:X2}", arrBuff[a]);
                            }
                            value = value.Substring(0, value.Length - 1); break;
                        case "Charge": break;
                    }
                    ErrorFlag = false;
                }
                else
                {
                    ErrorFlag = false;
                }

            }
            catch
            {
                // ignored
            }
        }
        #endregion
        #region 测试按键对外方法  buttontest
        /// <summary>
        /// 测试按键对外方法 buttontest
        /// </summary>
        /// <param name="readdata">按键操作对应指令返回值</param>
        /// <param name="flag">按键操作对应指令返回下標</param>
        /// <param name="name">按键操作对应窗口名</param>
        /// <param name="handle">通道地址</param>
        /// <returns></returns>
        public bool Buttontest(string[] readdata, string[] flag, string name, string handle)
        {
            commandflag = true;
            this.readdata = readdata;
            this.flag = new int[flag.Length];
            for (int i = 0; i < flag.Length; i++)
            {
                this.flag[i] = Convert.ToInt16(flag[i]);
            }
            if (!CT_CreateFile(handle)) return false;
            BeginAsyncRead();
            return MessgBox(name);

        }
        #endregion
        #region FW异步读取对外方法

        /// <summary>
        /// 测试按键对外方法 buttontest
        /// </summary>
        /// <param name="readdata">按键操作对应指令返回值</param>
        /// <param name="flag">按键操作对应指令返回下標</param>
        /// <param name="handle">通道地址</param>
        /// <returns></returns>
        public string fwtest(string[] readdata, string[] flag, string handle, string type, int[] arr)
        {
            ErrorFlag = true;
            this.arr = arr;
            this.readdata = readdata;
            this.type = type;
            this.flag = new int[flag.Length];
            for (int i = 0; i < flag.Length; i++)
            {
                this.flag[i] = Convert.ToInt16(flag[i]);
            }
            if (!FWCT_CreateFile(handle)) return "false";
            BeginAsyncRead();
            while (ErrorFlag)
            {
                switch (type)
                {
                    case "电压":
                        string command = "06 00 02 00 9A 00 00 68 4A 8E 0A 00 00 00 BB 02";
                        Command.Writecommand(command, 62, GetHandle.donglehandle[2], "5 6", "testVoltage", out value);
                        Thread.Sleep(20);
                        Command.GetReportcommand(command, 62, GetHandle.donglehandle[2]);
                        break;
                }
                Thread.Sleep(10);
            }
            return value;
        }
        #endregion
        #region jack测试对外方法 jacktest
        /// <summary>
        /// jack测试对外方法
        /// </summary>
        /// <param name="str1">指令</param>
        /// <param name="lenght1">指令长度</param>
        /// <param name="intPtr1">通道指针对象</param>
        /// <param name="readdata">返回值</param>
        /// <param name="flag">返回值下标</param>
        /// <param name="name">弹出窗口名</param>
        /// <param name="handle">通道地址</param>
        /// <returns></returns>
        public bool jacktest(string str1, int lenght1, IntPtr intPtr1, string[] readdata, string[] flag, string name, string handle)
        {
            commandflag = false;
            _str = str1;
            lenght = lenght1;
            intPtr = intPtr1;
            this.readdata = readdata;
            this.flag = new int[flag.Length];
            for (int i = 0; i < flag.Length; i++)
            {
                this.flag[i] = Convert.ToInt16(flag[i]);
            }
            if (!CT_CreateFile(handle)) return false;
            BeginAsyncRead();
            Command.command(_str, lenght, intPtr);
            return MessgBox(name);

        }
        #endregion
    }
}

```

### Command

```c#
using System;
using System.IO;
using System.Runtime.InteropServices;
using System.Threading;
using System.Windows.Forms;
using HDT668_HID_CMD.Util;
using Microsoft.Win32.SafeHandles;

namespace HDT668_HID_CMD.Interface
{
    public static class Command
    {
        #region 引入方法 参数
        private const uint GenericRead = 0x80000000;
        private const uint GenericWrite = 0x40000000;
        private const uint FileShareWrite = 0x2;
        private const uint FileShareRead = 0x1;
        private const uint FileFlagOverlapped = 0x40000000;
        private const uint OpenExisting = 3;
        [DllImport("kernel32.dll", SetLastError = true)]
        private static extern IntPtr CreateFile([MarshalAs(UnmanagedType.LPStr)] string strName, uint nAccess, uint nShareMode, IntPtr lpSecurity, uint nCreationFlags, uint nAttributes, IntPtr lpTemplate);

        [DllImport("hid.dll", SetLastError = true)]
        static extern Boolean HidD_GetFeature(IntPtr hidDeviceObject, Byte[] lpReportBuffer, Int32 reportBufferLength);
        [DllImport("hid.dll", SetLastError = true)]
        static extern Boolean HidD_SetFeature(IntPtr hidDeviceObject, Byte[] lpReportBuffer, Int32 reportBufferLength);
        public static string pairid = "";

        [DllImport("Kernel32.dll", SetLastError = true)]
        private static extern bool WriteFile(
                                        IntPtr hFile,
                                        byte[] lpBuffer,
                                        uint nNumberOfBytesToWrite,
                                        ref uint lpNumberOfBytesWritten,
                                        IntPtr lpOverlapped
                                        );

        [DllImport("hid.dll", SetLastError = true)]
        internal static extern bool HidD_GetInputReport(
                                               IntPtr hidDeviceObject,
                                               byte[] lpReportBuffer,
                                               int reportBufferLength);
        #endregion
        #region  消息提示
        /*public static bool messagebox(string message)
        {
            if (MessageBox.Show(message, "提示", MessageBoxButtons.YesNo, MessageBoxIcon.None, MessageBoxDefaultButton.Button1, MessageBoxOptions.ServiceNotification) == DialogResult.No) return false;
            return true;
        }*/
        public static bool Messagebox(string message)
        {
            MessageBox box = new MessageBox("提示", message);
            box.TopMost = true;
            box.ShowDialog();
            if (box.DialogResult == DialogResult.OK)
            {
                box.Dispose();
                return true;
            }
            else
            {
                box.Dispose();
                return false;
            }
        }
        #endregion
        #region    使用write下下指令對外方法 command
        /// <summary>
        /// 使用write下下指令對外方法 command
        /// </summary>
        /// <param name="str">指令</param>
        /// <param name="lenght">指令长度</param>
        /// <param name="intPtr">通道</param>
        /// <returns></returns>
        public static bool command(string str, int lenght, IntPtr intPtr)
        {
            Thread.Sleep(20);
            //將指令16進制字符串轉為byte數組
            Byte[] FactoryReset = new byte[lenght];
            FactoryReset = ChangeUtil.GetByteArray(str, lenght);

            UInt32 numberofbyteWriten = 0;
            try
            {       //       通道     指令                指令長度               
                return WriteFile(intPtr, FactoryReset, (uint)FactoryReset.Length, ref numberofbyteWriten, IntPtr.Zero);
            }
            catch (IOException)
            {
                return false;
            }


        }
        #endregion
        #region    使用getreport下下指令對外方法 command
        /// <summary>
        /// 使用getreport下下指令對外方法 command
        /// </summary>
        /// <param name="str">指令</param>
        /// <param name="lenght">指令长度</param>
        /// <param name="intPtr">通道</param>
        /// <returns></returns>
        public static bool GetReportcommand(string str, int lenght, IntPtr intPtr)
        {
            Thread.Sleep(20);
            //將指令16進制字符串轉為byte數組
            Byte[] FactoryReset = new byte[lenght];
            FactoryReset = ChangeUtil.GetByteArray(str, lenght);
            try
            {       //       通道     指令                指令長度               
                if (HidD_GetInputReport(intPtr, FactoryReset, FactoryReset.Length))
                {
                    return true;
                }
                else
                {
                    return false;
                }
            }
            catch (IOException)
            {
                return false;
            }


        }
        #endregion
        #region    下指令后人工判断對外方法 commandThink

        /// <summary>
        /// 下指令后人工判断對外方法
        /// </summary>
        /// <param name="str">指令</param>
        /// <param name="lenght">指令长度</param>
        /// <param name="intPtr">句柄通道</param>
        /// <param name="message">消息框中信息</param>
        /// <param name="flag"></param>
        public static bool CommandThink(string str, int lenght, IntPtr intPtr, string message, string flag)
        {
            Thread.Sleep(20);
            //將指令16進制字符串轉為byte數組
            var FactoryReset = new byte[lenght];
            FactoryReset = ChangeUtil.GetByteArray(str, lenght);
            UInt32 numberofbyteWriten = 0;
            if (flag == "write")
            {
                try
                {       //       通道     指令                指令長度               
                    if (WriteFile(intPtr, FactoryReset, (uint)FactoryReset.Length, ref numberofbyteWriten, IntPtr.Zero))
                    {
                        Thread.Sleep(10);
                        return Messagebox(message);
                    }
                    else
                    {
                        return false;
                    }
                }
                catch (IOException)
                {
                    return false;
                }
            }
            else if (flag == "getReport")
            {
                try
                {       //       通道     指令                指令長度               
                    if (HidD_GetInputReport(intPtr, FactoryReset, FactoryReset.Length))
                    {
                        Thread.Sleep(200);
                        return Messagebox(message);
                    }
                    else
                    {
                        return false;
                    }
                }
                catch (IOException )
                {
                    return false;
                }
            }

            else
            {
                return false;
            }
        }
        #endregion
        #region    使用SetFeature下下指令對外方法

        /// <summary>
        /// 使用Set Feature下下指令對外方法 command
        /// </summary>
        /// <param name="str">指令</param>
        /// <param name="lenght">指令长度</param>
        /// <param name="intPtr">通道</param>
        /// <returns></returns>
        public static bool SetFeaturecommand(string str, int lenght, IntPtr intPtr)
        {
            Thread.Sleep(20);
            //將指令16進制字符串轉為byte數組
            Byte[] FactoryReset = new byte[lenght];
            FactoryReset = ChangeUtil.GetByteArray(str, lenght);
            try
            {       //       通道     指令                指令長度               
                if (HidD_SetFeature(intPtr, FactoryReset, FactoryReset.Length)) return true;
                else return false;
            }
            catch (IOException)
            {
                return false;
            }


        }

        #endregion
        #region 使用GetFeature下下指令對外方法

        /// <summary>
        /// 使用GetFeature下下指令對外方法
        /// </summary>
        /// <param name="str">指令</param>
        /// <param name="lenght">指令長度</param>
        /// <param name="intPtr">通道</param>
        /// <param name="ar">返回下標值</param>
        /// <param name="test"></param>
        /// <param name="value">返回显示结果</param>
        /// <returns></returns>
        public static bool GetFeaturecommand(string str, int lenght, IntPtr intPtr, string ar, string test, out string value)
        {
            Thread.Sleep(20);
            int[] arr = ChangeUtil.GetintArray(ar);
            //將指令16進制字符串轉為byte數組
            Byte[] FactoryReset = new byte[lenght];
            FactoryReset = ChangeUtil.GetByteArray(str, lenght);
            try
            {
                if (HidD_GetFeature(intPtr, FactoryReset, FactoryReset.Length))
                {
                    Thread.Sleep(500);
                    if (test == "TestFW")
                    {
                        string ver = "V";
                        foreach (int a in arr)
                        {
                            ver = ver + FactoryReset[a].ToString() + ".";
                        }
                        ver = ver.Substring(0, ver.Length - 1);
                        value = ver;
                        return true;
                    }

                    if (test == "DisplayData")
                    {
                        string ver = "";
                        foreach (int a in arr)
                        {
                            ver = ver + string.Format("{0:X2}", FactoryReset[a]) + " ";
                        }
                        ver = ver.Substring(0, ver.Length - 1);
                        value = ver;
                        return true;
                    }

                    if (test == "testCurrent")
                    {
                        string ver = "";
                        foreach (int a in arr)
                        {
                            ver = ver + string.Format("{0:X2}", FactoryReset[a]);
                        }
                        int m = ChangeUtil.Hex2Ten(ver);
                        if (arr.Length != 1)
                        {
                            m = m / 1000;
                        }
                        value = m.ToString() + "%";
                        return true;
                    }
                    value = "False";
                    return false;

                }
                else
                {
                    value = "False";
                    return false;
                }
            }
            catch (IOException)
            {
                value = "False";
                return false;
            }
        }
        #endregion
        #region   使用write下指令并獲取返回值對外方法 Writecommand

        /// <summary>
        /// 使用write下指令并獲取返回值對外方法 Writecommand
        /// </summary>
        /// <param name="str">指令</param>
        /// <param name="lenght">指令長度</param>
        /// <param name="intPtr">通道</param>
        /// <param name="ar">返回下標值</param>
        /// <param name="test"></param>
        /// <param name="value">返回显示结果</param>
        /// <returns></returns>
        public static bool Writecommand(string str, int lenght, IntPtr intPtr, string ar, string test, out string value)
        {
            int[] arr = ChangeUtil.GetintArray(ar);
            //將指令16進制字符串轉為byte數組
            Byte[] FactoryReset = new byte[lenght];
            Byte[] recdata = new Byte[lenght];
            if (intPtr == IntPtr.Zero)
            {
                value = "False";
                return false;
            }
            FileStream fs = new FileStream(new SafeFileHandle(intPtr, false), FileAccess.Read, recdata.Length, false);
            Thread.Sleep(20);
            {
                FactoryReset = ChangeUtil.GetByteArray(str, lenght);
                UInt32 numberofbyteWriten = 0;
                try
                {       //       通道     指令                指令長度
                    if (WriteFile(intPtr, FactoryReset, (uint)FactoryReset.Length, ref numberofbyteWriten, IntPtr.Zero))
                    {
                        Thread.Sleep(100);
                        fs.Read(recdata, 0, recdata.Length);

                        byte[] result = new byte[arr.Length];
                        if (test == "TestFW")
                        {
                            string ver = "V";
                            foreach (int a in arr)
                            {
                                ver = ver + recdata[a].ToString() + ".";
                            }
                            ver = ver.Substring(0, ver.Length - 1);
                            value = ver;
                            return true;
                        }

                        if (test == "DisplayData")
                        {
                            string ver = "";
                            foreach (int a in arr)
                            {
                                ver = ver + string.Format("{0:X2}", recdata[a]) + " ";
                            }
                            ver = ver.Substring(0, ver.Length - 1);
                            value = ver;
                            return true;
                        }

                        if (test == "testCurrent")
                        {
                            string ver = "";
                            foreach (int a in arr)
                            {
                                ver = ver + string.Format("{0:X2}", recdata[a]);
                            }
                            int m = ChangeUtil.Hex2Ten(ver);
                            if (arr.Length != 1)
                            {
                                m = m / 1000;
                            }
                            value = m.ToString() + "%";
                            return true;
                        }

                        if (test == "testVoltage")
                        {
                            string ver = "";
                            foreach (int a in arr)
                            {
                                ver = ver + string.Format("{0:X2}", recdata[a]);
                            }
                            value = ver;
                            return true;
                        }


                        value = "False";
                        return false;
                    }
                    else
                    {
                        value = "False";
                        return false;
                    }
                }
                catch (IOException)
                {
                    value = "False";
                    return false;
                }
            }
        }
        #endregion
        #region 使用getReport下指令并處理返回值对外方法  GetInputReport

        /// <summary>
        /// 使用getReport下指令并處理返回值对外方法 GetInputReport
        /// </summary>
        /// <param name="str">指令</param>
        /// <param name="lenght">指令長度</param>
        /// <param name="intPtr">通道</param>
        /// <param name="ar">返回下標值</param>
        /// <param name="test"></param>
        /// <param name="value">返回显示结果</param>
        /// <returns></returns>
        public static bool GetInputReport(string str, int lenght, IntPtr intPtr, string ar, string test, out string value)
        {

            int[] arr = ChangeUtil.GetintArray(ar);
            //將指令16進制字符串轉為byte數組
            Byte[] FactoryReset = new byte[lenght];
            FactoryReset = ChangeUtil.GetByteArray(str, lenght);
            try
            {
                if (HidD_GetInputReport(intPtr, FactoryReset, FactoryReset.Length))
                {
                    Thread.Sleep(500);
                    if (test == "TestFW")
                    {
                        string ver = "V";
                        foreach (int a in arr)
                        {
                            ver = ver + FactoryReset[a].ToString() + ".";
                        }
                        ver = ver.Substring(0, ver.Length - 1);
                        value = ver;
                        return true;
                    }

                    if (test == "DisplayData")
                    {
                        string ver = "";
                        foreach (int a in arr)
                        {
                            ver = ver + string.Format("{0:X2}", FactoryReset[a]) + " ";
                        }
                        ver = ver.Substring(0, ver.Length - 1);
                        value = ver;
                        return true;
                    }

                    if (test == "testCurrent")
                    {
                        string ver = "";
                        foreach (int a in arr)
                        {
                            ver = ver + string.Format("{0:X2}", FactoryReset[a]);
                        }
                        int m = ChangeUtil.Hex2Ten(ver);
                        if (arr.Length != 1)
                        {
                            m = m / 1000;
                        }
                        value = m.ToString() + "%";
                        return true;
                    }
                    value = "False";
                    return false;

                }
                else
                {
                    value = "False";
                    return false;
                }
            }
            catch (IOException)
            {
                value = "False";
                return false;
            }
        }
        #endregion
        #region   使用write下指令并獲取返回值對外方法 Writecommand
        /// <summary>
        /// 
        /// </summary>
        /// <param name="commands">指令</param>
        /// <param name="lenght">指令长度</param>
        /// <param name="readdata">指令返回值标识值</param>
        /// <param name="indexes">指令返回值索引</param>
        /// <param name="handle">句柄通道地址</param>
        /// <param name="intPtr">句柄通道指针</param>
        /// <param name="type">测试类型</param>
        /// <returns></returns>
        public static bool commands(string commands, int lenght, string readdata, string indexes, string handle, IntPtr intPtr, string type, out string value)
        {
            var _createFileHandle = CreateFile(handle,          //文件位置
                           GenericWrite | GenericRead,          //允许对设备进行读写访问
                           FileShareRead | FileShareWrite,    //允许对设备进行共享访问
                           IntPtr.Zero,                           //指向空指针（SECURITY_ATTRIBUTES定义文件的安全特性）
                           OpenExisting,                         //文件必须已存在
                           FileFlagOverlapped,                  //允许对文件进行重叠操作
                           IntPtr.Zero);                          //指向空指针（如果不为零，则指定一个文件句柄。新文件将从这个文件中复制扩展属性）
            var readflag = true;
            var values = "";
            var arrInputReport = new byte[lenght];
            var endflag = false;
            FileStream fs = new FileStream(new SafeFileHandle(_createFileHandle, false), FileAccess.Read | FileAccess.Write, lenght, true);
            try
            {
                #region 异步读取句柄返回值
                AsyncCallback AsyRead = (iResult =>
                {
                    byte[] arrBuff = (byte[])iResult.AsyncState;
                    try
                    {
                        fs.EndRead(iResult);
                    }
                    catch
                    {
                        fs.Close();
                    }
                    var arrReaddata = readdata.Split(' ');
                    for (var i = 0; i < arrReaddata.Length; i++)
                    {
                        if (arrBuff[i] != Convert.ToInt32(arrReaddata[i], 16))
                        {
                            readflag = false;
                            return;
                        }
                    }
                    var arrIndexes = indexes.Split(' ');
                    switch (type)
                    {
                        case "FW版本":
                            values = "V";
                            foreach (var a in arrIndexes)
                            {
                                values = values + arrBuff[Convert.ToInt32(a)].ToString() + ".";
                            }
                            values = values.Substring(0, values.Length - 1);
                            endflag = true;
                            break;
                        case "电压":
                            foreach (var a in arrIndexes)
                            {
                                values = values + string.Format("{0:X2}", arrBuff[Convert.ToInt32(a)]);
                            }
                            values = Convert.ToInt32(values, 16).ToString().Insert(1, "."); 

                            endflag = true;
                            break;
                        case "电量":
                            foreach (var a in arrIndexes)
                            {
                                values = values + string.Format("{0:X2}", arrBuff[Convert.ToInt32(a)]);
                            }
                            values = Convert.ToInt32(values, 16).ToString();
                            endflag = true;
                            break;
                        case "测试值":
                            foreach (var a in arrIndexes)
                            {
                                values = values + string.Format("{0:X2}", arrBuff[Convert.ToInt32(a)]) + " ";
                            }
                            values = values.Substring(0, values.Length - 1);
                            endflag = true;
                            break;
                    }
                });
                #endregion
                fs.BeginRead(arrInputReport, 0, lenght, AsyRead, arrInputReport);
                Thread.Sleep(20);

                #region 监听委托是否完成
                var nums = 0;
                while (true)
                {
                    command(commands, lenght, intPtr);
                    if (endflag)
                    {
                        if (readflag)
                        {
                            value = values;
                            return true;
                        }
                        value = "False";
                        return false;
                    }
                    Thread.Sleep(100);
                    nums++;
                    if (nums > 40)
                    {
                        value = "False";
                        return false;
                    }
                }
                #endregion
            }
            catch
            {
                value = "False";
                return false;
            }
            finally
            {
                fs.Close();
            }

        }
        #endregion

    }
}

```

### GetHandle

```c#
using System;
using System.Runtime.InteropServices;
using System.Text.RegularExpressions;

namespace HDT668_HID_CMD.Interface
{
    class GetHandle
    {
        #region 参数及引用区
        public static IntPtr[] headsethandle = new IntPtr[4];
        public static IntPtr[] donglehandle = new IntPtr[4];
        public static string[] headsetpath = new string[4];
        public static string[] donglepath = new string[4];
        [DllImport("kernel32.dll", SetLastError = true)]
        private static extern int CloseHandle(IntPtr hFile);
        [DllImport("hid.dll")]//获得GUID
        private static extern void HidD_GetHidGuid(ref Guid hidGuid);
        [DllImport("setupapi.dll", SetLastError = true)]//过滤设备，获取需要的设备
        private static extern IntPtr SetupDiGetClassDevs(ref Guid gClass, [MarshalAs(UnmanagedType.LPStr)] string strEnumerator, IntPtr hParent, Digcf nFlags);
        public enum Digcf  //3
        {
            DigcfDefault = 0x1,//返回与系统默认设备相关的设备
            DigcfPresent = 0x2,//返回当前存在的设备
            DigcfAllclasses = 0x4,//返回所有安装的设备
            DigcfProfile = 0x8,//只返回当前硬件配置文件的设备
            DigcfDeviceinterface = 0x10//返回所有支持的设备
        }
        protected struct SpDeviceInterfaceData
        {
            public int Size;
            public Guid InterfaceClassGuid;
            public int Flags;
            public int Reserved;
        }
        [DllImport("setupapi.dll", CharSet = CharSet.Auto, SetLastError = true)]
        private static extern Boolean SetupDiEnumDeviceInterfaces(IntPtr hDevInfo, uint devInfo, ref Guid interfaceClassGuid, uint memberIndex, ref SpDeviceInterfaceData deviceInterfaceData);
        [DllImport("setupapi.dll", SetLastError = true, CharSet = CharSet.Auto)]
        private static extern bool SetupDiGetDeviceInterfaceDetail(IntPtr deviceInfoSet, ref SpDeviceInterfaceData deviceInterfaceData, IntPtr deviceInterfaceDetailData,
                                                                 uint deviceInterfaceDetailDataSize, ref uint requiredSize, IntPtr deviceInfoData);
        [StructLayout(LayoutKind.Sequential, Pack = 2)]//2
        internal struct SpDeviceInterfaceDetailData
        {
            public int Size;
            [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 256)]
            public string DevicePath;
        }
        private const uint GenericRead = 0x80000000;
        private const uint GenericWrite = 0x40000000;
        private const uint FileShareWrite = 0x2;
        private const uint FileShareRead = 0x1;
        private const uint FileFlagOverlapped = 0x40000000;
        private const uint OpenExisting = 3;
        [DllImport("kernel32.dll", SetLastError = true)]
        private static extern IntPtr CreateFile([MarshalAs(UnmanagedType.LPStr)] string strName, uint nAccess, uint nShareMode, IntPtr lpSecurity, uint nCreationFlags, uint nAttributes, IntPtr lpTemplate);
        [DllImport("setupapi.dll", SetLastError = true)]
        private static extern bool SetupDiGetDeviceInterfaceDetail(IntPtr lpDeviceInfoSet, ref SpDeviceInterfaceData oInterfaceData, ref SpDeviceInterfaceDetailData oDetailData, uint nDeviceInterfaceDetailDataSize, ref uint nRequiredSize, IntPtr lpDeviceInfoData);
        [DllImport("setupapi.dll", SetLastError = true)]
        internal static extern IntPtr SetupDiDestroyDeviceInfoList(IntPtr deviceInfoSet);
        #endregion
        #region 获取装置路径
        private static bool GetHidDevicePath(string headsetPid, string headsetVid, string donglePid, string dongleVid)
        {
            Guid hidGuid = Guid.Empty;
            Regex xregPid = new Regex(donglePid.ToLower());
            Regex xregVid = new Regex(dongleVid.ToLower());
            Regex rXregPid = new Regex(headsetPid.ToLower());
            Regex rXregVid = new Regex(headsetVid.ToLower());
            Regex[] externAgs = { new Regex("col01"), new Regex("col02"), new Regex("col03"), new Regex("col04") };
            bool result = true;
            bool resultflag = false;
            IntPtr hidHandle = IntPtr.Zero;
            UInt32 deviceSerialNumber = 0;
            HidD_GetHidGuid(ref hidGuid);

            IntPtr hDevInfo = SetupDiGetClassDevs(ref hidGuid, null, IntPtr.Zero, Digcf.DigcfPresent | Digcf.DigcfDeviceinterface);//过滤设备，获取需要的设备
            try
            {
                SpDeviceInterfaceData deviceInterfaceData = new SpDeviceInterfaceData();
                deviceInterfaceData.Size = Marshal.SizeOf(deviceInterfaceData);
                while (result)
                {
                    result = SetupDiEnumDeviceInterfaces(hDevInfo, 0, ref hidGuid, deviceSerialNumber, ref deviceInterfaceData);//获取设备，true获取到                   
                    if (result)
                    {
                        uint nRequiredSize = 0;
                        SetupDiGetDeviceInterfaceDetail(hDevInfo, ref deviceInterfaceData, IntPtr.Zero, 0, ref nRequiredSize, IntPtr.Zero);

                        SpDeviceInterfaceDetailData detailData = new SpDeviceInterfaceDetailData();
                        detailData.Size = 5;	// hardcoded to 5! Sorry, but this works and trying more future proof versions by setting the size to the struct sizeof failed miserably. If you manage to sort it, mail me! Thx
                        result = SetupDiGetDeviceInterfaceDetail(hDevInfo, ref deviceInterfaceData, ref detailData, nRequiredSize, ref nRequiredSize, IntPtr.Zero);//获取接口的详细信息，必须调用两次，一次返回长度，二次获取数据

                        if (result)
                        {
                            if (detailData.DevicePath != null)
                            {
                                Match matchTxPid = xregPid.Match(detailData.DevicePath);
                                Match matchRxPid = rXregPid.Match(detailData.DevicePath);
                                Match matchTxvid = xregVid.Match(detailData.DevicePath);
                                Match matchRxvid = rXregVid.Match(detailData.DevicePath);
                                Match[] mathExternAgs = new Match[4];
                                for (int i = 0; i < 4; i++)
                                {
                                    mathExternAgs[i] = externAgs[i].Match(detailData.DevicePath);
                                }
                                if (matchTxPid.Success && matchTxvid.Success)
                                {
                                    for (int i = 0; i < 4; i++)
                                    {
                                        if (mathExternAgs[i].Success)
                                        {
                                            donglepath[i] = detailData.DevicePath;
                                            resultflag = true;
                                        }
                                    }
                                }
                                else if (matchRxPid.Success && matchRxvid.Success)
                                {
                                    for (int i = 0; i < 4; i++)
                                    {
                                        if (mathExternAgs[i].Success)
                                        {
                                            headsetpath[i] = detailData.DevicePath;
                                            resultflag = true;
                                        }
                                    }
                                }
                            }
                            deviceSerialNumber++;
                        }
                    }
                }
            }
            catch (Exception)
            {

                resultflag = false;
            }
            finally
            {
                SetupDiDestroyDeviceInfoList(hDevInfo);
            }
            return resultflag;
        }
        #endregion
        #region 对外方法
        public static bool Gethandle(string headsetPid, string headsetVid, string donglePid, string dongleVid)
        {
            bool flag;
            try
            {

                flag = GetHidDevicePath(headsetPid, headsetVid, donglePid, dongleVid);
                for (int i = 0; i < 4; i++)
                {
                    headsethandle[i] = GetHidDeviceHandle(headsetpath[i]);
                }
                for (int i = 0; i < 4; i++)
                {
                    donglehandle[i] = GetHidDeviceHandle(donglepath[i]);
                }

            }
            catch (Exception)
            {
                flag = false;
            }
            return flag;
        }
        /// <summary>
        /// 判断字符串中是否包含中文
        /// </summary>
        /// <param name="str">需要判断的字符串</param>
        /// <returns>判断结果</returns>
        public static bool HasChinese(string str)
        {
            return Regex.IsMatch(str, @"[\u4e00-\u9fa5]");
        }
        #endregion
        #region 将路径转换成句柄
        private static IntPtr GetHidDeviceHandle(string hidDevicePath)
        {
            IntPtr hidWriteHandle = IntPtr.Zero;
            if (!String.IsNullOrEmpty(hidDevicePath))
            {

                hidWriteHandle = CreateFile(hidDevicePath, GenericWrite | GenericRead, FileShareRead | FileShareWrite, IntPtr.Zero, OpenExisting, 0, IntPtr.Zero);
            }
            return hidWriteHandle;
        }
        #endregion
        #region 释放句柄
        public static void CloseHandle()
        {
            try
            {
                foreach (IntPtr handle in headsethandle)
                {

                    if (handle != IntPtr.Zero) CloseHandle(handle);
                }
                foreach (IntPtr handle in donglehandle)
                {

                    if (handle != IntPtr.Zero) CloseHandle(handle);
                }
                for (int i = 0; i < 4; i++)
                {
                    donglepath[i] = "";
                    headsetpath[i] = "";
                }
            }
            catch
            {
                // ignored
            }
        }

        #endregion
    }
}

```

### ChangeUtil

```c#
using System;
using System.Collections.Generic;

namespace HDT668_HID_CMD.Util
{
    public static class ChangeUtil
    {
        #region 移除數組后幾項 changes(string[] name, int num)
        public static string[] Changes(string[] name, int num)
        {
            string[] name1 = new string[7];
            for (int i = 0; i < name.Length - num; i++)
            {
                name1[i] = name[i];
            }

            return name1;
        }
        #endregion
        #region 將字符串數組合併為字符串 change(string[] name)
        public static string Change(string[] name)
        {
            string name1 = "";
            for (int i = 0; i < name.Length; i++)
            {

                name1 = name1 + name[i] + ",";
            }

            return name1;

        }
        #endregion
        #region 將16進制字符串轉換為byte數組 GetByteArray(string shex)
        public static byte[] GetByteArray(string shex, int lenght)
        {
            string[] ssArray = shex.Split(' ');
            List<byte> bytList = new List<byte>();
            int i = 0;
            foreach (var s in ssArray)
            {   //将十六进制的字符串转换成数值  
                bytList.Add(Convert.ToByte(s, 16));
                i++;
            }
            for (int j = i; j < lenght; j++)
            {
                bytList.Add(Convert.ToByte("0"));
            }
            return bytList.ToArray();
        }
        #endregion
        #region 將byte數組轉換為16進制字符串 byteToHexStr(byte[] bytes)
        public static string ByteToHexStr(byte[] bytes)
        {
            string returnStr = "";
            if (bytes != null)
            {
                foreach (var t in bytes)
                {
                    returnStr += t.ToString("X2");
                }
            }
            return returnStr;
        }

        #endregion
        #region 將空格隔開的字符串轉換為int 數組
        public static int[] GetintArray(string ar)
        {
            string[] strArray = ar.Split(' ');
            int[] intArray;
            intArray = Array.ConvertAll(strArray, s => int.Parse(s));
            return intArray;
        }
        #endregion
        #region 將16進制字符串轉換為 int 
        public static int Hex2Ten(string hex)
        {
            int ten = 0;
            for (int i = 0, j = hex.Length - 1; i < hex.Length; i++)
            {
                ten += HexChar2Value(hex.Substring(i, 1)) * ((int)Math.Pow(16, j));
                j--;
            }
            return ten;
        }

        private static int HexChar2Value(string hexChar)
        {
            switch (hexChar)
            {
                case "0":
                case "1":
                case "2":
                case "3":
                case "4":
                case "5":
                case "6":
                case "7":
                case "8":
                case "9":
                    return Convert.ToInt32(hexChar);
                case "a":
                case "A":
                    return 10;
                case "b":
                case "B":
                    return 11;
                case "c":
                case "C":
                    return 12;
                case "d":
                case "D":
                    return 13;
                case "e":
                case "E":
                    return 14;
                case "f":
                case "F":
                    return 15;
                default:
                    return 0;
            }
        }
        #endregion
        #region 将指令集转为指令数组
        public static string[] Clist(string str)
        {
            return str.Split('-');
        }
        #endregion
        #region 将linkedlist指定节点转为数组
        public static string[] Changelinkedlist(LinkedList<string[]> clist, int i)
        {
            string[] arr = new string[2];
            int m = 0;
            foreach (string[] item in clist)
            {
                if (m != i) { m++; continue; }
                arr = (string[])item.Clone();
            }
            return arr;
        }
        #endregion
    }
}

```

### CHID

```c#
using System.Runtime.InteropServices;

namespace HDT668_HID_CMD.Util
{
    class CHID
    {
        [DllImport("HID_DLL.dll", CallingConvention = CallingConvention.StdCall)]
        public static extern int WriteDataToHIDDeviceB([MarshalAs(UnmanagedType.LPStr)] [In] string vid, [MarshalAs(UnmanagedType.LPStr)] [In] string pid, int index, byte[] data);

        [DllImport("HID_DLL.dll", CallingConvention = CallingConvention.StdCall)]
        public static extern int WriteReadDataFromHIDDeviceB([MarshalAs(UnmanagedType.LPStr)] [In] string vid, [MarshalAs(UnmanagedType.LPStr)] [In] string pid, int index, byte[] outData, byte[] inData, out int outLength);

        [DllImport("HID_DLL.dll", CallingConvention = CallingConvention.StdCall)]
        public static extern int ReadDataFromHIDDeviceB([MarshalAs(UnmanagedType.LPStr)] [In] string vid, [MarshalAs(UnmanagedType.LPStr)] [In] string pid, int index, byte[] outData, int timeout, out int outLength);
    }
}

```

### ClassControl

```c#
using System;
using System.Drawing;
using System.Runtime.InteropServices;
using System.Text;
using System.Windows.Forms;

namespace HDT668_HID_CMD.Util
{
    class ClassControl
    {
        private Control _ctl;

        public Control setContol
        {
            get { return _ctl; }
            set { _ctl = value; }
        }
        public void SetTag(Control cons)
        {
            foreach (Control con in cons.Controls)
            {
                con.Tag = con.Width + ":" + con.Height + ":" + con.Left + ":" + con.Top + ":" + con.Font.Size;
                if (con.Controls.Count > 0)
                    SetTag(con);
            }
        }
        public void SetControls(float newx, float newy, Control cons)
        {
            try
            {
                foreach (Control con in cons.Controls)
                {

                    string[] mytag = con.Tag.ToString().Split(new char[] { ':' });
                    float a = Convert.ToSingle(mytag[0]) * newx;
                    con.Width = (int)a;
                    a = Convert.ToSingle(mytag[1]) * newy;
                    con.Height = (int)(a);
                    a = Convert.ToSingle(mytag[2]) * newx;
                    con.Left = (int)(a);
                    a = Convert.ToSingle(mytag[3]) * newy;
                    con.Top = (int)(a);
                    Single currentSize = Convert.ToSingle(mytag[4]) * Math.Min(newx, newy);
                    con.Font = new Font(con.Font.Name, currentSize, con.Font.Style, con.Font.Unit);
                    if (con.Controls.Count > 0)
                    {
                        SetControls(newx, newy, con);
                    }
                }
            }
            catch
            {
                // ignored
            }
        }
        private delegate void Showdisplay(Control ctrl, string txt);
        public void ChangeDisplayVal(Control ctrl, string txt)
        {
            if (ctrl.InvokeRequired)
            {
                ctrl.Invoke(new Showdisplay(ChangeDisplayVal), new object[] { ctrl, txt });
            }
            else
            {
                ctrl.Text = txt;

            }
        }
    }
    class ApIcls
    {
        [DllImport("kernel32")]
        private static extern Int64 WritePrivateProfileString(String section,
                                                               String key,
                                                               String val,
                                                               String filePath);
        //讀ini文件-String
        [DllImport("kernel32")]
        private static extern Int64 GetPrivateProfileString(String section,
                                                            String key,
                                                            String def,
                                                            StringBuilder retVal,
                                                            Int32 size,
                                                            String filePath);
        //讀ini文件-Int32
        [DllImport("kernel32")]
        private static extern UInt32 GetPrivateProfileInt(String section,
                                                          String key,
                                                          Int32 returnValue,
                                                          String filePath);
        [DllImport("User32.dll")]
        private static extern Int32 SendMessage(IntPtr hWnd,
                                                int Msg,
                                                int wParam,
                                                int lParam);
        public static string RdiniStr(string fileName, string section, string key, string val)
        {
            StringBuilder sb = new StringBuilder(100);
            try
            {
                GetPrivateProfileString(section, key, "", sb, 100, fileName);
                return sb.ToString();
            }
            catch (Exception)
            {
                return "";
            }

        }
        public static int RdiniInt(string fileName, string section, string key, string val)
        {
            int irtn = 0;
            try
            {
                GetPrivateProfileInt(section, key, irtn, fileName);

            }
            catch (Exception ex)
            {
                throw new Exception(ex.Message);
            }
            return irtn;
        }
        public static void WriteIniFile(string fileName, string section, string key, string val)
        {
            Int64 iRtn = 0;
            try
            {
                iRtn = WritePrivateProfileString(section, key, val, fileName);
            }
            catch (Exception ex)
            {
                throw new SystemException("write ini file error :\r\n  exception is " + ex.Message);
            }
        }
    }
}

```

### Heater

```c#
using System;
using System.Runtime.InteropServices;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading;

namespace HDT668_HID_CMD.Util
{
    /// <summary>
    /// 委託類  耳機線插入時間
    /// </summary>
    public class Heater
    {
        public delegate void BoilHandler();
        public event BoilHandler BoilEvent;
        public event BoilHandler BoilEvent1;
        bool _bzw;
        bool _bbb;
        Thread _t1;
        Thread _t2;
        public void Zxlwd1()
        {
            _t1 = new Thread(BoilWater1);
            _t1.Start();

        }
        public void Zxlww1()
        {
            _t1.Abort();
        }
        public void Zxlwd2()
        {
            _t2 = new Thread(BoilWater2);
            _t2.Start();

        }
        public void Zxlww2()
        {
            _t2.Abort();
        }
        public bool Checkt1()
        {
            try
            {
                if (_t1.IsAlive) return true;
            }
            catch (Exception)
            {
                // ignored
            }

            return false;
        }
        public bool Checkt2()
        {
            try
            {
                if (_t2.IsAlive) return true;
            }
            catch (Exception)
            {
                // ignored
            }

            return false;
        }
        public void BoilWater1()
        {
            while (true)
            {
                GetHidDevicePath1();
                if (_flag)
                {
                    if (_bzw)
                    {
                        _bbb = false;
                    }
                    else
                    {
                        _bbb = true;
                    }
                    _bzw = true;
                }
                else
                {
                    if (_bzw)
                    {
                        _bbb = true;
                    }
                    else
                    {
                        _bbb = false;
                    }
                    _bzw = false;
                }
                if (_bbb)
                {
                    if (_bzw)
                    {
                        if (BoilEvent != null)
                        {
                            BoilEvent();
                        }
                    }
                    else
                    {
                        if (BoilEvent1 != null)
                        {
                            BoilEvent1();
                        }
                    }
                }
                Thread.Sleep(150);
            }
        }
        public void BoilWater2()
        {
            while (true)
            {
                GetHidDevicePath2();
                if (_flag)
                {
                    if (_bzw)
                    {
                        _bbb = false;
                    }
                    else
                    {
                        _bbb = true;
                    }
                    _bzw = true;
                }
                else
                {
                    if (_bzw)
                    {
                        _bbb = true;
                    }
                    else
                    {
                        _bbb = false;
                    }
                    _bzw = false;
                }
                if (_bbb)
                {
                    if (_bzw)
                    {
                        if (BoilEvent != null)
                        {
                            BoilEvent();
                        }
                    }
                    else
                    {
                        if (BoilEvent1 != null)
                        {
                            BoilEvent1();
                        }
                    }
                }
                Thread.Sleep(150);
            }
        }
        [DllImport("hid.dll")]
        private static extern void HidD_GetHidGuid(ref Guid hidGuid);
        [DllImport("setupapi.dll", SetLastError = true)]
        private static extern IntPtr SetupDiGetClassDevs(ref Guid gClass, [MarshalAs(UnmanagedType.LPStr)] string strEnumerator, IntPtr hParent, Digcf nFlags);
        [Flags]
        public enum Digcf
        {
            DigcfDefault = 0x1,
            DigcfPresent = 0x2,
            DigcfAllclasses = 0x4,
            DigcfProfile = 0x8,
            DigcfDeviceinterface = 0x10
        }
        [DllImport("setupapi.dll", CharSet = CharSet.Auto, SetLastError = true)]
        private static extern Boolean SetupDiEnumDeviceInterfaces(IntPtr hDevInfo, uint devInfo, ref Guid interfaceClassGuid, uint memberIndex, ref SpDeviceInterfaceData deviceInterfaceData);
        [DllImport("setupapi.dll", SetLastError = true, CharSet = CharSet.Auto)]
        private static extern bool SetupDiGetDeviceInterfaceDetail(IntPtr deviceInfoSet, ref SpDeviceInterfaceData deviceInterfaceData, IntPtr deviceInterfaceDetailData,
           uint deviceInterfaceDetailDataSize, ref uint requiredSize, IntPtr deviceInfoData);

        [DllImport("setupapi.dll", SetLastError = true)]
        private static extern bool SetupDiGetDeviceInterfaceDetail(IntPtr lpDeviceInfoSet, ref SpDeviceInterfaceData oInterfaceData, ref SpDeviceInterfaceDetailData oDetailData, uint nDeviceInterfaceDetailDataSize, ref uint nRequiredSize, IntPtr lpDeviceInfoData);
        protected struct SpDeviceInterfaceData
        {
            public int Size;
            public Guid InterfaceClassGuid;
            public int Flags;
            public int Reserved;
        }
        internal struct SpDeviceInterfaceDetailData
        {
            public int Size;
            [MarshalAs(UnmanagedType.ByValTStr, SizeConst = 256)]
            public string DevicePath;
        }
        bool _flag = false;
        private void GetHidDevicePath1()
        {
            GetPrivateProfileString("CONFIG", "HeadsetVID", "", temp, 500, ".\\CONFIG.INI");
            _headsetVid = temp.ToString();
            GetPrivateProfileString("CONFIG", "HeadsetPID", "", temp, 500, ".\\CONFIG.INI");
            _headsetPid = temp.ToString();
            Guid hidGuid = Guid.Empty;
            Regex regPidMono = new Regex(_headsetPid);
            Regex regVid = new Regex(_headsetVid);
            bool result = true;
            uint deviceSerialNumber = 0;
            HidD_GetHidGuid(ref hidGuid);
            IntPtr hDevInfo = SetupDiGetClassDevs(ref hidGuid, null, IntPtr.Zero, Digcf.DigcfPresent | Digcf.DigcfDeviceinterface);
            try
            {
                SpDeviceInterfaceData deviceInterfaceData = new SpDeviceInterfaceData();
                deviceInterfaceData.Size = Marshal.SizeOf(deviceInterfaceData);
                while (result)
                {
                    result = SetupDiEnumDeviceInterfaces(hDevInfo, 0, ref hidGuid, deviceSerialNumber, ref deviceInterfaceData);
                    if (result)
                    {
                        uint nRequiredSize = 0;
                        SetupDiGetDeviceInterfaceDetail(hDevInfo, ref deviceInterfaceData, IntPtr.Zero, 0, ref nRequiredSize, IntPtr.Zero);

                        SpDeviceInterfaceDetailData detailData = new SpDeviceInterfaceDetailData();
                        detailData.Size = 5;
                        result = SetupDiGetDeviceInterfaceDetail(hDevInfo, ref deviceInterfaceData, ref detailData, nRequiredSize, ref nRequiredSize, IntPtr.Zero);
                        if (result)
                        {
                            deviceSerialNumber++;
                            if (detailData.DevicePath != null)
                            {
                                Match matchPidMono = regPidMono.Match(detailData.DevicePath);
                                Match matchVid = regVid.Match(detailData.DevicePath);
                                if (matchPidMono.Success && matchVid.Success)
                                {
                                    _flag = true;
                                    break;
                                }
                                else
                                {
                                    _flag = false;
                                }
                            }
                        }
                    }

                }
            }
            catch (Exception)
            {
                _flag = false;
            }
        }
        [DllImport("kernel32.dll")]
        private static extern int GetPrivateProfileString(string section, string key, string def, StringBuilder retVal, int size, string iniPath);
        string _dongleVid = "";
        string _donglePid = "";
        string _headsetVid = "";
        string _headsetPid = "";
        StringBuilder temp = new StringBuilder(500);
        private void GetHidDevicePath2()
        {
            GetPrivateProfileString("CONFIG", "DongleVID", "", temp, 500, ".\\CONFIG.INI");
            _dongleVid = temp.ToString();
            GetPrivateProfileString("CONFIG", "DonglePID", "", temp, 500, ".\\CONFIG.INI");
            _donglePid = temp.ToString();
            Guid hidGuid = Guid.Empty;
            Regex regPidMono = new Regex(_donglePid);
            Regex regVid = new Regex(_dongleVid);
            bool result = true;
            uint deviceSerialNumber = 0;
            HidD_GetHidGuid(ref hidGuid);
            IntPtr hDevInfo = SetupDiGetClassDevs(ref hidGuid, null, IntPtr.Zero, Digcf.DigcfPresent | Digcf.DigcfDeviceinterface);
            try
            {
                SpDeviceInterfaceData deviceInterfaceData = new SpDeviceInterfaceData();
                deviceInterfaceData.Size = Marshal.SizeOf(deviceInterfaceData);
                while (result)
                {
                    result = SetupDiEnumDeviceInterfaces(hDevInfo, 0, ref hidGuid, deviceSerialNumber, ref deviceInterfaceData);
                    if (result)
                    {
                        uint nRequiredSize = 0;
                        SetupDiGetDeviceInterfaceDetail(hDevInfo, ref deviceInterfaceData, IntPtr.Zero, 0, ref nRequiredSize, IntPtr.Zero);

                        SpDeviceInterfaceDetailData detailData = new SpDeviceInterfaceDetailData();
                        detailData.Size = 5;
                        result = SetupDiGetDeviceInterfaceDetail(hDevInfo, ref deviceInterfaceData, ref detailData, nRequiredSize, ref nRequiredSize, IntPtr.Zero);
                        if (result)
                        {
                            deviceSerialNumber++;
                            if (detailData.DevicePath != null)
                            {
                                Match matchPidMono = regPidMono.Match(detailData.DevicePath);
                                Match matchVid = regVid.Match(detailData.DevicePath);
                                if (matchPidMono.Success && matchVid.Success)
                                {
                                    _flag = true;
                                    break;
                                }
                                else
                                {
                                    _flag = false;
                                }
                            }
                        }
                    }

                }
            }
            catch (Exception)
            {
                _flag = false;
            }
        }
    }
}

```



### Program

```c#
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading;
using HDT668_HID_CMD.Interface;
using static HDT668_HID_CMD.Interface.GetHandle;
namespace HDT668_HID_CMD
{
    class Program
    {

        static void Main(string[] args)
        {
            Loadmessage();
            while (true)
            {
                var parameter = Console.ReadLine();
                if (parameter.Contains("message")) //这个是固定的(主程序需要的弹窗)
                {
                    string message = parameter.Replace("message", "");
                    Console.WriteLine(Command.Messagebox(messages[message]));
                    continue;
                }
                Gethandle(args[0], args[1], args[2], args[3]);
                switch (parameter)
                {
                    case "BootUp":
                        Console.WriteLine(BootUp()); break; //开机
                    case "ShutDown":
                        Console.WriteLine(ShutDown()); break; //关机
                    case "GetTxAvneraFW":
                        Console.WriteLine(GetTxAvneraFW(args[2], args[3])); break;//获取获取dongleAvneraFW
                    case "GetTxMCUFW":
                        Console.WriteLine(GetTxMCUFW()); break;//获取获取dongleMCUFW
                    case "GetRxAvneraFW":
                        Console.WriteLine(GetRxAvneraFW()); break;//获取耳机AvneraFW
                    case "TestRxRedLED":
                        Console.WriteLine(TestRxRedLED()); break;//打开耳机红色LED
                    case "TestRxGreenLED":
                        Console.WriteLine(TestRxGreenLED()); break;//打开耳机绿色LED
                    case "TxButtonTest":
                        Console.WriteLine(TxButtonTest()); break;//dongle按键测试
                    case "GetTxPairId":
                        Console.WriteLine(GetTxPairId()); break;//获取dongle配对ID
                    case "Pair":
                        Console.WriteLine(Pair()); break;//配对
                    case "GetRxPairId":
                        Console.WriteLine(GetRxPairId()); break;//获取耳机配对ID
                    case "GetRXGaugeICFW":
                        Console.WriteLine(GetRXGaugeICFW()); break;  //获取耳机GaugeICFW版本 
                    case "GetRXChemID":
                        Console.WriteLine(GetRXChemID()); break;  //获取耳机ChemID
                    case "GetPower":
                        Console.WriteLine(GetPower()); break;  //获取电量
                    case "GetVoltage":
                        Console.WriteLine(GetVoltage()); break;  //获取电压
                    case "GoldImage":
                        Console.WriteLine(GoldImage()); break;  //GoldImage 版本  
                    case "MuteButtenTest":
                        Console.WriteLine(MuteButtenTest()); break;  //静音按键测试  
                    case "OpenSideTone":
                        Console.WriteLine(OpenSideTone()); break;  //打开SideTone模式
                    case "ResetGaugeIC":
                        Console.WriteLine(ResetGaugeIC()); break;  //ResetGaugeIC
                    case "UpdateAVMode":
                        Console.WriteLine(UpdateAVMode()); break;  //切换dongleAV模式
                    case "End": return;
                    default: Console.WriteLine(false); break;
                }
            }


            // Console.ReadKey();
        }
        public static IntPtr intPtr = IntPtr.Zero;
        private static string value = "";
        private static string PairID = "";
        private static string message = "";
        private static Dictionary<string, string> messages = new Dictionary<string, string>();
        /// <summary>
        /// 自定义提示框
        /// </summary>
        private static void Loadmessage()
        {
            MessageBox box = new MessageBox();
            box.Show();
            box.TopMost = true;
            box.Close();
            box.Dispose();
            try
            {
                using (FileStream fs = new FileStream(".//Console/message.txt", FileMode.Open, FileAccess.Read))
                {
                    using (StreamReader sr = new StreamReader(fs, Encoding.GetEncoding("utf-8")))
                    {
                        while ((message = sr.ReadLine()) != "")
                        {
                            string[] arr = message.Split(',');
                            messages.Add(arr[1], arr[0]);
                        }
                    }
                }
            }
            catch
            {
                using (FileStream fs = new FileStream(".//message.txt", FileMode.Open, FileAccess.Read))
                {
                    using (StreamReader sr = new StreamReader(fs, Encoding.GetEncoding("utf-8")))
                    {
                        while ((message = sr.ReadLine()) != "")
                        {
                            string[] arr = message.Split(',');
                            messages.Add(arr[1], arr[0]);
                        }
                    }
                }
            }

        }
        private static bool BootUp()
        {

            string command = "07 88 07 01";
            return Command.command(command, 20, headsethandle[0]);
        }
        private static bool ShutDown()
        {
            string command = "07 88 07 00";
            return Command.command(command, 20, headsethandle[0]);
        }
        private static string GetTxAvneraFW(string pid, string vid)
        {
            string command = "06 00 02 00 9A 00 00 68 4A 8E 0A 00 00 00 BB 11";
            string returnvalue = "0B 00 BB 11";
            string indexs = "4 5 6 7 8";
            if (Command.commands(command, 62, returnvalue, indexs, donglepath[2], donglehandle[2], "FW版本", out value))
                if (Command.GetReportcommand(command, 62, donglehandle[2]))
                {
                    if (value.Equals("V1.0.0.0.0"))
                    {
                        CloseHandle();
                        Gethandle("", "", pid, vid);
                        GetTxAvneraFW(pid, vid);
                    }
                    return value;
                }
            return "False";

        }
        private static string GetTxMCUFW()
        {
            string command = "01 00 0D 00 03 01 00 23 2D B3";
            string Indexes = "10 14 18 22";
            string test = "TestFW";
            if (!Command.command(command, 62, donglehandle[0])) return "False";
            Thread.Sleep(20);
            Command.GetInputReport(command, 62, donglehandle[0], Indexes, test, out value);
            return value;

        }
        private static string GetRxAvneraFW()
        {
            string command = "07 88 04";
            string indexs = "3 4 5 6 7";
            string returnvalue = "07 88 04 03";
            Command.commands(command, 20, returnvalue, indexs, headsetpath[0], headsethandle[0], "FW版本", out value);
            return value;
        }
        private static bool TestRxRedLED()
        {
            string command = "07 88 03 01 00";
            Command.command(command, 20, headsethandle[0]);
            Thread.Sleep(20);
            command = "07 88 03 02 01";
            if (Command.CommandThink(command, 20, headsethandle[0], "头戴LED是否变为红色", "write"))
            {
                command = "07 88 03 02 00";
                return Command.command(command, 20, headsethandle[0]);
            }
            return false;
        }
        private static bool TestRxGreenLED()
        {
            string command = "07 88 03 01 01";
            if (Command.CommandThink(command, 20, headsethandle[0], "头戴LED是否变为绿色", "write"))
            {
                command = "07 88 03 01 00";
                return Command.command(command, 20, headsethandle[0]);
            }
            return false;
        }
        private static bool TxButtonTest()
        {
            string command = "06 00 02 00 9A 00 00 68 4A 8E 0A 00 00 00 BB 16 01";
            return Command.CommandThink(command, 62, donglehandle[2], "dongle是否按键后亮/灭", "write");

        }
        private static string GetTxPairId()
        {
            string command = "01 00 0D 00 A0 01 00 68 4A 8E 10 00 00 00 01 00 00 00";
            if (Command.command(command, 62, donglehandle[0]))
            {
                for (int i = 0; i < 20; i++)
                {
                    CloseHandle();
                    Gethandle("1719", "0951", "1717", "0951");
                    if (donglehandle[0] != IntPtr.Zero) break;
                    Thread.Sleep(500);
                }
                command = "FF 0A 00 FD 04 00 00 05 81 D4 C0 04";
                if (Command.SetFeaturecommand(command, 64, donglehandle[3]))
                {
                    Thread.Sleep(50);
                    string Indexes = "11 12 13 14";
                    if (Command.GetFeaturecommand(command, 64, donglehandle[3], Indexes, "DisplayData", out value))
                    {
                        if (value == "78 56 34 12" || value == "00 00 00 00")
                        {
                            return "False" + value;
                        }
                        else
                        {
                            PairID = value;
                            command = "FF 05 00 39";
                            if (Command.SetFeaturecommand(command, 64, donglehandle[3]))
                                return value;
                        }
                    }
                }
            }
            return "False" + value;
        }
        private static string Pair()
        {
            Gethandle("1719", "0951", "1717", "0951");
            string command = "FF 0A 00 FD 04 00 00 05 81 D4 C0 04";
            if (Command.SetFeaturecommand(command, 64, donglehandle[3]))
            {
                Thread.Sleep(50);
                string Indexes = "11 12 13 14";
                if (Command.GetFeaturecommand(command, 64, donglehandle[3], Indexes, "DisplayData", out value))
                {
                    if (value == "78 56 34 12" || value == "00 00 00 00")
                    {
                        return "False" + value;

                    }
                    else
                    {
                        command = "07 88 01" + " " + value;
                        if (Command.command(command, 20, headsethandle[0])) return value;
                    }


                }
            }
            return "False" + value;
        }
        private static string GetRxPairId()
        {
            string command = "FF 0C 00 FD 04 00 00 05 81 0D B1 04";
            string Indexes = "11 12 13 14";
            if (Command.SetFeaturecommand(command, 64, headsethandle[1]))
            {
                Thread.Sleep(20);
                if (Command.GetFeaturecommand(command, 64, headsethandle[1], Indexes, "DisplayData", out value))
                {
                    PairID = value;
                    Gethandle("1719", "0951", "1717", "0951");
                    command = "FF 0A 00 FD 04 00 00 05 81 D4 C0 04";
                    if (Command.SetFeaturecommand(command, 64, donglehandle[3]))
                    {
                        Thread.Sleep(50);
                        Indexes = "11 12 13 14";
                        if (Command.GetFeaturecommand(command, 64, donglehandle[3], Indexes, "DisplayData", out value))
                        {
                            if (value == "78 56 34 12" || value == "00 00 00 00")
                            {
                                return "False" + value;
                            }
                            else
                            {
                                if (PairID == value)
                                {
                                    return value;
                                }
                            }

                        }
                    }
                }
            }
            return "False" + value;
        }
        private static string GetRXGaugeICFW()
        {
            string command = "07 88 08 04 04 AA 3E 02";
            string indexs = "5 6";
            string returnvalue = "07 88 08 15 61";
            Command.commands(command, 20, returnvalue, indexs, headsetpath[0], headsethandle[0], "FW版本", out value);

            if (value == "False") return value;
            var strb = new System.Text.StringBuilder(value);
            strb.Insert(2, '0');
            strb.Insert(2, '.');
            value = strb.ToString();
            return value;
        }
        private static string GetRXChemID()
        {
            string command = "07 88 08 02 04 AA 3E 06";
            string indexs = "3 4";
            string returnvalue = "07 88 08";
            Command.commands(command, 20, returnvalue, indexs, headsetpath[0], headsethandle[0], "测试值", out value);

            if (value == "False") return value;
            string[] arr = value.Split(' ');
            value = "";
            value = arr[1] + arr[0];
            return value;
        }
        private static string GetPower()
        {
            string command = "06 00 02 00 9A 00 00 68 4A 8E 0A 00 00 00 BB 02";
            string index = "0B 00 BB 02 00";
            Command.commands(command, 62, index, "7", donglepath[2], donglehandle[2], "电量", out value);
            if (value == "False") return value;
            if (Command.GetReportcommand(command, 62, donglehandle[2])) return value;
            return "False";

        }
        private static string GetVoltage()
        {
            string command = "06 00 02 00 9A 00 00 68 4A 8E 0A 00 00 00 BB 02";
            string index = "0B 00 BB 02 00";
            Command.commands(command, 62, index, "5 6", donglepath[2], donglehandle[2], "电压", out value);
            if (value == "False") return value;
            if (Command.GetReportcommand(command, 62, donglehandle[2])) return value;
            return "False";
        }
        private static string GoldImage()
        {
            string command = "07 88 08 01 04 AA 3E 70";
            string index = "07 88 08";
            Command.commands(command, 20, index, "3", headsetpath[0], headsethandle[0], "FW版本", out value);
            return value;
        }
        private static bool MuteButtenTest()
        {
            string command = "07 88 02 01";
            if (Command.command(command, 20, headsethandle[0]))
            {
                Thread.Sleep(20);
                string[] a = { "170" };
                string[] b = { "3" };
                ButtonTest bt = new ButtonTest();
                return bt.Buttontest(a, b, "请按mute键", headsetpath[0]);
            }
            return false;
        }

        private static bool OpenSideTone()
        {
            Thread.Sleep(40);
            string command = "07 88 05 01";
            if (Command.CommandThink(command, 20, headsethandle[0], "对麦克风说话,耳机中是否有回音", "write"))
            {
                command = "07 88 05 00";
                return Command.command(command, 20, headsethandle[0]);
            }
            else
            {
                command = "07 88 05 00";
                Command.command(command, 20, headsethandle[0]);
                return false;
            }


        }
        private static bool ResetGaugeIC()
        {
            string command = "07 88 08 00 04 AA 3E 12";
            return Command.command(command, 20, headsethandle[0]);
        }

        private static bool UpdateAVMode()
        {
            string command = "01 00 0D 00 A0 01 00 68 4A 8E 10 00 00 00 01 00 00 00";
            if (!Command.command(command, 62, donglehandle[3])) return false;
            for (int i = 0; i < 20; i++)
            {
                CloseHandle();
                Gethandle("1719", "0951", "1717", "0951");
                if (donglehandle[0] != IntPtr.Zero) return true;
                Thread.Sleep(500);
            }
            return false;
        }

        private static bool TestTxRedLED(IntPtr handle)
        {
            string command = "07 88 03 01 00";
            Command.command(command, 20, handle);
            Thread.Sleep(20);
            command = "07 88 03 02 01";
            if (Command.CommandThink(command, 20, handle, "头戴LED是否变为红色", "write"))
            {
                command = "07 88 03 02 00";
                return Command.command(command, 20, handle);
            }
            return false;
        }
    }

}

```

