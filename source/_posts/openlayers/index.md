---
title: openlayers
date: 2018-01-18 20:00:31
tags: openLayers
toc: true
---

# OpenLayers 3 Primer

## 介绍
由于在学习OpenLayers3，有缘看到OpenLayers 3 Primer教程，真是很荣幸。转到博客，仅仅方便地铁上阅读，学习。
这是一个关于OpenLayers 3使用的入门教程。帮助初学者逐步认识OpenLayers 3，理解地图组成，以及各个组成部分的作用。在此基础上，逐个突破，结合大量的实例，指导大家应用OpenLayers 3开发出符合业务的地图。在这个过程中，辅以对应的理论知识，学习了解OpenLayers 3背后的原理，尽量做到知其然且知其所以然，为更高级的OpenLayers 3开发打下坚实的基础。本教程将坚持实用为主，对应用中遇到的重点、难点加以详细讲解，提出实用方案。

为了满足广大初学者的需要，教程将坚持一个原则：即以**浅显易懂**为基础，以**有效实用**为最终目标的原则。

<!-- more -->

## 为什么要写这样一个教程
故事是从我学习OpenLayers 3开始的。那时版本才3.0.0，当时学习OpenLayers 3很快就入手了，没有遇到任何障碍。 但后来发现有些初学者并不是这样的，都不知道怎么入手，遇到很多问题，我在回答了很多类似问题后，开始寻找入门难的原因。 总结下来有两个主要原因：
* 第一，虽然官网有关于每个类和方法的API文档，也有相关的例子，但就是没有一个系统的教程把他们串起来，对着例子虽然能做出一些类似的业务，但并不能灵活应用，自然会遇到很多问题。 同时国内目前并没有一本关于OpenLayers 3学习的书籍或教程，这是比较让人遗憾的事。庆幸地是国外的同仁已经写了两本英文书籍，一本是[OpenLayers 3: Beginner's Guide](https://www.packtpub.com/web-development/openlayers-3-beginner%E2%80%99s-guide)，需要付费；另一本是[The book of OpenLayers 3](https://leanpub.com/thebookofopenlayers3)，需要付费，但同时也支持开放。但对于国内开发者而言，还是中文更加可读。 (国外最近又出了两本需要付费的书，一本是[Mastering Openlayers 3](https://www.packtpub.com/web-development/mastering-openlayers-3)，这本书内容涵盖也比较全面，对英文没有障碍的初学者，可以学习。 另一本是[OpenLayers 3.x Cookbook - Second Edition](https://www.packtpub.com/web-development/openlayers-3x-cookbook-second-edition))
* 第二，使用OpenLayers 3的初学者并不都是GIS专业出身，有很大一部分是前端开发者，或者其他领域的开发者，GIS相关的理论知识欠缺。同时，还有一些开发者连JavaScript语言也不熟练，API文档都不知道如何阅读。 但由于任务或者项目压身，不得不使用Openlayers开发，自然会遇到很多困难。

为了解决这个问题，我写过一篇[OpenLayers 3 入门指南](http://www.jianshu.com/p/6785e755fa0d)，一开始想当然地认为可以解决入门问题，直到有一天在群里来了一个初学者，他看了这篇文章后，觉得并没有什么卵用。这时我才意识到问题的严重性，问题没解决，反而多了一篇垃圾。因为入门者需要的是更为具体的东西。 在征集了大多数人的意见后，我决定写这本教程（在此一并感谢QQ群里那些提出意见，给予帮助的帅哥美女们）。目前国内存在很多OpenLayers 3的爱好者和使用者，我所在的QQ群就有将近千人，同时还存在很多其他关于OpenLayers的群，后续还会有更多的开发者使用它。

本教程作为一次尝试，我愿意做第一本国内的OpenLayers 3教程，希望能在开放的情况下，真正帮助到国内OpenLayers 3的初学者入门，虽然这并不容易，但我愿意挑战自己，并加入到开源的潮流中去，以此向OpenLayers 3这个优秀的开源引擎致敬，向它的开发团队和所有贡献者致敬！

## 目标读者
本教程的主要目标读者是刚开始接触OpenLayers 3的应用开发者，但对它有一定了解的开发者也可以通过这个教程的后续部分来提升自己的理解和认识。同时希望对它有深入了解的开发者review本教程，提交issue或者PR。

## 环境
* OpenLayers 3的版本: 3.13.1
* 浏览器: chrome 47.0.2526.111(64bit)

### 关于环境，这边可能会使用openlayers 3或者4来实验和学习。

## 关于作者
QQ群 OpenLayers官方旗舰群[2] 里面的 **扯淡大叔**     
QQ: 11364382

### BTW
我和一帮天南海北的朋友们正在开发一个开源的WEB 3D GIS引擎[F3Earth](http://www.f3earth.com)，有兴趣的朋友可以了解一下，谢谢大家的支持。



# Summary
* [OpenLayers 3 介绍](./ch01/index.html)
* [一个简单的地图](./ch02/index.html)
* [OpenLayers 3地图](./ch03/index.html)
	* [初步解析地图组成](./ch03/index.html)
	* [地图所有组成部分](./ch03/index.html)
	* [看懂API](./ch03/index.html)
	* [结合API文档分析代码](./ch03/index.html)
	* [ol.Map的应用](./ch03/index.html)
		* [定制地图logo](./ch03/index.html)
		* [单页面多地图](./ch03/index.html)
		* [地图联动](./ch03/index.html)
		* [动态交换地图](./ch03/index.html)
* [View](./ch04/index.html)
	* [地图导航](./ch04/index.html)
	* [坐标](./ch04/index.html)
	* [坐标系及投影](./ch04/index.html)
	* [OpenLayers 3使用的坐标系](./ch04/index.html)
	* [ol.View的应用](./ch04/index.html)
		* [限制地图范围](./ch04/index.html)
		* [限制地图缩放级别](./ch04/index.html)
		* [自适配区域](./ch04/index.html)
* [Source和Layer](./ch05/index.html)
	* [加载瓦片地图](./ch05/index.html)
		* [最简单的加载在线地图](./ch05/index.html)
		* [万能瓦片地图加载秘籍](./ch05/index.html)
		* [加载离线瓦片地图](./ch05/index.html)
        * [瓦片加载的源码浅析与小结](./ch05/index.html)
	* [静态地图及应用](./ch05/index.html)
	* [加载WMS服务地图](./ch05/index.html)
	* [矢量地图](./ch05/index.html)
		* [获取加载后的所有feature](./ch05/index.html)
		* [坐标转换](./ch05/index.html)
		* [样式设置](./ch05/index.html)
	* [图层叠加及管理](./ch05/index.html)
* [LOD与分辨率](./ch06/index.html)
	* [LOD原理](./ch06/index.html)
	* [瓦片计算](./ch06/index.html)
	* [分辨率](./ch06/index.html)
	* [自定义瓦片地图及加载](./ch06/index.html)
* [图标及提示信息](./ch07/index.html)
	* [应用overlay](./ch07/index.html)
	* [动画图标](./ch07/index.html)
	* [style及应用](./ch07/index.html)
		* [设置图标位置](./ch07/index.html)
		* [根据层级放大缩小图标](./ch07/index.html)
		* [另类设置svg图标](./ch07/index.html)
		* [规则几何体图标](./ch07/index.html)
		* [用Canvas自绘图标](./ch07/index.html)
		* [动态改变图标](./ch07/index.html)
		* [文字标注](./ch07/index.html)
	* [styleFunction应用](./ch07/index.html)
	* [大量图标方案](./ch07/index.html)
	* [提示信息](./ch07/index.html)
* [事件](./ch08/index.html)
	* [一个简单的事件应用](./ch08/index.html)
	* [注销事件响应](./ch08/index.html)
	* [常用事件](./ch08/index.html)
	* [自定义事件及应用](./ch08/index.html)
* [Interaction](./ch09/index.html)
	* [内置交互方式介绍](./ch09/index.html)
	* [实现原理](./ch09/index.html)
	* [Feature选取之选中样式](./ch09/index.html)
	* [Feature选取之条件过滤](./ch09/index.html)
	* [Feature选取之取消选中](./ch09/index.html)
	* [绘制一条线](./ch09/index.html)
	* [绘图进阶](./ch09/index.html)
	* [总结](./ch09/09-08.md)
* [Control](./ch10/index.html)
	* [控件概览](./ch10/10-01.md)
	* [探究控件原理](./ch10/10-02.md)
	* [控件美颜](./ch10/10-03.md)
	* [自定义控件](./ch10/10-04.md)
* [动画](./ch11/index.html)
	* [动画简单应用](./ch11/11-01.md)
	* [动画高阶应用](./ch11/11-02.md)
	* [利用postcompose事件做动画](./ch11/11-03.md)
* [进阶实例](./ch12/index.html)
    * [通过wfs增删改查要素](./ch12/index.html)
      * [GeoServer环境配置](./ch12/index.html)
      * [配置数据源](./ch12/index.html)
      * [通过wfs查询要素](./ch12/index.html)
      * [通过wfs修改要素](./ch12/index.html)
      * [通过wfs添加要素](./ch12/index.html)
      * [通过wfs删除要素](./ch12/index.html)
* [常见问题](./ch13/index.html)
* [注意事项](./ch14/index.html)