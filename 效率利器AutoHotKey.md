# 效率利器 AutoHotKey

> ### 自动热键
>
> 强大。简单易学。
>
> Windows的终极自动化脚本语言。

## Download —— 下载

**[AutoHotkey —— 官网](https://www.autohotkey.com/)**

**进入官网，没什么可说的，直接点击 Download 下载安装**

**安装成功后，是没有任何提示的，直接关闭安装程序即可！**

## Create a program —— 创建项目

**桌面右键→→新建→→ AutoHotkey Script 即可创建一个脚本**

**新建一个文本文件，修改文件后缀名为 .ahk 格式也行**



##  快捷键定义

**;#代表win，**

**+代表shift，^代表Ctrl，**

**！代表Alt ;**

 **如果要注释，可以用分号 也可以用/**/**

- ### 1. 热键标记

  `!`：是热键标记，这里的感叹号代表键盘上的 alt 键。
  `!n`：即对应键盘热键 alt + n。

  常用热键标记对应关系：

  - `#` ：win 键
  - `!` ：alt
  - `^` ：ctrl
  - `+` ：shift

  ### 2. 连接

  符号 `::` 意味着每次按下前面的热键时，随后的命令将会被执行。这里用来连接热键标记和 Run 命令。

  ### 3. Run 命令

  Run 命令：用来启动一个程序、文档、URL 网址或者快捷方式。如：

  ```text
  Run Notepad
  Run C:\My Documents\Address List.doc
  Run www.yahoo.com
  Run mailto:someone@somedomain.com
  ```

  

  把上面 3 部分组合起来即可实现自定义快捷键。

| Ctrl                   | ^            | ^                             |
| ---------------------- | ------------ | ----------------------------- |
| Shift                  | +            | +                             |
| Alt                    | !            | !                             |
| Windows键              | #            | #                             |
| #、^、+、!等           | 一般不用     | {#}、{^}、{+}、{!}等          |
|                        |              |                               |
| 左Ctrl、左Shift、左Alt | <^   <+   <! |                               |
| 右Ctrl、右Shift、右Alt | >^   >+   >! |                               |
| Enter                  |              | {enter}                       |
| Tab                    |              | {Tab}                         |
| F1、F12等              | F1、F12等    | {F1}、{F12}等                 |
| Esc                    |              | {Esc}或{Escape}               |
| 空格                   |              | {Space}                       |
| Backspace              |              | {Backspace}或{BS}             |
| Delete                 |              | {Delete} or {Del}             |
| Insert                 |              | {Insert} or {Ins}             |
| ↑、↓、←、→             |              | {Up}、{Down}、{Left}、{Right} |
| Home键                 |              | {Home}                        |
| End键                  |              | {End}                         |
| PageUp键               |              | {PgUp}                        |
| PageDown键             |              | {PgDn}                        |
| CapsLock键             |              | {CapsLock}                    |

## Substitute character —— 替换字符


**[AutoHotkey —— 中文脚本手册](http://ahkcn.sourceforge.net/docs/AutoHotkey.htm)**
**我们来一个简单的例子：输入 chinar 自动替换为 `http://www.chinar.xin`**

**在打开的记事本中输入：**

**第一种写法：**

```ahk
::oyk::http://oykperson.xyz/
```

**第二种写法：**

```ahk
::oyk::
Send http://oykperson.xyz/
return
::bky::
Send https://www.cnblogs.com/
return
```

**AHK语法中： Send 是发送命令， return 是结束**

**保存后，右键文件 →→ Run Script 运行脚本**

**然后，当我们输入文本`chinar` 按下 空格键 / 回车键 / tab 键时，会自动帮我们替换为`http://www.chinar.xin`**



## **极速打开网页**

```
#0::Run https://tinypng.com/
```

**Win+0**

##  自定义快捷键-打开文件或网址

```ruby
!n::run notepad
```

```ruby
!c::run, D:\SoftwareKit\_jade_new_soft\cmd_markdown_win64\Cmd Markdown.exe
```

**Alt+n 或+c启动程序**

## **一键拷贝文件路径**

```kotlin
^+c::
; null= 
send ^c
sleep,200
clipboard=%clipboard% ;%null%
tooltip,%clipboard%
sleep,500
tooltip,
return
```

只需要**Ctrl+shift+c**即可拷贝文件路径

## **缩写快速打出常用语**

```ruby
::/mail::gmail@gmail.com
::/jeff::http://www.jeffjade.com/
::/con::console.log();
::/js::javascript:;
::/fk::轩先生这会子肯定在忙，请骚后。thx。祝君：天天开心，日日欣悦。
```

键入`/jeff` 后，再加**空格**、或 **tab**、或**回车**

## **激活/打开/隐藏程序**

```php
#c::
IfWinNotExist ahk_class Chrome_WidgetWin_1
{
    Run "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe"
    WinActivate
}
Else IfWinNotActive ahk_class Chrome_WidgetWin_1
{
    WinActivate
}
Else
{
    WinMinimize
}
Return
```

**快捷键：Win+C**

## 音量随心所欲



```
;最钟爱代码之音量随心所欲
;=========================================================
~lbutton & enter:: ;鼠标放在任务栏，滚动滚轮实现音量的加减
exitapp  
~WheelUp::  
if (existclass("ahk_class Shell_TrayWnd")=1)  
Send,{Volume_Up}  
Return  
~WheelDown::  
if (existclass("ahk_class Shell_TrayWnd")=1)  
Send,{Volume_Down}  
Return  
~MButton::  
if (existclass("ahk_class Shell_TrayWnd")=1)  
Send,{Volume_Mute}  
Return  

Existclass(class)  
{  
MouseGetPos,,,win  
WinGet,winid,id,%class%  
if win = %winid%  
Return,1  
Else  
Return,0  
}
;=========================================================
```

## 窗口操作

```
;=========================================================================
#enter:: ;最大化窗口
WinGet,S,MinMax,A
if S=0
    WinMaximize,A
else if S=1
    WinRestore,A
else if S=-1
    WinRestore,A
return

!enter:: ;最爱代码之窗口置顶
    WinGet ow, id, A
    WinTopToggle(ow)
    return
WinTopToggle(w) {
 
    WinGetTitle, oTitle, ahk_id %w%
    Winset, AlwaysOnTop, Toggle, ahk_id %w%
    WinGet, ExStyle, ExStyle, ahk_id %w%
    if (ExStyle & 0x8)  ; 0x8 为 WS_EX_TOPMOST.在WinGet的帮助中
        oTop = 置顶
    else
        oTop = 取消置顶
    tooltip %oTitle% %oTop%
    SetTimer, RemoveToolTip, 5000
    return
 
    RemoveToolTip:
    SetTimer, RemoveToolTip, Off
    ToolTip
    return
}

!x:: ;关闭窗口
send ^w
return

#c:: ;cosea版以管理员身份打开cmd命令行
Run *RunAs cmd ,,max
return

#m:: ;窗口最小化
WinMinimize, A
return

^delete::FileRecycleEmpty ;ctrl+del，清空回收站

F7::
Shutdown, 1 ;关机
;Shutdown, 2 ;重启
return

;代码之清除所有标点符号================================
F2:: ;清除所有标点符号，并换行
run notepad,,max
Loop
{
StringReplace, clipboard, clipboard, `, , `r`n, UseErrorLevel ;清除英文逗号
StringReplace, clipboard, clipboard, ， , `r`n, UseErrorLevel ;清除中文逗号
StringReplace, clipboard, clipboard, `; , `r`n, UseErrorLevel ;清除英文分号
StringReplace, clipboard, clipboard, ； , `r`n, UseErrorLevel ;清除英文分号
StringReplace, clipboard, clipboard, . , `r`n, UseErrorLevel ;清除英文句号
StringReplace, clipboard, clipboard, 。 , `r`n, UseErrorLevel ;清除中文句号
StringReplace, clipboard, clipboard, : , `r`n, UseErrorLevel ;清除英文冒号
StringReplace, clipboard, clipboard, ： , `r`n, UseErrorLevel ;清除中文冒号
StringReplace, clipboard, clipboard, ‘’ , `r`n, UseErrorLevel ;清除中文双引号1
StringReplace, clipboard, clipboard, “ , `r`n, UseErrorLevel ;清除中文双引号2
StringReplace, clipboard, clipboard, ” , `r`n, UseErrorLevel ;清除中文双引号3
StringReplace, clipboard, clipboard, ' , `r`n, UseErrorLevel ;清除英文双引号
StringReplace, clipboard, clipboard, 、 , `r`n, UseErrorLevel ;清除中文顿号
StringReplace, clipboard, clipboard, / , `r`n, UseErrorLevel ;清除右斜杠
StringReplace, clipboard, clipboard, \ , `r`n, UseErrorLevel ;清除左斜杠
StringReplace, clipboard, clipboard, - , `r`n, UseErrorLevel ;清除短横线
StringReplace, clipboard, clipboard, —— , `r`n, UseErrorLevel ;清除长横线
StringReplace, clipboard, clipboard, = , `r`n, UseErrorLevel ;清除等号
StringReplace, clipboard, clipboard, + , `r`n, UseErrorLevel ;清除加号
StringReplace, clipboard, clipboard, （ , `r`n, UseErrorLevel ;清除中文左括号
StringReplace, clipboard, clipboard, ） , `r`n, UseErrorLevel ;清除中文右括号
StringReplace, clipboard, clipboard, ( , `r`n, UseErrorLevel ;清除英文左括号
StringReplace, clipboard, clipboard, ) , `r`n, UseErrorLevel ;清除英文右括号
StringReplace, clipboard, clipboard, 【 , `r`n, UseErrorLevel ;清除中文【
StringReplace, clipboard, clipboard, 】 , `r`n, UseErrorLevel ;清除中文】
StringReplace, clipboard, clipboard, [ , `r`n, UseErrorLevel ;清除[
StringReplace, clipboard, clipboard, ] , `r`n, UseErrorLevel ;清除]
StringReplace, clipboard, clipboard, ! , `r`n, UseErrorLevel ;清除英文感叹号
StringReplace, clipboard, clipboard, ！ , `r`n, UseErrorLevel ;清除中文感叹号
StringReplace, clipboard, clipboard, ？ , `r`n, UseErrorLevel ;清除中文问号
StringReplace, clipboard, clipboard, ? , `r`n, UseErrorLevel ;清除英文问号
StringReplace, clipboard, clipboard, < , `r`n, UseErrorLevel ;清除英文<
StringReplace, clipboard, clipboard, > , `r`n, UseErrorLevel ;清除英文>
StringReplace, clipboard, clipboard, 《 , `r`n, UseErrorLevel ;清除中文《
StringReplace, clipboard, clipboard, 》 , `r`n, UseErrorLevel ;清除英文》
StringReplace, clipboard, clipboard, @ , `r`n, UseErrorLevel ;清除@
StringReplace, clipboard, clipboard, # , `r`n, UseErrorLevel ;清除#
StringReplace, clipboard, clipboard, $ , `r`n, UseErrorLevel ;清除$
StringReplace, clipboard, clipboard, `% , `r`n, UseErrorLevel ;清除%
StringReplace, clipboard, clipboard, ^ , `r`n, UseErrorLevel ;清除^
StringReplace, clipboard, clipboard, & , `r`n, UseErrorLevel ;清除&
StringReplace, clipboard, clipboard, * , `r`n, UseErrorLevel ;清除*
StringReplace, clipboard, clipboard, `` , `r`n, UseErrorLevel ;清除`
StringReplace, clipboard, clipboard, `:: , `r`n, UseErrorLevel ;清除::
StringReplace, clipboard, clipboard, " , `r`n, UseErrorLevel ;清除"
StringReplace, clipboard, clipboard, { , `r`n, UseErrorLevel ;清除{
StringReplace, clipboard, clipboard, }, `r`n, UseErrorLevel ;清除}
StringReplace, clipboard, clipboard, |, `r`n, UseErrorLevel ;清除|
StringReplace, clipboard, clipboard, ·, `r`n, UseErrorLevel ;清除中文···
StringReplace, clipboard, clipboard, ~, `r`n, UseErrorLevel ;清除~
StringReplace, clipboard, clipboard, _ , `r`n, UseErrorLevel ;清除_
    if ErrorLevel = 0  ; 不需要再进行替换.
        break
}
sleep,200
send,^v
return

F8:: ;清除Windows中文件名不能包含下列任何字符： \ / : * ? " < > | 
Loop
{
StringReplace, clipboard, clipboard, ? , , UseErrorLevel ;清除英文问号
StringReplace, clipboard, clipboard, * , , UseErrorLevel ;清除*
StringReplace, clipboard, clipboard, : , , UseErrorLevel ;清除英文冒号
StringReplace, clipboard, clipboard, " , , UseErrorLevel ;清除"
StringReplace, clipboard, clipboard, < , , UseErrorLevel ;清除英文<
StringReplace, clipboard, clipboard, > , , UseErrorLevel ;清除英文>
StringReplace, clipboard, clipboard, / , , UseErrorLevel ;清除右斜杠
StringReplace, clipboard, clipboard, \ , , UseErrorLevel ;清除左斜杠
StringReplace, clipboard, clipboard, |, , UseErrorLevel ;清除|
    if ErrorLevel = 0  ; 不需要再进行替换.
        break
}
return

;神级代码之锁定中英文=============================================
;功能：输入法状态提示 ;f3强制中文 f4强制英文小写
;环境：win10+搜狗输入法，输入法状态切换用默认的shift键。
;作者：kazhafeizhale 知乎@oahgnat11
#NoEnv ; Recommended for performance and compatibility with future AutoHotkey releases.
SendMode Input ; Recommended for new scripts due to its superior speed and reliability.
SetWorkingDir %A_ScriptDir% ; Ensures a consistent starting directory.
#SingleInstance force

XButton1::^c
XButton2::^v

F3::
	SetCapsLockState,off
	switchime(0)
	switchime()
return

F4::
	SetCapsLockState,off
	switchime(0)
return

switchime(ime := "A")
{
    if (ime = 1){
        DllCall("SendMessage", UInt, WinActive("A"), UInt, 80, UInt, 1, UInt, DllCall("LoadKeyboardLayout", Str,"00000804", UInt, 1))
    }else if (ime = 0)
    {
        DllCall("SendMessage", UInt, WinActive("A"), UInt, 80, UInt, 1, UInt, DllCall("LoadKeyboardLayout", Str,, UInt, 1))
    }else if (ime = "A")
    {
        ;ime_status:=DllCall("GetKeyboardLayout","int",0,UInt)
        Send, #{Space}
    }
}
```

## 得到当前选中文件的路径

```
;=========================================================
#+c:: ;用快捷键得到当前选中文件的路径
send ^c
sleep,200
clipboard=%clipboard% ;windows 复制的时候，剪贴板保存的是“路径”。只是路径不是字符串，只要转换成字符串就可以粘贴出来了
tooltip,%clipboard% ;提示文本
sleep,500
tooltip,
return
;=========================================================
```



## 开机自启

把需要开机启动的脚本，放入 “启动” 文件夹即可。

**目录：**{C:\Users\username\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup}

## 提示：避免和系统快捷键冲突

> 系统的常用快捷键都是使用Ctrl+XX和Alt+XX来组合的,而Win+XX的很少，所以我们应该尽量使用Win+XX来组合

```text
/*温馨提示*/
;Windows系统默认的Win快捷键:
;Win + E：打开资源管理器;
;Win + D：显示桌面;
;Win + F：打开查找对话框;
;Win + R：打开运行对话框;
;Win + L：锁定电脑;
;Win + PauseBreak：打开系统属性对话框;
;Win + Q: 本地文件 / 网页等搜索;
;Win + U: 打开控制面板－轻松使用设置中心;
```