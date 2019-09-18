---
title: 'express'
date: 2018-10-06 16:29:52
tags:
 - node.js
categories:
 - node.js
---
# express
保留了node.js原生的功能,添加了一些方法,增强原有的功能
## use
### 监听
```
const express = require('express');
var server = express();
server.use('/', function (req, res) {
  res.send({a: 1, b: 2})
})
server.listen(8080);
```
### 处理请求
```
server.use('adress', callback(req, res) {})
```
### 接受请求的方法
1. get('/', callback(req, res)) // 处理get
```
server.get('/', (req, res) => {
  req.query // get参数
})
```
2. post('/', callback(req, res)) // 处理post
```
需要body-parser 中间件
server.use(bodyParser.urlencoded({
  extended: false,  // 扩展模式
  limit: 2*1024*1024 // 限制
}))
server.post('/', (req, res) => {
  req.body // post参数
})
```

3. use('/', callback(req, res)) // 都能处理
   
### 读取文件

读取静态文件
```
const expressStatic = require('express-static');
server.use(expressStatic('./www')) 
```

### 链式操作

```
// 处理同一个接口地址
server.use('/', (req, res, next) {
  next(); // 继续下一步
})
server.use('/', (req, res, next) {

})
```
### 中间件

```
// lib/body.js
module.exports = function (req, res, next) {
  req.body = {}  // 自定义 req属性可以传递
  next() // 执行下一步
}

const body = require('./libs/body');
server.use(body)

server.use('/', (req, res) {
  console.log(req.body) // {}  通过 body 传递过来
})
```

### cookie

客户端保存数据
空间非常小
安全性非常差
校验cookie是否被篡改

#### 发送
```
res.secret = String // 签名秘钥
res.cookie('key', value, {
  path: '' // 向上继承
  maxAge: // 毫秒
  signed: Boolean
})

```
#### 读取
```
const cookieParser = require('cookie-parser');
server.use(cookieParser(secret))

server.use((req, res) => {
  req.cookies  // 未签名
  req.signedCookies // 签名
})
```
#### 删除

```
res.clearCookie(key)
```


### session

保存数据在服务端
不能独立存在,基于cookie
```
const cookieSession = require('cookie-session');
server.use(cookieParser());
server.use(cookieSession({
  name: '' 
  keys: [] // session keys
  maxAge: 
}))

server.use('/', (req, res) => {
  req.session
})

delete req.session
```

## jade

html模板引擎 改名为(pug)

```
const jade = require('jade');
var str = jade.renderFile('filename', {pretty: true});
```

### 语法
   根据缩进划分层级
1. 属性后面括号里面,多个逗号分隔
2. 内容空格写
3. style JSON写法和普通写法
4. class arr写法和普通写法
5. 简写 div.wrap - div#box -> div class="wrap" div id="box"
6. 复合属性json写法 tag&attributes
7. 自动识别单双标签
8. | 表示原样输出
9. tag + '.' 表示所有子代原样输出
10. -var 写js 循环,判断
```
html 
  head
    style
    script(src="a.js")
    script
      include 'filename'
    link(href="a.css", rel="relstylesheet")
  body
    div(style="width:100px;height: 200px")
    div(style={width: '100px', height: '200px'})
    div(class="left right active")
    div(class=['left', 'right', 'active'])
      ul(id='list')
        li 内容
        li&attributes({id: 'box', title: 'sss'})
        -var a = 0;
          
```



## ejs 

```
const ejs = require('ejs');
ejs.renderFile(', {变量}', (err, data) => {})
```

### 语法

```
<%= a %>
```

## body-parser
解析post数据


## multer

解析文件数据

```
var obj = multer({dest: '上传路径'})
server.use(obj.any())
server.use((req, res) => {
  req.files // arr originalname path
  // rename
  var newName = req.files[0].path + pathLib.parse(req.files[0].originalname).ext;
  fs.rename(req.files[0].path, newName, (err, data) => {
      if (err) {
          res.send('上传失败')
      } else {
          res.send('ok')
      }
  })
})
```

## consolidate

适配模板引擎

```
require('consolidate'); //引入
server.set('view engine', 'html') // 输出文件类型
server.set('views', '模板文件目录') // 模板文件位置
server.engine('html', consolidate.ejs) // 模板类型

server.get('/', (req, res) => {
  res.render('index.ejs', {name: '1111'});
})
```

## router (子服务)
路由 => 拆分成模块

```
// create
router = express.Router();
// add
server.use('/user', router);
// action
router.get() post() use()
```

## 数据库

### MySQL
免费 性能不错 中小型网站
Server端: 存数据
Client端: 客户端

####  数据库基本概念

两种单位
1. 库: 文件夹 - 管理
2. 表: 文件 - 存数据
3. 表 -> 行: 一条数据
4. 表 -> 字段(域): 一个数据项

主键: 唯一标识符 性能高

#### node使用mysql

1. 连接

```
const mysql = require('mysql');
mysql.createConnection(host, port, user, password,database);
```
2. 查询

```
db.query(SQL, (err, data) => {})
```
3. SQL (结构化查询语句)
关键字大写, 库 表 字段 加上``
4大查询语句: 增删改查
增: INSERT
```
INSERT INTO 表名 (字段列表) VALUES(值列表)
INSERT INTO `user_table` (`ID`, `username`, `password`) VALUES(0, 'xiaohong', '123');
```
删: DELETE
```
DELETE FROM 表 WHRER 条件
```
改: UPDATE
```
UPDATE 表 SET 字段-值, 字段-值... WHERE 条件
```
查: SELECT

```
SELECT what FROM 表
SELECT * FROM `user_table` WHERE 条件;
```

子句: 
WHERE 条件
 ```
 WHERE name = ''
 WHERE age > 18 AND score < 90
 WHERE cash > 100 OR score > 10000
 ```
 
ORDER 排序
```
ORDER BY age ASC/DESC
ASC - 升序 (从小到大)
DESC - 降序 (从大到小)

ORDER BY price ASC
ORDER BY price ASC, sales DESC
```

GROUP 聚类 - 合并相同
```
GROUP BY 
```
LIMIT - 限制输出 - 分页
```
LIMIT 10; 前10条;
LIMIT 5,8; 从5开始,取8条

// 分页 每页20条
第一页: 0, 20  0~19
第二页: 20, 20 20~39
第三页: 40, 20
```

子句之间有顺序关系
WHERE GROUP ORDER LIMIT
筛选    合并  排序   限制

## MD5
```
crypto node系统模块
var crypto = require('crypto');
var obj = crypto.createHash('md5');
obj.update('123456');
obj.digest('hex');
```
## END 

附上一份我自己搭建的express服务

```
// server.js
const express = require('express');
// 解析数据
// 1. 接口数据
const bodyParser = require('body-parser');
// 2. 文件数据
const multer = require('multer');
// cookie设置
const cookieParser = require('cookie-parser');
const cookieSession = require('cookie-session');
// 静态文件设置
const staticLib = require('express-static');
// mysql
const mysql = require('mysql');
// 模板引擎
const consolidate = require('consolidate');
// create listen
var server = express();
server.listen(8080);
// use the middle parser
server.use(bodyParser.urlencoded({extended: false}));
server.use(multer({dest: 'filepath'}).any());
server.use(cookieParser('secret'));
(function () {
    var arr = [];
    for(var i =0;i<100000; i++) {
        arr.push('keys_' + Math.random());
    }
    server.use(cookieSession({
        name: 'session_id',
        keys: arr, 
        maxAge: 2*60*60*1000 // 过期时间
    }))
})()
server.set('view engine', 'html');
server.set('views', 'temlate path');
server.engine('html', consolidate.ejs);
// use the child router
server.use('/admin', require('./route/admin/index'));
server.use('/', require('./route/web/index'));
// use the static file
server.use(staticLib('./www/'));

```