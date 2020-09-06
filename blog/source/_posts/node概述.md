---
title: node概述
date: 2020-07-29 15:19:15
categories:
- 前端
- node
tags:
- 前端
---
> [nodejs的中文社区](https://cnodes.org)

# 是什么
1. nodejs是 js runtime，用于<font color=red>解析执行js代码</font>，以前只有浏览器可以解析执行js代码
2. node.js不是一门语言，不是库，也不是框架
3. node是用来打开服务器黑盒的，除了node，其他的如java,PHP,Pyhton,Ruby,.Net都可以做到
具体的API操作可以直接查看官方文档
更多底层细节可参考：
>《深入浅出nodejs》
# 特性
1. event-driven
2. non-blocking I/O model,即异步
3. lightweight and efficient 
# 能做什么？
1. 开发web服务器后台
2. 命令行工具（webpack,gulp,npm)
# 安装及常用命令
查看版本号：
```bash
node --version
```
执行文件:<u>文件不能用node.js命名</u>
```bash
node a.js
```
# 基本知识点
小🌰
```js
var a = 1
console.log(a)
```

- <font color=green>浏览器中的JS没有文件操作能力</font>；但是node中的js有,需要引入fs核心模块
- node不太关注样式，更关注服务端的操作
``` js
var fs = require('fs')
fs.readFile(path, function (error, data){
    console.log(data.toString())          // 因为data为16进制数据，不可读,所以需要toString()
}) 
```
第二个参数是回调函数，如果成功data有数据，error为null;如果失败，data为undifined, error有数据
``` js
fs.write(peth, string, function (error){
    if (error){
    }
    else{
    }
}) 
```
- npm是用nodejs开发的包管理工具，绝大多数的JS相关的包都存放在了npm上
- 浏览器中的`JS == ES + BOM + DOM`
- Node.js中的JS：<font color=green>只有ES，没有BOM，没有DOM</font>
- Node主要就是使用ES，核心模块进行编程
## 零散
LTS:long time supprt
可以直接在浏览器的console 或者 node命令进行API测试
node中核心模块可以直接使用，不用require
node中字符串中涉及到\需要用\\，因为会被转义
安装指定版本的包 
```bash
npm i jquery@1.11.0
```
## 8. 修改代码自动重启
通过第三方命令行nodemon解决频繁修改代码后，频繁重启服务器的情况
安装：
```bash
npm install --global nodemon
nodemon --version     # 验证是否安装成功
```
使用：用`nodemon`代替原来的`node`，使用nodemon启动的服务，会自动监视代码的变化，然后重启服务器.
原来：
```bash
node app.js
```
现在：
```bash
nodemon app.js
```