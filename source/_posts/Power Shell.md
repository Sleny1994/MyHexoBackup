---
title: PowerShell学习笔记
date: 2023-07-01
tags: 
- PowerShell
categories:
- PowerShell
---

## 常用命令
```powershell
$PSVersionTable         # 查看版本信息
Update-help             # 更新帮助文档【注：需要管理员启动】
Help / Man              # 和Get-Help返回相同的结果，但前者一次一页显示
Get-Help xxx | More     # 
Help *log*              # 可以使用通配符，*log*是参数-Name，可以省略不写
```