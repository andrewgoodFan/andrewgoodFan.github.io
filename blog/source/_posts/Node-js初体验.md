---
title: 'Node.js初体验'
date: 2018-08-08 15:12:50
tags:
 - node.js
categories:
 - node.js
---
## node 概念

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。 

## node request
`request`是`node`的服务器模块,用来处理请求
```
const request = require('request');
var server = http.createServer(function (req, res) {
    if (req.url === '/') {
        res.write('welcome to home');
    }
    res.end();
});
server.listen(8888);

```

## node path

提供用于处理文件路径和目录路径的实用工具。
```
const path = require('path');
var obj = path.parse('file');
    
{
    root: 'c:\\',
    dir: 'c:\\wamp\\www', // 文件路径
    base: 'a.html', // 文件名
    ext: 'html',  // 文件扩展名
    name: 'a' // 文件名
}
```

## node fs

操作文件系统，每个文件操作都有同步和异步形式
```
const fs = require('fs');
fs.unlink('filepath', (err) => {
  if (err) throw err;
  cosole.log('ok');
});
```
## get请求
```
const http = require('http');
const url = require('url');
http.createServer((req, res) => {
  var obj = url.parse(req.url, true);
  console.log(obj);
}).listen(8888);
```
## post请求

```
const http = require('http');
const questring = require('querystring');
http.createServer((req, res) => {
  // post - req
  var str = ''; 
  req.on('data', function (data) {
    str += data;
  })
  // 数据接收完成
  req.on('end', function () {
    var obj = questring.parse(str);
    console.log(obj);
  })
}).listen(8888);
```
## express

基于 Node.js 平台，快速、开放、极简的 Web 开发框架

## END
最终以官方文档为主，学习的时候可以对照官方文档，看对应的API教程。