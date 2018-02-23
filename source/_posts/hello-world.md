---
title: Hello World Hexo 命令笔记
tags:
	- hexo
	- hexo g
	- hexo d
	- hexo s
	- hexo p
	- hexo n
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

<!-- more -->

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)


#### Hexo简写
```
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo p == hexo publish
hexo g == hexo generate#生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy#部署
```
#### 服务器
```
hexo server #Hexo 会监视文件变动并自动更新，您无须重启服务器。
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP

hexo clean #清除缓存 网页正常情况下可以忽略此条命令
hexo g #生成静态网页
hexo d #开始部署
```
#### 监视文件变动
```
hexo generate #使用 Hexo 生成静态文件快速而且简单
hexo generate --watch #监视文件变动
```
#### 完成后部署
两个命令的作用是相同的
```
hexo generate --deploy
hexo deploy --generate
hexo deploy -g
hexo server -g
```
#### 草稿
```
hexo publish [layout] <title>
```
#### 模版
```
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub

hexo new [layout] <title>
hexo new photo "My Gallery"
hexo new "Hello World" --lang tw
```
#### 变量	描述
```
layout	布局
title	标题
date	文件建立日期
title: 使用Hexo搭建个人博客
layout: post
date: 2018-01-14 23:18:00
comments: true
categories: Blog
tags: [Hexo]
keywords: Hexo, Blog
description: 生命在于折腾，又把博客折腾到Hexo了。给Hexo点赞。
模版（Scaffold）
hexo new photo "My Gallery"
```
变量	描述
layout	布局
title	标题
date	文件建立日期
设置文章摘要
以上是文章摘要 <!--more--> 以下是余下全文 
写作
```
hexo new page <title>
hexo new post <title>
```
变量	描述
```
:title	标题
:year	建立的年份（4 位数）
:month	建立的月份（2 位数）
:i_month	建立的月份（去掉开头的零）
:day	建立的日期（2 位数）
:i_day	建立的日期（去掉开头的零）
推送到服务器上
hexo n #写文章
hexo g #生成
hexo d #部署 #可与hexo g合并为 hexo d -g
```
报错
1.找不到git部署
ERROR Deployer not found: git
解决方法
```
npm install hexo-deployer-git --save
```
3.部署类型设置git
hexo 3.0 部署类型不再是github，_config.yml 中修改
```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@***.github.com:***/***.github.io.git
  branch: master
```
4. xcodebuild
xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```
npm install bcrypt
```
5. RSS不显示
安装RSS插件
```
npm install hexo-generator-feed --save
```
开启RSS功能

编辑hexo/_config.yml，添加如下代码：

rss: /atom.xml #rss地址  默认即可

##### 添加站点地图
站点地图是一种文件，您可以通过该文件列出您网站上的网页，从而将您网站内容的组织架构告知Google和其他搜索引擎。Googlebot等搜索引擎网页抓取工具会读取此文件，以便更加智能地抓取您的网站

##### 安装插件
打开hexo目录下的dos命令行，分别安装百度和google插件
```
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```
##### 在博客目录的_config.yml中添加如下代码
##### 自动生成sitemap
```
sitemap:
path: sitemap.xml
baidusitemap:
path: baidusitemap.xml
```
##### 编译你的博客
