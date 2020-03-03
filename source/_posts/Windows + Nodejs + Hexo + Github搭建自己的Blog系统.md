---
title: Windows + Nodejs + Hexo + Github搭建自己的Blog系统
date: 2019-08-01
tags: 
- Hexo
- Node.js
- Github
- Blog
---

## Quick Start

1. 安装Nodejs
2. 安装Git
3. 安装Hexo
4. 在Github上创建repositories
5. 将本地Hexo文件deploy至Github
6. 访问Blog

## 安装Nodejs

1. 访问Nodejs官方网站：https://nodejs.org/en/download/ ,下载最新的Nodejs安装包。
2. 运行下载好的安装包，默认下一步安装即可。

<!--more-->

## 安装Git

1. 访问Git官方网站：https://git-scm.com/downloads/ ,下载最新的Git安装包。
2. 相对来说Git官网的下速度会比较慢，可以通过pc.qq.com查找Git安装包，再下载。
3. 和Nodejs相同，默认下一步安装即可。

## 使用NPM先安装CNPM

1. 由于npm是国外的源，安装起来会比较慢，这里推荐使用cnpm,其为淘宝的源。
2. 注意npm是跟随Nodejs一起安装的，类似Linux里的包管理器。
3. 打开Windows上的PowerShell程序（管理员权限运行）
4. 可以使用命令npm -v查看当前的版本信息。
5. 输入下面的命令安装cnpm：
   `npm install -g cnpm --registry=https://registry.npm.taobao.org`
6. 下载完后，可以通过cnpm -v查看当前的版本信息，如果报错，需要将cnpm的路径加入系统环境变量。

## 使用CNPM安装HEXO

1. 先创建一个空的文件夹，可以命名为My Blog，再在该文件夹下启动Power Shell程序（管理员运行）并输入下面的命令安装Hexo：
   `cnpm install -g hexo-cli`
2. 同样可以通过hexo -v命令查看版本信息。
3. 输入hexo init进行初始化操作，此处如若提示git巴拉巴拉的报错，是由于其未在Windows系统环境变量内添加对应的Path对象。
4. 将下列两个目录添加至环境变量的Path内即可：
   `C:\Program Files\Git\bin`
   `C:\Program Files\Git\mingw64\libexec\git-core`
5. 接着再执行hexo init命令。
6. 待初始化完成之后，可以从你创建的My Blog文件下看到一系列的文件。
7. 接着再执行hexo generate(可以简写为hexo g)释放静态文件。
8. 最后输入hexo server，启动Hexo
9. 使用浏览器根据命令提示窗口访问[http://localhost:4000](http://localhost:4000/)
10. 可以看到Hexo默认的博客页面，则代表安装Hexo成功。

## Github上创建repositories

1. 在Github中新建一个仓库。
2. 注意仓库的名字要与Github的用户名一致，否则会出现问题。

## 将Hexo部署到Github上

1. 修改Hexo的配置文件_config.yml，可以使用notepad++或者其它文本编辑器打开。

2. 在该配置文件的最下方有deploy字段，补充该字段，如下列所示（注意冒号后面有一个空格）：

   ```yaml
   deploy:
       type: git
       repo: https://github.com/Sleny1994/sleny1994.github.io.git
       branch: master
   ```

3. 注意将上述repo的地址更换成你自己的Github刚创建的仓库地址。

4. 输入cnpm install –save hexo-deployer-git命令，安装插件。

5. 待安装好插件后，输入hexo deploy，将Hexo部署至Github上。

6. 此时，Github上的Code区域下就生成了一系列的文件。

## 大功告成

1. 最后，使用浏览器访问你创建的Github仓库的地址。
2. 例如我的:[https://sleny1994.github.io](https://sleny1994.github.io/)