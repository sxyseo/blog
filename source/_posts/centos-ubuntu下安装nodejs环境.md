---
title: centos ubuntu下安装nodejs环境
date: 2018-01-13 17:33:18
tags:
	- nodejs
	- node.js
	- centos
	- ubuntu
---

#### 申请了阿里云的主机，后续打算将博客转移到阿里云上，之前的腾讯云由于没有备案都闲置了，用作测试。

顺便从头记录node学习相关。

先安装一个node环境，到node官网下载node安装包
```
https://nodejs.org/zh-cn/download/
```
```
https://nodejs.org/dist/v8.9.3/node-v8.9.3-linux-x64.tar.xz
```

centos ubuntu下安装nodejs环境

由于是新的环境，一切都是新的。

首先cd到/temp下，wget https://nodejs.org/dist/v8.9.3/node-v8.9.3-linux-x64.tar.xz

然后解压node-v8.9.3-linux-x64.tar.xz文件(默认使用的root用户，非root用户命令前加sudo)。


```
xvf -d node-v8.9.3-linux-x64.tar.xz
mv node-v8.9.3-linux-x64 node
cp -R node /usr/local
```

将node-v8.9.3-linux-x64重新命名为node


再把node复制到/usr/local下。

之后配置环境变量，由于没有安装vim，先使用vi来编辑，

```
vi /etc/profile
```

在/etc/profile下追加node环境配置


```
export NODE_HOME=/usr/local/node
export PATH=$NODE_HOME/bin:$PATH
export NODE_PATH=$NODE_HOME/lib/node_modules:$PATH
```

然后按esc之后输入:wq（保存并退出）

让配置生效source /etc/profile

这样安装完成。可以使用node -v查看node版本，可以使用npm -v查看npm版本

安装cnpm或者换源。

#### 如果在centos下（ubuntu也是类似，只是命令不一样）要编译安装node

首先


```
yum -y install gcc make gcc-c++ openssl-devel wget
```

下载源码

```
wget https://nodejs.org/dist/v8.9.3/node-v8.9.3.tar.gz
```

解压
```
tar -zxvf node-v8.9.3-linux-x64.tar.gz
```

编译安装

```
./configure
make && make install
```
然后验证


```
node -v
```

安装pm2
```
https://github.com/Unitech/pm2
```


```
# General
$ npm install pm2 -g            # Install PM2
$ pm2 start app.js              # Start, Daemonize and auto-restart application (Node)
$ pm2 start app.py              # Start, Daemonize and auto-restart application (Python)
$ pm2 start npm -- start        # Start, Daemonize and auto-restart Node application
# Cluster Mode (Node.js only)
$ pm2 start app.js -i 4         # Start 4 instances of application in cluster mode
                                # it will load balance network queries to each app
$ pm2 reload all                # Zero Second Downtime Reload
$ pm2 scale [app-name] 10       # Scale Cluster app to 10 process
# Process Monitoring
$ pm2 list                      # List all processes started with PM2
$ pm2 monit                     # Display memory and cpu usage of each app
$ pm2 show [app-name]           # Show all information about application
# Log management
$ pm2 logs                      # Display logs of all apps
$ pm2 logs [app-name]           # Display logs for a specific app
$ pm2 logs --json               # Logs in JSON format
$ pm2 flush
$ pm2 reloadLogs
# Process State Management
$ pm2 start app.js --name="api" # Start application and name it "api"
$ pm2 start app.js -- -a 34     # Start app and pass option "-a 34" as argument
$ pm2 start app.js --watch      # Restart application on file change
$ pm2 start script.sh           # Start bash script
$ pm2 start app.json            # Start all applications declared in app.json
$ pm2 reset [app-name]          # Reset all counters
$ pm2 stop all                  # Stop all apps
$ pm2 stop 0                    # Stop process with id 0
$ pm2 restart all               # Restart all apps
$ pm2 gracefulReload all        # Gracefully reload all apps in cluster mode
$ pm2 delete all                # Kill and delete all apps
$ pm2 delete 0                  # Delete app with id 0
# Startup/Boot management
$ pm2 startup                   # Detect init system, generate and configure pm2 boot on startup
$ pm2 save                      # Save current process list
$ pm2 resurrect                 # Restore previously saved processes
$ pm2 unstartup                 # Disable and remove startup system
$ pm2 update                    # Save processes, kill PM2 and restore processes
$ pm2 generate                  # Generate a sample json configuration file
# Deployment
$ pm2 deploy app.json prod setup    # Setup "prod" remote server
$ pm2 deploy app.json prod          # Update "prod" remote server
$ pm2 deploy app.json prod revert 2 # Revert "prod" remote server by 2
# Module system
$ pm2 module:generate [name]    # Generate sample module with name [name]
$ pm2 install pm2-logrotate     # Install module (here a log rotation system)
$ pm2 uninstall pm2-logrotate   # Uninstall module
$ pm2 publish                   # Increment version, git push and npm publish
```