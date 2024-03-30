---
title: WinDbg基础使用方法
date: 2023-09-22
tags:
- WinDbg
categories:
---

## 安装
1. 从官网下载WinDbg并安装
2. 设置Symbols Path
```bash
_NT_SYMBOL_PATH=srv*c:\symbols*http://msdl.microsoft.com/download/symbols
```
3. 打开DUMP文件（如何获取DUMP文件，网上有很多教程，在此省略）

## 常用指令
```bash 
!analyze -v         # 详细列出DUMP文件的信息
~* kb               # 查看所有线程的堆栈
!threads            # 查看当前进程的所有线程
!peb                # 查看进程环境块
!teb                # 查看当前线程环境块
!clrstack           # 查看 .NET 程序的堆栈信息
!dumpheap           # 查看堆中的对象信息
!dumpobj            # 查看堆中指定对象的信息
!gcroot             # 查看指定对象的根引用
!sosex.mdt          # 查看指定类型的所有实例信息
!syncblk            # 查看线程之间是否存在互锁情况
.load  C:\Windows\Microsoft.NET\Framework64\v4.0.30319\SOS.dll
                    # 加载sos
.chain              # 显示加载的扩展dll
!runaway            # 查看线程运行的时间
~*s                 # 进入线程，*代表线程号
!dso                # 将当前栈上所有变量都显示出来
!lm                 # 列出模块
!lmf                # 列出当前进程中加载的所有DLL文件和对应路径
!lmvm               # 查看DLL/EXE文件信息，参数为某个DLL文件名称
!address            # 内存使用情况
!threadpool(!tp)    # 显示程序池信息
!ProcInfo           # 显示进程信息
!address -summary   # 内存概况
!heap               # 查看进程堆
!locks              # 查看当前处于临界区的线程
```