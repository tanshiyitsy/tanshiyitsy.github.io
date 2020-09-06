---
title: express
date: 2020-07-29 18:10:06
categories:
- 前端
- node
tags:
---
原生http不足以应对开发需求，所以需要使用框架加快开发效率，提高效率，使得代码高度统一
Node中有许多web开发框架，express就是其中一种
> 官网首页：expressjs.com
# 基本使用
step1:安装：
```bash
npm i -S express 
```
step2:引包
```js
var express = require('express')
```
step3:创建服务器应用程序，也就是原来的`http.createServer`
```js
var app = express()
// 服务器收到GET请求/的时候，执行回调函数
app.get('/',function(req, res){
    res.send('hello,world')
})
app.get('/about', function(req. res){
    res.send('你好，世界')    # 中文该框架帮忙处理了
})
// 相当于原来的server.listen
app.listen(3000, function(){
    console.log('app is running at 3000')
})
```
提供静态资源服务，公开指定目录，可以直接通过/public/xxx的方式访问public文件夹中的资源了
app.use('/public',express.static('./public'))   # 开放public目录

step4:基本路由
路由：有分发的意思，其实就是一张表，表里有具体的映射关系
请求方法+请求路径+处理函数
```js
app
  .get('/', funciton_name)
  .post('login', funciton_name)
  .get('billing', function_name)     // 这个就可以被看为是一张路由表
```
静态文件处理：
app.use()的第一个参数是URL，浏览器访问时是第二个参数的别名，
./public目录下有login.html文件，下面表示以/public/开头时，去./public目录下找寻对应的文件
比如浏览器访问`xxx/public/login.html`就可以得到这个文件
```js
app.use('/public/', express.static('./public/'))
// 省略第一个参数，则访问时可以省略/public,浏览器只需要输入xxx/login.html,如果输入xxx/public/login.html则访问不到
app.use(express.static('./public/')) 
// 比较特殊的,必须通过/a/login.html来访问文件public 文件夹里面的资源，a相当于是public的别名
app.use('/a/', express.static('./public'))
```
# 在express中使用`art-template`模板引擎
## 基本使用
1. 安装
```bash
npm install --save atr-template
npm install --save express-art-template
```
2. 配置使用art-template
app.js
```js
var express = require('express')
var app = express()
app.engine('art', require('express-art-template'）） // 这里配置，第一个参数表示：当渲染以art结尾的文件时，使用art-template；
// express为response对象提供了一个方法：render
// render方法默认不可用，需要配置模版引擎才可使用
// res.render('html模板名',{模板数据})；第一个参数不能写路径，默认会去项目中views目录下寻找，默认所有的视图文件都在views目录中
app.get('/', funciton(req, res){
    res.render('404.art')
})
app.get('/admin', function(req, res){
    res.render('admin/index.html', {
        title: '管理系统'
    })
})
# 如果想要修改render函数默认的views目录，可以
app.set('views', path)
app.get('/', function(req, res){
    res.send()    # send是express提供的，end是原生node提供的
})
```
- 这里用art 只是为了更好识别使用了art-template，可以自定义
- express-art-template：这个包专门用于express框架
- 虽然外面不需要加载art-template包，但是也必须安装，因为express-art-template包依赖了art-template
index.html
```html
<h1> admin {{ title }} </h1>
```

## express中实现留言板
### 原生语言实现
```js
app.get('/', function(req, res){
    res.render('index.html',{
        comments: comments
    })
})
app.get('/post', function(req, res){
    res.render('post.html')
})
app.get('/pinglun', function(req, res){
    var comment = req.query    // 这个就是用户输入的内容，只能获取GET方法的表单内容
    comments.unshift(comment)
    res.redirect('/') //express封装的；等价于原生nodejs的res.statusCode = 302; res.setHeader('Location',  '/')
})
```
### 优化版
- 对于POST方法，express没有提供获取表单内容的API，必须使用插件（在express官网中, resources->middleware中可查看）
- 凡是在浏览器输入地址回车，发送的都是GET请求
- POST需要使用`表单`或者`ajax请求`
1. 安装
```bash
npm install --save body-parser
```
2. 配置
```js
// 1. 引包
var bodyParser = require（'body-parser') 
// 2. 配置
// 只要加入这个配置，则在req请求对象上会多出来一个属性：body
// 因此可以直接通过req.body 获取POST表单的请求实体数据了
app.use(bodyParser.urlencoded({ extended: false }))
// 3. 解析
app.use(bodyParser.json())
```
同一路径使用多次，但是<font color=#FF6A6A>不同的方法</font>请求
```js
app.get('/post', function(req, res){
    res.render('post.html')
})
app.post('/post', function(req, res){
    var comment = req.body    // 获取POST的表单内容
    comment.dateTime = '2020-07-24 10:34:23'
    comments.unshift(comment)
    res.redirect('/')
})
```
## 处理定制404页面
- express对于没有设定的请求路径，默认返回`Can not get xxx`
- 如果想要定制404，需要通过中间件配置,只需要在自己的路由之后增加一个
```js
app.use(function(req, res){
    //所有未处理的请求都会跑到这里
})
```





