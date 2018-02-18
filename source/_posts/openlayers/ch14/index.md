---
title: OpenLayers 3 注意事项
date: 2018-01-18 20:29:59
tags: openLayers
categories: openLayers
---

# 注意事项

1. EasyUI和OL3有冲突，体现在浏览器大小缩放时，地图会变形，具体原因未知。

<!-- more -->

1. `ol.js`这个文件**千万不能**使用源码目录中的`src\ol\ol.js`。请下载官网的`*-dist.zip`，用解压后文件夹里面的`ol.js`。