---
title: cmd运行程序时假死解决方法
tags:
  - [cmd]
  - [python]
  - [c++]
categories:
  - gallery
date: 2023-12-19 00:30:13
---

最近使用打包的Python程序执行定时任务的时候，发现有时候会不动了，点击一下之后又开始继续执行了，典型的cmd假死问题。记录一下往上搜罗到的解决方法。 

一、 在CMD窗口中点击右键->属性->选项->编辑选项->取消勾选“快速编辑模式”  

但这个方法作用范围有限，我发现设置完cmd是禁用了，运行一个exe终端，发现它还是启动了快速编辑模式，所以每运行一次exe都需手动设置取消“快速编辑模式”。  

二、 修改注册表  
建立一个 批处理文件 xxx.bat, 写入以下内容:  
```shell
@echo off
reg add HKEY_CURRENT_USER\Console /v QuickEdit /t REG_DWORD /d 00000000 /f
```
保存后执行即可. 以后新打开的命令行就会自动取消快速编辑模式  

三、 在自己要运行的程序里面加上取消“快速编辑模式”的code(放前面)  

python
```python
import ctypes

# 取消或禁用控制台/终端的快速编辑模式
kernel32 = ctypes.windll.kernel32
kernel32.SetConsoleMode(kernel32.GetStdHandle(-10), (0x4 | 0x80 | 0x20 | 0x2 | 0x10 | 0x1 | 0x00 | 0x100))
```  
  
c++
```cpp
HANDLE hStdin = GetStdHandle(STD_INPUT_HANDLE);

DWORD mode;
GetConsoleMode(hStdin, &mode);

mode &= ~ENABLE_QUICK_EDIT_MODE; //移除快速编辑模式

mode &= ~ENABLE_INSERT_MODE; //移除插入模式

mode &= ~ENABLE_MOUSE_INPUT;

SetConsoleMode(hStdin, mode);

```
