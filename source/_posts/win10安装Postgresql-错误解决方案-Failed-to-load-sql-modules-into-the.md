---
title: win10安装Postgresql 错误解决方案 Failed to load sql modules into the
date: 2018-01-14 16:28:57
tags:
	- postgresql
	- win10
	- fail
---

#### win10安装postgrelsql安装了很多次都是报错，开始查明以为是权限的问题，然后就给文件夹赋予权限，以为快解决了，又出现了 Failed to load sql modules into the database cluster的错误，于是根据错误去继续寻找解决方案，ubuntu下分分钟就install了。最后翻看stackoverflow找到解决方案。
步骤如下：
I was getting this same error when trying to install PostgreSQL v9.4.4 on Windows 10 Pro. Starting with a solution hosted on Stack Exchange, I came up with the following steps that allowed the installer to run successfully:

```
1) Create a new user account, called postgres
2) Add the new account to the Administrators and Power Users groups
3) Restart the computer
    NOTE: I added step #3, since step #4 didn't work without it
4) Run a command prompt as the postgres user, using the command:
    runas /user:postgres cmd.exe
5) Run the installer from the postgres command window
6) Delete the postgres user account, as well as the user directory
    NOTE: I added step #6, since the postgres account is not required after installation
```
<!-- more -->

第二步的话，在win10下很简单，在此电脑（我的电脑）下右键选管理，右键-》新增用户（postgres），然后重启用原来的用户登陆。
注意第四步，可以按win+r打开运行命令，然后输入
```
runas /user:postgres cmd.exe
```

之后在cmd里面直接运行postgresql-10.1-3-windows-x64.exe，安装成功。
win10安装Postgresql 错误解决方案 Failed to load sql modules into the database cluster
win10安装Postgresql 错误解决方案 新增用户

![](https://www.whatled.com/content/uploadfile/201712/38de1513519156.png)
![](https://www.whatled.com/content/uploadfile/201712/704c1513519157.png)


建议访问：[stackoverflow](https://stackoverflow.com/questions/30689251/failed-to-load-sql-modules-into-the-database-cluster-during-postgresql-installat/)