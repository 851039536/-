

# ATE API

##  软体设计理念

> 本软体主要是提供客户自行撰写程序，将资料传送给MES系统，由MES系统确认资料的正确性与记录的工作，MES系统也会将相关资料回传回来 

**本软体具备几下的功能：**

提供driver的function call，供user自行开发程序



## ATE API软体说明

###  安装软体

> 请先将本软体的压缩档(Connect Driver.zip)解压缩至电脑中，有两个目录driver和sample，driver目录下存放的是本软体的Driver(SajetConnect.Dll)，当撰写程序时，这个目录下的所有档案须复制至同个目录 ，而sample目录下存放的是本软体用Delphi 开发的sample范例。

 

###  Dirver基本说明：

> 我们提供的driver包含了三个FUNCTION CALL ，兹简易说明如下(程序的写法可参考sample目录下的程序)：

1 SajetTransStart：用来分配Driver的资源，当程序启动后，须执行这个Function一次 

2 SajetTransClose：用来释放driver所占用的资源，当有呼叫过TransStart这个function之后，则必须在程序结束前呼叫这个function释放资源

3 SajetTransData：传送定义好的资料给MES系统	

 

### Dirver说明- SajetTransStart：

> 这个function主要功用是Create Driver会使用到的相关资源，这个function只须于一开始呼叫过一次即可，当重复呼叫时，driver会忽略而不处理，当执行成功后，即可透过SajetTransData这个function传送资料给MES系统

**其宣告如下**：

```c#
function SajetTransStart : boolean; stdcall;
```

**传入变数**：

无

**传回结果**：

> 型态为Boolean，当成功执行本function，即会回传true值，当有下列情况时，会回传false值，

(1) 启动driver有发生exception

​	

### Dirver说明- SajetTransClose

> 这个function主要功用用于释放driver所占用的资源，当程序要结束执行时，须呼叫这个function来释放资源。

 

**其宣告如下**：

```c#
function SajetTransClose : boolean; stdcall;
```

**传入变数**：

无

**传回结果**：

> 型态为Boolean，当成功执行本function，即会回传true值，当有下列情况时，会回传false值，

(1) 有发生exception	

###  Dirver说明- SendSajetDriver

> 当user要传送资料给MES系统，即可呼叫这个function。



**其宣告如下**：

```c#
function SajetTransData(f_iCommand : integer;
f_pData,f_pLen : pointer) : boolean; stdcall;
```

**传入变数**：

`f_iCommand`：型态为string指定此次资料所要执行的工作，相关说明请参阅Dirver说明-Function COMMAND

 

f_pData：型态为pointer，其值为一连续记忆体空间(例如ARRAY OF BYTE)的起始位置， 当USER传送一字符串资料给MES系统，则依序将值填入到这个记忆体空间，当执行完FUNCTION后，MES系统也会将要回传的资料放到这个记忆体空间，这个记忆体空间的可为动态阵列或固定阵列，但最小长度须为100，以让MES系统存放回传讯息

 

f_pLen：型态为integer的pointer，用来储放f_pData内资料的有效长度，当要执行function前，将要传送资料的长度存放到此pointer所指定的 integer变数，当执行结束后，此pointer所指定的interger会存放回传资料的有效长度，可根据此长度取得回得回传讯息。

 

**传回结果**：

> 型态为Boolean，当成功执行本function，即会回传true值，当有下列情况时，会回传false值，

(1) 当driver没有成功启动时

(3) 传入的值不合法

(4) 执行function有发生exception

 

f_pData中会存放MES回传的讯息，而f_pLen中会存放回传讯息的有效长度，可依据这两个数值得到回传讯息。

​		

​	

## 连线软体流程图

每个Function Test的ATE工作站必须是Window base的程序接口， 并且要开放出输入界面：

1. 提供作业员输入工号。

2. 提供作业员输入产品序号。

ATE须有一组测试机台(含PC，需网卡连接SFC网路)，条码枪(如确定序号可自动读入则不须条码枪)一支。

### 软体编写流程如下：

![img](file:///C:\Users\CH190006\AppData\Local\Temp\ksohtml21372\wps1.png)

 

### 流程说明：

(1) 输入员工编号：于ATE上输入作业员工号。系统会依据工号检查是否正确，如不正确会要求重新输入。

(2) 呼叫API：设定 CMD = 1，Call API，格式如下。

(3) 输入产品序号：输入目前进行作业的机台序号。系统会依据序号检查流程是否正确，如不正确会要求重新输入。

(4) 呼叫API：设定 CMD = 2，Call API，格式如下。

(5) 呼叫API：设定 CMD = 4，Call API，格式如下。測試過站呼叫一次本CMD

(6) 呼叫API：设定 CMD = 3，Call API，格式如下。所有需要控管的测试项目的数据上传呼叫本CMD


##  Protocal Format of f_pData paramenter

###  API参数格式

```c#
function SajetTransData(f_iCommandNo : integer;f_pData,f_pLen : pointer) : boolean; stdcall;
```

 

当测试程序Call API的时候，要传送资料给MES系统时，需要三个参数 f_iCommandNo、f_pData、f_pLen。

 

MES系统即会依据COMMAND NO(f_iCommandNo)***来处理工作，及进行资料(f_pData)的解码，***\*f_pData\****的资料必须以”;”当结尾，***\*f_pData\****的资料和COMMAND NO的关系兹说明如下：



###  f_iCommandNo = 1(檢查工號)

| f_pData | Remark     |
| ------- | ---------- |
| Emp_No; | 检查EMP_No |

 

```c#
Ex：SajetTransData(1,f_pData,7);
f_pData=”EMP001;”
```



> 本API(CMD=1)是用来检查员工编号是否正确, 结果是OK表示检查成功, 可以继续下一个动作。
>

 

**Return Status From MES**

| f_pData               | Remark       |
| --------------------- | ------------ |
| OK                    |              |
| NG;[EC201] EMP_NO NG; | Emp No Error |
| NG;[EC101] CALL DBA;  | API time out |
| 。。。。              | 可扩充       |

```c#
  Ex：return f_pData=’OK;’, f_pLen=3

  Ex：return f_pData =‘NG;[EC201] EMP NG;’, f_pLen=18
```



###  f_iCommandNo = 2(檢查SN)

| f_pData | Remark           |
| ------- | ---------------- |
| SN;     | 检查产品序号(SN) |

 

```c#
Ex：SajetTransData(2,f_pData,7);
f_pData=”SN0001;”
```

 

> 本API(CMD=2)是用来检查序号以及检验流程, 结果是OK表示检查成功, 可以继续下一个动作。当回应是OK的时候, 本API可以一并回应与client端协议之讯息。
>

 

**Return Status From MES**

| f_pData                   | Remark                       |
| ------------------------- | ---------------------------- |
| OK;SN;                    |                              |
| NG;[EC202] SN NG;         | SN 错误 (ex: 此SN为报废状态) |
| NG;[EC999] Route NG:XXXXX | 途程错误，此SN应该到XXXXX    |
| NG;[EC101] CALL DBA;      | API time out                 |
| 。。。。                  | 可扩充                       |

```c#
Ex：return f_pData=’OK;’, f_pLen=3
Ex：return f_pData=‘NG;[EC202] SN NG;’, f_pLen=17
Ex：return f_pData=‘NG;[EC101] CALL DBA;’, f_pLen=20\*
```

 

| f_pData                                                      | Remark       |
| ------------------------------------------------------------ | ------------ |
| EVENT_ID; TESTER_NAME; SERIAL_NUMBER;EVENT_TIME;PROCESS_STEP;SITE_NAME;Line_Name;Line_Description;TEST_PROGRAM_DESCRIPTION; TEST_PROGRAM_VERSION; FIXTURE_ID; OPERATOR_ID; EVENT_STATUS; EVENT_DURATION; TEST_LOOP_COUNT;REPAIR_LOOP_COUNT; PROCESS_STEP_TEST_COUNT; PROCESS_STEP_ENTRY_COUNT; TEST_ORDER_NUMBER;TEST_NAME; SUB_TEST_NAME; TEST_STATUS; TEST_DURATION; TEST_RESPONSE; TEST_MEASUREMENT_VALUE; TEST_MEASUREMENT_UNIT;REFERENCE_DESIGNATOR; MIN_SPEC_LIMIT; MAX_SPEC_LIMIT; TARGET_VALUE; | 插入測試數據 |

###  f_iCommandNo = 3(BOSE data feed)



```c#
Ex1：SajetTransData(3,f_pData,327);

f_pData=”5348576552674856575669562019031807553453;SMTA0002558;F193180010CF;

2019-03-18 07:55:35;FLASH_01;JMES;
LineName;LineDescription;
FW download;V18.03.16.01;2000-660880555041-0005;

074853;PASS;189.109;

1;0;1;
1;1;FW_Down;

sub test name;PASS;21;
TestResponse;23;TestMeasurementUnit;
ReferenceDesignator;MinSpecLimit;MaxSpecLimit;
TargetValue;”
```

 

EVENT_TIME格式为：“2017-07-29 10:22:08”

f_pLen长度勿超过3000。

 

**Return Status From MES**

| No   | f_pData  | Remark |
| ---- | -------- | ------ |
| 3    | OK       |        |
| 3    | 。。。。 | 可扩充 |

```c#
Ex：return f_pData=”OK”, f_pLen=154
```



###  f_iCommandNo =4 (SN過站)

| f_pData                                             | Remark                                                       |
| --------------------------------------------------- | ------------------------------------------------------------ |
| Emp_No; SN; Result; [Error Code1, Error Code2, ……;] | 检查EMP_No; 检查产品序号(SN);過站結果(OK/NG);不良代碼(若OK則不需要傳); |

 

```c#
Ex：SajetTransData(4,f_pData,14)
  f_pData=”008;SN0001;OK;”

Ex：SajetTransData(4,f_pData,28);
  f_pData=”008;SN0002;NG;QC0003;QC1234;”
```

 

> 本API(CMD=4)是用来记录当站的测试结果, 结果只能是OK或是NG（系统检查时，结果非OK的會提示錯誤）, 检验结果如果是NG, 需要附带不良代码。多个不良代码须以***\*’,’\****隔开（如果最后一个不良代码后未加***\*’,’\****，将会自动过滤掉最后一个）。
>

 

**Return Status From MES**

| f_pData                   | Remark             |
| ------------------------- | ------------------ |
| OK;                       |                    |
| NG;[EC203] ERROR CODE NG; | Defect Code Error  |
| NG;[EC101] RESULT NG;     | 過站結果不是OK或NG |
| NG;[EC101] CALL DBA;      | API time out       |
| 。。。。                  | 可扩充             |

```c#
Ex：return f_pData=’OK;’, f_pLen=3

Ex：return f_pData=‘NG;[EC202] ERROR CODE NG;’, f_pLen=25

Ex：return f_pData=‘NG;[EC101] CALL DBA;’, f_pLen=20
```



### f_iCommandNo =5 (測試數據上傳)

| f_pData                                                      | Remark                                     |
| ------------------------------------------------------------ | ------------------------------------------ |
| EMP_No; SN; ItemName, Result, ItemValue, UpperLimit, LowerLimit, #;…… | 记录SN的测试项目、测试值、测试上下限的内容 |

 

```c#
Ex1：SajetTransData(5,f_pData,35);
f_pData=”EMP001;SN0001;Test001,P,12,10,18,#;”

Ex2：SajetTransData(5,f_pData,34);
f_pData=”EMP001;SN0001;Test001,F,9,10,18,#;”

Ex3：SajetTransData(5,f_pData,55);
f_pData=”EMP001;SN0001;Test001,F,9,10,18,#Test002,P,12.1,9,28,#;”

Ex4：SajetTransData(5,f_pData, 48);
f_pData=”EMP001;SN0001;Test001,F,9,10,18,#Test002,P,,,,#;”
```

 

本API(CMD=5)是用来记录各测试项目的测试结果，每个SN传输一次，格式为：工号 + 序列号（SN）+ 测试项目及测试结果（结果是P表示Pass,非P为Fail）

 若测试结果惟一量测值，则需包含上下限的规格值，则格式中除包含 测试项目(ItemName)、测试结果(Result)、测试实际值（ItemValue）、测试上限（UpperLimit）、测试下限（LowerLimit）等以 “,” 符号区隔。最后必须以“,”结束

若有多組測試項目與結果，每組項目之間以“#”區隔

 若测试值为非测量值，只有Pass与Fail的结果。ItemValue、UpperLimit、LowerLimit的值为空，“,”必须保留。见实例：Ex4

 f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                   | Remark        |
| ------------------------- | ------------- |
| OK;                       |               |
| NG;[EC301] TEST ITEM NG;  | Test Item NG  |
| NG;[EC302] TEST VALUE NG; | Test Value NG |
| NG;[EC101] CALL DBA;      | API time out  |
| 。。。。                  | 可扩充        |

```c#
Ex：return f_pData=’OK;’, 
f_pLen=3

Ex：return f_pData=‘NG;[EC301] TEST ITEM NG;’, 
f_pLen=24

Ex：return f_pData=‘NG;[EC302] TEST VALUE NG;’, f_pLen=25

Ex：return f_pData=‘NG;[EC101] CALL DBA;’, f_pLen=2
```



### f_iCommandNo =6 (BDA取號)

| f_pData                 | Remark                   |
| ----------------------- | ------------------------ |
| EMP_No; SN; WO; BDA_No; | 員工號; SN; 工單; BDA號; |

```c#
Ex1：SajetTransData(6,f_pData,35);
f_pData=”EMP001;SN0001;Test001;0452C725F6D1;”
```

 

> 返回結果若為OK;則字串後面拚接BD碼、BDA Type以及License Keys，Name與Value以“,”區隔，多組License Key以“#”區隔，範例如下
>

```c#
Ex：return f_pData=’OK;BDA,F1000AC00FA5;BDA Type,1;CVC,1111 1111 1111 1111 A007#NOWSPEAK,1111 1111 1111 A005#’,f_pLen=90
```

 若輸入BD碼與SN已經綁定，則返回原紀錄以及License Key；若無紀錄，則取一個新BD號並綁定此SN***\*。\****

 SN輸入可為空，則不檢查SN，並且此BDA取號但不綁定任何SN

 由MES返回BDA Type(1:正常取號 2:QC首尾件)，鎖定QC首尾件未取完且PASS以前，不可以正常取號。

 f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                              | Remark                                    |
| ------------------------------------ | ----------------------------------------- |
| OK;’BD code’;’License key’;          | 正常取號OK                                |
| NG;[EC201] EMP_NO NG;                | Emp No Error                              |
| NG;BDA and SN not match,’BDA’,’SN’;  | BD與SN不匹配，返回數據庫內已綁定的BDA與SN |
| NG;Work order not found,’wo number’; | 未找到該工單                              |
| NG; BD code is insufficient;         | BD碼餘量不足                              |
| NG; The first five not all passed;   | 首五件已經取完，但並沒有完全測試PASS      |
| NG; BDA repeat, please try again;    | BDA重複，請重新取號                       |

  

| f_pData                                     | Remark                                                       |
| ------------------------------------------- | ------------------------------------------------------------ |
| Emp_No; SN; WO; BDA_No; LicenseKey; Status; | 員工號; SN;工單; BDA號; License Keys; Status(1:燒錄成功,2:燒錄失敗,3:重新燒錄成功); |

### f_iCommandNo =7 (BDA燒錄紀錄寫入/更新)

 

```c#
Ex1：SajetTransData(7,f_pData,97);

f_pData=”EMP001; SN0001;Test001;0452C725F6D1;CVC,1111 1111 1111 1111 A007#NOWSPEAK,1111 1111 1111 A005#;3;”
```

 

 此API為寫入/更新燒錄紀錄

 License Name與License Value以“,”區隔，多組License Key以“#”區隔，範例如上

燒錄結果值為 1:燒錄成功,2:燒錄失敗,3:重新燒錄成功

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                                 | Remark                                                       |
| --------------------------------------- | ------------------------------------------------------------ |
| OK;                                     | OK                                                           |
| NG;BD code is already bound;            | BDA已經綁定                                                  |
| NG;BD is not found;                     | 取號紀錄內找不到此BD號                                       |
| NG;BDA and SN not match,’BDA’,’SN’;     | 傳入的SN與BD碼與使用紀錄不匹配，並回傳使用紀錄內應匹配的SN與BD碼 |
| NG;SN is already bound BDA,’BDA’,’ SN’; | 傳入的SN已經綁定並錄入其他BD碼，並回傳綁定紀錄               |

 

### f_iCommandNo =8 (取BDA號段信息)

| f_pData             | Remark               |
| ------------------- | -------------------- |
| WO; Is assign over; | 工單; 是否燒錄完號段 |

```c#
*Ex1：SajetTransData(8,f_pData,10);

f_pData=”Test001;N;”
```

 

 Is assign over，Y:已燒錄完, N:未燒錄完

 返回結果若為OK;則字串後面拚接查詢訊息，Key-Value以“,”區隔，多組License Key以“#”區隔，多組號段信息以“;”區隔，範例如下：

 AssignUsedCount：當前客戶分配到的BDA號總數量

 AssignBDStartNo：當前客戶分配到的BDA號段的開始BDA號

AssignBDEndNo：當前客戶分配到的BDA號段的結束BDA號

 FormAssignCount：當前工單BDA號總數量

 FormBDStartNo：當前工單BDA號段的開始BDA號

 FormBDEndNo：當前工單BDA號段的開結束BDA號

 UsedLastNo：當前工單最新一筆已使用的BDA號

 Total used qty：總取用數量

 Pass qty：燒錄PASS數量

 Fail qty：燒錄FAIL數量(含取用未燒錄的情況)

 License Keys：License Keys

```c#
Ex：return f_pData=’OK;AssignUsedCount,6240;AssignBDStartNo,2C41A1208C8D;AssignBDEndNo,2C41A120A4EC;FormAssignCount,490;FormBDStartNo,0452C725F6D1;FormBDEndNo,0452C725F8BA;UsedLastNo,0452C725F8B9;Total used qty,120;Pass qty,119;Fail qty,1;CVC,1111 1111 1111 1111 A007#NOWSPEAK,1111 1111 1111 A005#;’, f_pLen=278 
```

 f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData            | Remark                  |
| ------------------ | ----------------------- |
| NG;Data not found; | 工單輸入錯誤 / 沒有資料 |

 

 

### f_iCommandNo =9 (查詢最新一筆BDA燒錄紀錄)

| f_pData | Remark |
| ------- | ------ |
| BDA     | BDA號  |



```c#
Ex1：SajetTransData(9,f_pData,12);

f_pData=” 0452C725F6D1;”
```

 

此API為查詢取號/燒錄紀錄，根據BDA查詢燒錄紀錄。License Name與License Value以“,”區隔，多組License Key以“#”區隔，範例如下

Terminal ID：站點ID

Test User ID：員工工號

Record Time：最後燒錄時間(若為空則取用但未返回測試時間)

Create Time：取用時間

Status：1:燒錄PASS, 2:燒錄Fail/未燒錄, 3:報廢待回收

License Keys：License Keys

```c#
Ex：return f_pData=’OK;TerminalID,12345;TestUserID,T0085;RecordTime,2019-04-24 15:30:12;CreateTime, 2019-04-24 15:30:12; CVC,1111 1111 1111 1111 A007#NOWSPEAK,1111 1111 1111 A005#;’,f_pLen=159
```

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData           | Remark             |
| ----------------- | ------------------ |
| OK;BDA unused;    | BDA未使用          |
| OK;BDA No record; | BDA沒有燒錄紀錄    |
| NG;……             | 查詢失敗，系統錯誤 |

 



###  f_iCommandNo =12 (QC數據反查)

| f_pData                   | Remark                  |
| ------------------------- | ----------------------- |
| SN;TestName;[Process_id;] | SN;測試項目名;[製程ID;] |

 

```c#
Ex1：SajetTransData(12,f_pData,18);

f_pData=”832177N90860002AE;Accelerometer X;100444”
```

 

此API為輸入SN反查最新一筆Bose data feed上傳的數據，回傳資料格式如下，按順序拼接字串

\1. OK;

\2. EVENT_ID; 

\3. TESTER_NAME; 

\4. SERIAL_NUMBER;

\5. EVENT_TIME;

\6. PROCESS_STEP;

\7. SITE_NAME;

\8. Line_Name;

\9. Line_Description;

\10. TEST_PROGRAM_DESCRIPTION; 

\11. TEST_PROGRAM_VERSION; 

\12. FIXTURE_ID; 

\13. OPERATOR_ID; 

\14. EVENT_STATUS; 

\15. EVENT_DURATION; 

\16. TEST_LOOP_COUNT;

\17. REPAIR_LOOP_COUNT; 

\18. PROCESS_STEP_TEST_COUNT; 

\19. PROCESS_STEP_ENTRY_COUNT; 

\20. TEST_ORDER_NUMBER;

\21. TEST_NAME; 

\22. SUB_TEST_NAME; 

\23. TEST_STATUS; 

\24. TEST_DURATION; 

\25. TEST_RESPONSE; 

\26. TEST_MEASUREMENT_VALUE; 

\27. TEST_MEASUREMENT_UNIT;

\28. REFERENCE_DESIGNATOR; 

\29. MIN_SPEC_LIMIT; 

\30. MAX_SPEC_LIMIT; 

\31. TARGET_VALUE;

 

```
Ex：return f_pData=’OK;5348576552674860000000000000000000000000;SMTA0002558;832177N90860002AE;2019-03-18 07:55:35; FLASH_01;JMES;Frank_ASSY;test line;FW download;V18.03.16.01;2000-660880555041-0005;074853;PASS;189.109;1;0;1;1;1;FW_Down;;PASS;1;;;;;;;;’,f_pLen=231
```

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                     | Remark   |
| --------------------------- | -------- |
| OK;                         | OK       |
| NG;SN or TestName not found | 找不到SN |

 



| f_pData        | Remark        |
| -------------- | ------------- |
| SN; File_path; | SN; 檔案路径; |

###  f_iCommandNo =13 (上传SCFile路径)

 

```c#
 Ex1：SajetTransData(13,f_pData,106);

f_pData=”832177N91150001AE;\\10.53.4.1TeDateBaseHDT656T3.0station2019-04-25832177N91150001AE_20190425T201345;”
```

 

此API為上传SC檔案路径方法

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData          | Remark         |
| ---------------- | -------------- |
| OK;              | 上傳成功       |
| NG;SN is null;   | 上傳SN不可为空 |
| NG;Insert error; | 上传失败       |

 



| f_pData                                                      | Remark                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| SMT_SN; Ship_SN; Pro_SN; Customer_Name; Model; Part_NO; WO; Class; TestResult; bluetooth; Version; License_No; TestValue; R1; R2; | SMT_SN; Ship_SN; Pro_SN; 客戶名稱; 機種; 成品料號; 工單; 班別; 測試結果; 藍芽碼; 版本; License Keys; 測試值; R1; R2; |

###  f_iCommandNo =14 (EDA數據上傳)

 

 

```c#
Ex1：SajetTransData(14,f_pData,266);

f_pData=”1123ABCD456C;077061Z91410008AZ; ;BOSE;HDT656;85X656001000-M;Test_ATE;Shift;OK; ;FW Version:4.1.3-1861#Device Name:Bose QC35 II#; ;Check Test Status:1#FW Version:4.1.3-1861#In DFU:1#Open Connection:1#Device Name:Bose QC35 II#Read BTAD:1123ABCD456C#;ZD357;Write BTAD;”
```

 

此API為上傳EDA數據

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData | Remark   |
| ------- | -------- |
| OK;     | 上傳成功 |

 



### f_iCommandNo =15 (查詢是否有測試資料)

| f_pData | Remark |
| ------- | ------ |
| SN;     | SN;    |

```c#
Ex：SajetTransData(15,f_pData,17);
f_pData=” 077061191470070AZ”
```

 

此API為查詢當前站是否有測試

TE有些測試站為客供程序，需先測試客供程序，而後才執行MES過站程式，此API為了防止未測試直接過站的情況。

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                                   | Remark                 |
| ----------------------------------------- | ---------------------- |
| OK;                                       | OK                     |
| NG;No Test Data (Process:’Process Name’); | 當前站未測試，不可過站 |

 



###  f_iCommandNo =16(檢查員工是否有當前站權限)

| f_pData          | Remark     |
| ---------------- | ---------- |
| Emp_No;Password; | 工號;密碼; |

 

```c#
Ex：SajetTransData(16,f_pData,15);
f_pData=” RET0031;123456;”
```

 

此API為檢查員工是否有當前站修改測試程序配置的權限

直接透過TGS獲取當前製程，判斷輸入的用戶是否有在當前製程修改配置的權限。

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                | Remark     |
| ---------------------- | ---------- |
| OK;                    | OK         |
| NG;Emp No. Error;      | 无此员工号 |
| NG;Emp Password Error; | 密码错误   |
| NG;No Permission;      | 无权限     |

 



### f_iCommandNo =17(取虛擬BD號)

| f_pData        | Remark     |
| -------------- | ---------- |
| Work Order;Qty | 工單;數量; |

 

```c#
Ex1：SajetTransData(17,f_pData,16);
f_pData=” 703-191200010;1;”

Ex2：SajetTransData(17,f_pData,16);
f_pData=” 703-191200010;2;”
```

 

此API為SMT T0.1站取虛擬BD號

 取號可以取1或多個，具體數量由第二個參數(Qty)決定。

f_pLen长度勿超过3000。

 

```c#
*Ex1：return f_pData=’OK;E01031400001;’, f_pLen=16 

Ex2：return f_pData=’OK;E01031400002;E01031400003;’, f_pLen=29
```

**Return Status From MES**

| f_pData                          | Remark     |
| -------------------------------- | ---------- |
| OK;’BD code 01’;[’ BD code 02’;] | OK         |
| NG;(015)WO Error;                | 工單號錯誤 |
| NG;Qty Error;                    | 數量錯誤   |

 



### f_iCommandNo =18(組立SN綁定Keyparts)

| f_pData                         | Remark                                              |
| ------------------------------- | --------------------------------------------------- |
| Emp_No;Assembly SN;Keyparts SN; | 检查EMP_No;組立SN;Keyparts SN(可輸入多個,逗號區隔); |



```c#
*Ex1：SajetTransData(18,f_pData,38);
f_pData=”RET0031;077061191480069AZ;A5011T00009;”

Ex2：SajetTransData(18,f_pData,42);
f_pData=”RET0031;077061191480070AZ;M2-0138,C2-0064;”
```

 

此API為組立SN綁定Keyparts SN

 目前應用在如下幾種情況

n 取代DCN PCBA綁定站功能，直接將燒錄於主板內的PCB SN於T2.3站進行綁定

n 取代DCN Controller綁定站功能，直接將Controller/Mic於T8.0站綁定

 假如有多個附件，每個附件間用逗號 “,” 區隔

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                                | Remark                  |
| -------------------------------------- | ----------------------- |
| OK;                                    | OK                      |
| NG;(020)SN Error;                      | SN錯誤                  |
| NG;(141)This PCBA was not checked yet. | Keypart未做收料IQC檢查  |
| NG;(142)This PCBA already scrapped.    | Keypart已報廢           |
| NG;(060)BOM Error.                     | BOM錯誤                 |
| NG;This SN already linked Keypart      | 此SN已綁定其他Keypart   |
| NG;Already linked with ‘SN’            | 此Keypart已綁定另一個SN |

 



###  f_iCommandNo =19(組立SN - PCB SN綁定檢查)

| f_pData      | Remark  |
| ------------ | ------- |
| Assembly SN; | 組立SN; |



```c#
Ex1：SajetTransData(19,f_pData,18);
f_pData=”077061191480069AZ;”
```

 此API為T4.3站SN綁定檢查

檢查是否有綁定PCB SN

 檢查是否有綁定BDA

f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                                    | Remark     |
| ------------------------------------------ | ---------- |
| OK;                                        | OK         |
| NG;PCB SN has not been linked;             | PCB未綁定  |
| NG;BDA has not been linked;                | BDA未綁定  |
| NG;Data not found (serial number, part id) | 未找到此SN |

 



### f_iCommandNo =20(檢查附件綁定)

| f_pData                                         | Remark                              |
| ----------------------------------------------- | ----------------------------------- |
| Assembly SN; Attachment SN1[, Attachment SN2]…; | 組立SN;附件SN(可輸入多個,逗號區隔); |

 

```c#
Ex1：SajetTransData(20,f_pData,13);
f_pData=”SN0001;C2001;”

Ex2：SajetTransData(20,f_pData,25);
f_pData=”SN0001;C2001,M2001,K2001;”
```

 

 此API為附件綁定檢查

第一個參數輸入組立SN，第二個參數輸入附件SN，假如有多個附件，每個附件間用逗號 “,” 區隔

組立SN與附件SN使用分號 “;” 區隔

 f_pLen长度勿超过3000。

 

**Return Status From MES**

| f_pData                             | Remark               |
| ----------------------------------- | -------------------- |
| OK;                                 | OK                   |
| NG;SN:‘SN’, ATT:‘Att. SN’ not match | 組立SN與附件SN不匹配 |

