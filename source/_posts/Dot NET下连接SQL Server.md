---
title: Dot NET下连接SQL Server
date: 2023-08-14
tags:
- CSharp
- SQL Server
categories:
- CSharp
---

## Appsetting.json配置
```c#
"ConnectionStrings":{
"DefaultConnection":"Server=192.168.152.135;Database=DncZeus;User Id=sa;Password=admin@123;Trusted_Connection=True;MultipleActiveResultSets=true;Encrypt=True;TrustServerCertificate=True;Integrated Security=false;"}

//Integrated Security = false      #登录失败，该登录名来自不受信任的域；程序与数据库不在同一台服务器上时设置
//Encrypt = True;TrustServerCertificate = True       #证书链是由不受信任的颁发机构颁发的
```