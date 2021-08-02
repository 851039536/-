# Web Service接口

> 系统使用web service为测试程序提供调用接口.

##  过站相关接口

### checkEmpNo

> 功能说明: 檢查員工是否有權限在此工站作業

**方法定义:** 

`public string checkEmpNo(string sEMP, string sStation)`

**参数定义:**

```c#
/// <param name="sEMP">人員工號</param>
/// <param name="sStation">站點編號</param>
/// <returns>成功訊息：【OK;】，失敗訊息【NG; 錯誤訊息】開頭</returns>
```

 

###  checkSN_Route

> 功能说明: 檢測序號的有效性及是否允許在此工站測試 

**方法定义:** 

```c#
public string checkSN_Route(string sSN, string sStation)
```

**参数定义:**

```c#
/// <param name="sSN">在製品序號</param>
/// <param name="sStation">站點編號</param>
/// <returns>成功訊息：【OK;】，失敗訊息【NG; 錯誤訊息】開頭</returns>
```

 

###  sendTestResult

> 功能说明: 上傳測試結果到MES,OK:進入途程中的下一站，NG：送維修；包括1次Fail需多次測試PASS方可過站及固定次數Fail必須進維修的邏輯

**方法定义:** 

```c#
public string sendTestResult(string sEMP, string sSN, string sStation, string sResult)
```

**参数定义:**

```c#
/// <param name="sEMP">人員工號</param>
/// <param name="sSN">在製品序號</param>
/// <param name="sStation">站點編號</param>
/// <param name="sResult">測試結果（測試pass傳值:OK / 測試NG傳值:NG;GA001;GA002 ；GA001 GA002為不良現象代碼）</param>
/// <returns>成功訊息：【OK;】，失敗訊息【NG; 錯誤訊息】開頭</returns>
```

 

###  sendTestData

> 功能说明: 上傳測試項目資料，不含過站(MES)

**方法定义:** 

```c#
public string sendTestData(string sEMP, string sSN, string sStation, string sTestResult)
```

**参数定义:**

```c#
/// <param name="sEMP">人員工號</param>
/// <param name="sSN">在製品序號</param>
/// <param name="sStation">站點編號</param>
/// <param name="sTestResult">測試項目明細，傳值如下：測試項目1:測試值; 測試項目2:測試值;…</param>
/// <returns>成功訊息：【OK;】，失敗訊息【NG; 錯誤訊息】開頭</returns>
```

 

### Bluetooth_ID

> 功能说明: 綁定產品序號與藍芽地址的對應關係

**方法定义:** 

```c#
public string Bluetooth_ID(string sSN, string sLnkData)
```

**参数定义:**

```c#
/// <param name="sSN">在製品序號</param>
/// <param name="sLnkData">藍牙地址</param>
/// <returns></returns>
```

 

### Query_Bluetooth_ID

> 功能说明: 根據SN查詢綁定的BD

**方法定义:** 

```c#
public string Query_Bluetooth_ID(string sSN)
```

**参数定义:**

```c#
/// <param name="sSN">在製品序號</param>
/// <returns></returns>
```

 

### Unbind_Bluetooth_ID

> 功能说明: BD和SN解除綁定

**方法定义:** 

public string Unbind_Bluetooth_ID(string strEmpNo, string sSN, string strBDs)

**参数定义**:

```c#
/// <param name="strEmpNo">人員工號</param>
/// <param name="sSN">在製品序號</param>
/// <param name="strBDs">BD號，多個BD以“:”隔開</param>
/// <returns></returns>
```

 

### SetStation

> 功能说明: 回傳MES站點名稱，讓產測人員於首次執行時指定

**方法定义:**

```c#
 public ObservableCollection<Com_Model> SetStation()
```

**参数定义:无**

> MES建議测试程序寫入到 INI，避免產測程式重啟後，人員必須要再指定一次。

 

### InsertSSN

> 功能说明: 綁定出貨序號

**方法定义:** 

```c#
public string InsertSSN(string sSN, string sLnkData)
```

**参数定义:**

```c#
/// <param name="sSN">在製品序號</param>
/// <param name="sLnkData">出貨序號</param>
/// <returns></returns>
```



### checkSN_Station

> 功能说明: 檢測序號的有效性及是否允許在此工站測試

**方法定义:** 

```c#
public string checkSN_Station(string sn, string station)
```

**参数定义:**

```c#
/// <param name="sSN">在製品序號</param>
/// <param name="station">工作站編號</param>
/// <returns></returns>
```

 

### ASSY_Data

> 功能说明: 上傳組裝資料

**方法定义:** 

```c#
public string ASSY_Data(string sEMP, string sSN, string sKPSN, string sStation)
```

**参数定义:**

```c#
/// <param name="sEMP">人員工號</param>
/// <param name="sSN">在製品序號</param>
/// <param name="sKPSN">子件序號</param>
/// <param name="station">工作站編號</param>
/// <returns></returns>
```
