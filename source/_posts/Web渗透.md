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





