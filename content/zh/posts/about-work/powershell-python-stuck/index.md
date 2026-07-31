---
title: "Python脚本总是卡住？为什么在PowerShell敲几下回车又继续运行"
date: 2026-07-31T23:20:00+08:00
draft: false
categories:
  - Python
  - 开发工具
tags:
  - Python
  - PowerShell
  - Windows
  - 调试
  - 多线程
description: "解决Python脚本运行过程中卡住，PowerShell敲几下回车又继续执行的问题，分析输入阻塞、缓冲、线程锁、网络请求、GPU任务等常见原因。"
---
# Python脚本总是卡住？为什么PowerShell敲几下回车又继续运行？

很多 Windows 用户在运行 Python 脚本时会遇到一个非常奇怪的问题：

> Python程序运行一段时间后突然卡住，没有报错，也没有退出。  
> 但是在 PowerShell 窗口里面敲几下回车，程序又继续执行。  
> 过一会儿又卡住，再敲回车又恢复。

## 先说解决方法

如果代码还没运行，可以在你的代码import 之后加入：
```python
import sys

# 彻底解决 Windows PowerShell / CMD 鼠标点击导致 Python 卡死的问题
if sys.platform == "win32":
    import ctypes
    kernel32 = ctypes.windll.kernel32
    # 获取标准输入句柄
    hInput = kernel32.GetStdHandle(-10)
    mode = ctypes.c_ulong()
    kernel32.GetConsoleMode(hInput, ctypes.byref(mode))
    # 禁用 ENABLE_QUICK_EDIT_MODE (0x0040)，保留 ENABLE_EXTENDED_FLAGS (0x0080)
    mode.value &= ~0x0040
    mode.value |= 0x0080
    kernel32.SetConsoleMode(hInput, mode.value)
```

如果你的代码正在运行
可以直接在终端界面设置：

右键 PowerShell 窗口最顶部的标题栏，选择 属性（Properties）。

在弹出的窗口中，找到 选项（Options） 标签页。

找到 快速编辑模式（QuickEdit Mode），把前面的勾选取消掉。

点击 确定 保存。


这样就搞定了，如果你想知道原理可以继续往下看

## 为什么会卡住
当你的 Python 脚本在终端里疯狂打日志或刷 tqdm 进度条时：

只要你的鼠标在 PowerShell 窗口里不小心点了一下，或者划选了某个文字，PowerShell 就会认为你正在“选中文本”。

此时终端会进入 标记模式（Selection/Marking Mode），并强制冻结（暂停）所有标准输出 (stdout)。

Python 脚本因为无法继续往终端输出文字，就会被操作系统彻底卡住锁死。

只有当你敲下 Enter（回车） 或 Esc 键取消选中时，控制台才解冻，Python 才会继续运行。