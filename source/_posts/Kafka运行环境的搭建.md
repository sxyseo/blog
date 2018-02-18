---
title: Kafka运行环境的搭建
date: 2018-02-08 16:52:05
tags:
---

##### 准备学习下Kafka神器，Kafka是用Scala语言开发的，首先需要安装JDK环境。

Java环境还是比较容易配置的，目前系统是Win10.

先到官网下载JDK，这里下载的是JDK 1.8，下载后双击运行安装，选择路径。

桌面此电脑右键选择属性，打开高级系统设置，点击环境变量，在系统变量中新增变量名JAVA_HOME，变量值为 JDK 1.8 安装路径，也就是刚刚安装的路径。由于默认安装在 Program Files目录下，目录名之间有空格，有可能在运行某些应用时因JDK安装路径有空格而报错，这个有点坑。
<!-- more -->
再新增变量名 CLASSPATH，变量值为
```
.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
```
最后的结果如下：
```
系统变量	JAVA_HOME	C:\Program Files\Java\jdk1.8.0_131
用户变量	CLASSPATH	.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
新增PATH变量     PATH   %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
```
设置好了后，重新打开一个终端，运行
```
java -version
```
显示类似下面结果，说明JDK环境搭建成功。
```
java version "1.8.0_131"
Java(TM) SE Runtime Environment (build 1.8.0_131-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.131-b11, mixed mode)
```

类似的有maven环境，tomcat环境，Java开发必备，当然也可以Docker。

还有Git环境。

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：

```
ssh-keygen -t rsa -C "youremail@example.com"
```

你需要把邮件地址youremail@example.com换成你自己的邮件地址，然后一路回车，使用默认值即可，由于这个Key也不是用于军事目的，所以也无需设置密码。

如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。


