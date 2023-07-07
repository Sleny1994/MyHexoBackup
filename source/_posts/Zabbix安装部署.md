---
title: Zabbix安装部署
date: 2023-07-03
tags: 
- Linux
- Zabbix
- SQL
- OM
categories:
- OM
---

## 环境
- 服务器：Centos 8 Stream
- 版本：6.4
- 组件：Server、Frontend、Agent
- 数据库：PostgreSQL
- Web：Nginx + PHP7.4

## 安装
1. 安装Zabbix依赖
```bash
rpm -Uvh https://repo.zabbix.com/zabbix/6.4/rhel/8/x86_64/zabbix-release-6.4-1.el8.noarch.rpm
dnf clean all
```
2. 切换PHP版本
```bash
dnf module switch-to php:7.4
```
3. 安装Server、Web前端和代理
```bash
dnf install zabbix-server-pgsql zabbix-web-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-selinux-policy zabbix-agent
```
4. 创建初始数据库
```bash
# Make sure you have database server up and running.
sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```
5. 配置数据库
```bash
# Edit /etc/zabbix/zabbix_server.conf
DBPassword=password
```
6. 配置PHP
```bash
#  Edit /etc/nginx/conf.d/zabbix.conf uncomment and set 'listen' and 'server_name' directives
listen 8080;
server_name example.com;
```
7. 启动Server和Agent进程
```bash 
systemctl restart zabbix-server zabbix-agent nginx php-fpm
systemctl enable zabbix-server zabbix-agent nginx php-fpm
```