---
title: Epicor ERP UD Table Maintenance
date: 2023-07-19
tags: 
- ERP
- Epicor
categories: 
- ERP
- Epicor
---

## 如何添加一个UD Field
1. 在小计UD表维护中，选择对应的Table，然后选择文件>新建>New Columns
2. 输入对应的信息后，打开Epicor Administration Console
3. 选择Database Server Management，右键对应的数据库，选择Regenerate DataModel
4. 重新启动ERP服务器
5. 登录ERP，查看之前修改的UD Table，当右侧表格和数据模型均显示绿色同步即完成添加
6. 此时进入数据库查看对应的UD Table，可以查询到之前添加的字段