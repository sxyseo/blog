---
title: Hexo渲染时排除目录文件夹或部分文件
date: 2018-01-31 13:28:03
tags:
    - hexo
    - skip_render
---

#### 使用Hexo的时候，有时候需要写点Demo实验，发现Hexo也渲染了，那么怎么才可以让Hexo跳过目录或者部分文件，在查看_config.yml的时候发现

<!-- more -->

```
# Directory
# 资源文件夹，这个文件夹用来存放内容
source_dir: source
# 公共文件夹，这个文件夹用于存放生成的站点文件
public_dir: public
# 标签文件夹
tag_dir: tags
# 归档文件夹
archive_dir: archives
# 分类文件夹
category_dir: categories
# Include code 文件夹
code_dir: downloads/code
# 资源文件夹
assets_dir: assets
# 国际化（i18n）文件夹
i18n_dir: :lang
# 跳过指定文件的渲染，您可使用 glob 表达式来匹配路径
# skip_render: *.html
```
注意skip_render，跳过指定文件的渲染，您可使用 glob 表达式来匹配路径，把注释去掉，让此配置起效。

在提交网址的时候，搜索引擎确认网站所有权时(站长统计验证)需要下载一个验证文件来进行验证，
要是这个文件被渲染了，验证就可能会失败了。
或者，有时候会写一些简单的html示例页面，这也是不希望Hexo渲染的。因此有必要针对某个文件或者目录进行排除。
Hexo的配置文件中提供了配置项skip_render。

注意
只有source目录下的文件才会发布到public（能够在网络上访问到），因此Hexo只渲染source目录下的文件。skip_render参数设置的路径是相对于source目录的路径。

设置排除项
假设source目录下的文件如以下目录树所示,
Ubuntu下 tree 命令以树形结构显示文件夹目录结构，先安装tree
```
sudo apt-get install tree
```
只查看当前第一级的目录和文件
```
tree -L 1
```
```
.
├── assets
├── CNAME
├── demo
├── instagram
├── labs
├── photos
├── _posts
└── t

```
只查看当前第二级的目录和文件

```
tree -L 2
```
结果如下：
```
.
├── assets
│   ├── demo
│   └── img
├── CNAME
├── demo
│   ├── baidu.html
│   ├── google.html
│   └── index.html
├── instagram
│   └── index.md
├── labs
│   ├── index
│   └── index.md
├── photos
│   ├── index.ejs
│   ├── ins.css
│   ├── ins.js
│   ├── ins.json
│   ├── lazyload.min.js
│   └── tools.js
├── _posts
│   ├── centos-ubuntu下安装nodejs环境.md
│   ├── CNAME
│   └── hello-world.md
└── t
    ├── index.ejs
    ├── ins.css
    ├── ins.js
    ├── ins.json
    └── lazyload.min.js
```

排除单个文件
排除demo/index.html
```
skip_render: 'demo/index.html'
```

排除多个文件
排除baidu.html和google.html
```
skip_render:
  - 'demo/baidu.html'
  - 'demo/google.html'
```
或者

```
skip_render: '*.html'
```
后者会排除source目录下所有后缀为html的文件，但是不会排除子目录如demo及其子目录中的html文件。

排除demo目录下baidu.html和google.html以及index.html
```
skip_render:
  - 'demo/baidu.html'
  - 'demo/google.html'
  - 'demo/index.html'
```
或者
```
skip_render: 'demo/*.html'
```
demo目录下新增other目录，另外新增几个文件
```
mkdir demo/other
touch demo/other/test.html
touch demo/other/test1.html
touch demo/other/test2.md
```
排除source/demo/other目录中的所有html文件

```
skip_render: 'demo/other/*.html'
```
这不会排除test2.md文件

排除source/demo/other目录中的所有文件

```
skip_render: 'demo/other/**'
```
排除baidu.html和google.html以及整个source/demo目录

```
skip_render:
  - 'demo/*.html'
  - 'demo/other/**'
```
可以到public文件下查看
排除所有特定后缀的文件，使用 glob 表达式来匹配路径，那么什么是glob表达式呢？