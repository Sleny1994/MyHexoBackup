---
title: AD域服务器开启NTP时间同步
date: 2023-10-26
tags: 
- AD
- Windoes
categories:
- Windows
---

## 查找合适的NTP服务器
在CMD命令行或Power Shell中输入命令`w32tm /stripchart /computer:[ntp server]`,若不提示错误即为成功可用
```bash
w32tm /stripchart /computer:ntp.aliyun.com

# 常见的NTP服务器
210.72.145.44 国家授时中心服务器IP地址
ntp.sjtu.edu.cn 202.120.2.101 上海交通大学网络中心NTP服务器地址
s1a.time.edu.cn 北京邮电大学
s1b.time.edu.cn 清华大学
s1c.time.edu.cn 北京大学
s1d.time.edu.cn 东南大学
s1e.time.edu.cn 清华大学
s2a.time.edu.cn 清华大学
s2b.time.edu.cn 清华大学
s2c.time.edu.cn 北京邮电大学
s2d.time.edu.cn 西南地区网络中心
s2e.time.edu.cn 西北地区网络中心
s2f.time.edu.cn 东北地区网络中心
s2g.time.edu.cn 华东南地区网络中心
s2h.time.edu.cn 四川大学网络管理中心
s2j.time.edu.cn 大连理工大学网络中心
s2k.time.edu.cn CERNET桂林主节点
s2m.time.edu.cn 北京大学
 
Africa — africa.pool.ntp.org (53)
Antarctica — antarctica.pool.ntp.org (0)
Asia — asia.pool.ntp.org (298)
Europe — europe.pool.ntp.org (2967)
North America — north-america.pool.ntp.org (960)
Oceania — oceania.pool.ntp.org (128)
South America — south-america.pool.ntp.org (61)
```

## 配置NTP服务器
```bash
w32tm /config /manualpeerlist:ntp.aliyun.com /syncfromflags:manual /reliable:yes /update

w32tm /config /update
net stop w32time
net start w32time
w32tm /resync /rediscover
w32tm /query /source
```

## 设置与NTP服务器时间同步
1. 添加时间服务器
打开注册表`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\DateTime\Servers`，右键新建字符串值，名称设置为`0`，数据输入：`ntp.aliyun.com`，将最上方默认数据修改为`0`
2. 指定时间源
打开注册表`HKEY_LOCAL_MACHINE\SYSTEM\Current\ControlSetServices\W32Time\Parameters`，修改键`NtpServer`的数据为：`ntp.aliyun.com`
3. 设置校时周期
打开注册表`HKEY_LOCAL_MACHINE\SYSTEM\Current\ControlSetServices\W32Time\TimeProviders\NtpClient`，修改键`SpecialPollInterval`的数据为十进制的`3600`（即3600s，60分钟）

## 设置权威服务器
1. 设置权威服务器
打开注册表`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W32TimeConfig`，修改键`AnnounceFlag`的数据为十进制的`10`
2. 启用NTP服务器
打开注册表`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W32Time\TimeProviders\NtpServer`，修改键`Enabled`的数据为十进制的`1`

## 配置组策略
1. 打开组策略管理，计算机配置-管理模板-系统-Windows时间服务，选择全局配置设置，选择已启用，修改`MaxNegPhaseCorrection`的值为`3600`，修改`MaxPosPhaseCorrection`的值为`3600`，修改`AnnounceFlags`的值为`5`，点击应用和确认
2. 打开组策略管理，计算机配置-管理模板-系统-Windows时间服务-时间提供程序，选择启用Windows NTP客户端，选择已启用，配置Windows NTP客户端，选择已启用，修改`NTP Server`的值为`ntp.aliyun.com`，修改`Type`的值为`NTP`，修改`SpecialPollInterval`的值为`3600`
3. cmd命令在AD域服务器和客户端内检测是否成功
```bash 
gpupdate /force              # 强制更新组策略
w32tm /query /source         # 查看时间服务器是否更改
w32tm /resync /rediscover    # 手工同步时间

# 强制同步主域控服务器时间的命令
net time \[AD Server IP] /SET /y
```

## 注意点
1. 强制更新组策略，可能没有效果，需要重新启动计算机或使用强制同步命令
2. 系统时间比标准时间系统时间晚14小时59分钟之内会同步失败
3. 系统时间比标准时间早30分钟之内会同步失败
4. 若部分客户端Windows Time服务自动停止，可以尝试重新注册该服务
```bash
W32tm /unregister
W32tm /register

# 开启w32tm debug的方法
w32tm /debug /enable /file:c:w32time.log /size:10000000 /entries:0-116
w32tm /debug /disable
```