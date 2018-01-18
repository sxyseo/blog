---
title: >-
  Tomcat运行一段时间后报错cache eviction process was unable to free [10]
date: 2018-01-18 17:50:25
tags:
---


##### 应用运行一段时间后崩掉了。查看日志记录如下:
```
15-Jan-2018 16:56:55.801 INFO [ContainerBackgroundProcessor[StandardEngine[Catal
ina]]] org.apache.catalina.webresources.Cache.backgroundProcess The background c
ache eviction process was unable to free [10] percent of the cache for Context [
] - consider increasing the maximum size of the cache. After eviction approximat
ely [24,460] KB of data remained in the cache.
15-Jan-2018 16:57:05.810 INFO [ContainerBackgroundProcessor[StandardEngine[Catal
ina]]] org.apache.catalina.webresources.Cache.backgroundProcess The background c
ache eviction process was unable to free [10] percent of the cache for Context [
] - consider increasing the maximum size of the cache. After eviction approximat
ely [24,460] KB of data remained in the cache.
15-Jan-2018 16:57:15.819 INFO [ContainerBackgroundProcessor[StandardEngine[Catal
ina]]] org.apache.catalina.webresources.Cache.backgroundProcess The background c
ache eviction process was unable to free [10] percent of the cache for Context [
] - consider increasing the maximum size of the cache. After eviction approximat
ely [24,460] KB of data remained in the cache.
15-Jan-2018 16:57:25.827 INFO [ContainerBackgroundProcessor[StandardEngine[Catal
ina]]] org.apache.catalina.webresources.Cache.backgroundProcess The background c
ache eviction process was unable to free [10] percent of the cache for Context [
] - consider increasing the maximum size of the cache. After eviction approximat
ely [24,460] KB of data remained in the cache.

```
##### 日志中记录并给出了建议：
```
consider increasing the maximum size of the cache
```
也就是增加最大缓存。

打开tomcat下的conf文件下(tomcat_home/conf/Catalina/localhost/里的context文件里)
的context.xml在文件尾部</Context>前增加添加这么一行：
```
<Resources cachingAllowed="true" cacheMaxSize="100000" />
```
把cacheMaxSize调大就行了。

##### 群友说可能是代码的问题，正在排查中。
最后来个群友发的段子。

```
我今天突然觉得递归可以这样通俗的表达出来：
两个人玩成语接龙，第一个人说：为所欲为。
第二个人说：为所欲为。
......
```