---
title: VSCode中配置C Sharp开发环境
date: 2023-06-06
tags: 
- VSCode
- CSharp
categories: 
- CSharp
---
## 下载安装VSCode
略

## 下载安装.NetCore SDK
官网下载地址：https://dotnet.microsoft.com/en-us/learn/dotnet/hello-world-tutorial/install
```bash
# 输入以下命令确认安装是否成功
dotnet -h
```

## 配置C#开发环境
1. VSCode插件安装
- C#
- C# Extensions
- C/C++

2. 创建文件夹并新建终端
```bash
dotnet new console -o HelloWorld
# 会生成obj文件夹、与文件夹同名的.csproj文件，Program.cs
# -o 指定目录，即在创建在当前目录中的HelloWorld目录中

dotnet run
# 会生成bin文件夹
```

3. 创建文件夹.vscode
按下Ctrl+Shift+P，选择.Net:Generate Assets for build and Debug
会自动生成文件launch.json和tasks.json

## 使用Code Runner插件执行C#
1. 安装
2. 打开VScode设置，输入run in terminal，勾选Code-runner:Run in Terminal
3. 打开右上角对应的代码
4. 输入code-runner.executorMap会自动插入所有支持的语言
5. 找到文件中CSharp所在并修改
    ```json
    "csharp": "cd $dir && dotnet run $fileName",
    ```
6. 保存后，右键运行