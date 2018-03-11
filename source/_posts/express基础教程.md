---
title: express基础教程
date: 2018-02-23 16:39:24
tags: express
---

##### express官网
http://expressjs.com/
```
npm install express --save
```
全局安装
```
npm install express -g
```
github地址
```
https://github.com/expressjs/express
```
<!--more-->

官方demo
```
var express = require('express')
var app = express()

app.get('/', function (req, res) {
  res.send('Hello World')
})

app.listen(3000)
```

用npm初始化一个项目。
```
npm init
```
安装express
```
npm install express --save
```

新建server.js
```
var express = require('express');

var app = express();

app.get('/', function(req, res){
    res.send('1111');
});

app.listen(3000);

console.log('listen 3000');
```
安装nodemon
```
npm i nodemon -g
```
运行
```
nodemon server
```
```
nodemon server
[nodemon] 1.14.12
[nodemon] to restart at any time, enter `rs`
[nodemon] watching: *.*
[nodemon] starting `node server.js`
listen 3000
```
访问`localhost:3000`可以看到页面显示1111

express官方API
```
http://expressjs.com/en/4x/api.html
```
查看右侧菜单栏，点击Request查看
```
app.get('/user/:id', function(req, res) {
  res.send('user ' + req.params.id);
});
```
或者
```
app.get('/user/:id', function(request, response) {
  response.send('user ' + request.params.id);
});
```
访问`http://localhost:3000/user/1`页面显示`user 1`
