---
title: node基本知识点
date: 2020-07-29 16:10:58
categories:
- 前端
- node
tags:
---
# node原生语言构建web服务器
## 服务器作用：
- 提供数据服务
- 发请求
- 接收请求
- 处理请求
- 发送响应
<font color=red>一次请求对应一次响应，只有一个res.end()</font>
## 构建服务器
```js
var http = require('http')             // 引入http核心模块
var server = http.createServer()        //该方法创建了一个web服务器，返回server实例
server.on('request', function(request, response){ 
    // 注册request请求事件，当客户端请求过来，触发request事件，执行回调函数
    // 参数1:request，请求对象，用于获取客户端的请求信息，如请求路径
    // 参数2:response,响应对象，用于给客户端发送响应信息
    console.log('收到客户端的请求了，请求路径为：' + request.url)
    // write用于给客户端发送响应数据，write可以使用多次，必须用end关闭，否则客户端一直等待
    response.write('hello')
    response.write('node.js')
    response.end()
})
// 绑定端口号, 启动服务器
server.listen(3000, function(){
    console.log('服务器启动成功，可以通过127.0.0.1:3000访问‘)
}) 
```
## 验证方法
- 验证方法：浏览器中输入127.0.0.1:3000
- <font color=red>响应内容只能是字符串或者二进制数据(Buffer)，`数字`，`对象`，`布尔值`，`数组`均不行</font>
- <font color=green>所以通过JSON.Sstringify()将参数转换为数组</font>
- <font color=green>JSON.parse()则相反</font>

# 模块
## 模块概念
- node一共三种模块
    1. 内置的核心模块:如fs,http,path,os等，通过`require`引入
    2. 自定义模块
    3. 第三方模块(如art-template,需要通过npm进行安装) 
- 核心模块本质是编译过后的`二进制文件`
- 第三方模块npm安装后会多出node_modules文件夹, 所以加载第三方模块的步骤
    1. 找到node_modules目录
    2. 找node_modules/art-template目录
    3. 找node_modules/art-template/package.json文件
    4. 找node_modules/art-template/package.json文件中的`main`属性（main属性中记录了art-template的`入口模块`）
    5. 如果package.json文件不存在，或者没有main属性，默认加载node_modules/art-template/目录下的`index.js`文件
    6. 如果上述条件均不成立，那么进入上一级目录中的node_modules查找，<font color=green>直至查找至根目录</font>如果未找到，则报错`"can not find module xxx"`
- 引入模块示例
b.js
```js
console.log('b执行了’);
```
a.js
```js
console.log('a start')
require('./b.js')        
console.log('a end')
```
引入模块时相对路径必须加`./`,不然会被当成核心模块或者第三方模块，后缀`.js`可以省略；
读文件时的./可以省略
输出
```bash
a start
b执行了
a end
```
node中没有全局作用域，只有模块作用域（只在本文件内有用）,模块间默认封闭
## require作用
1. 引入模块
2. 返回exports对象，exports默认为{}
exports是个对象，可以解决变量命名空间冲突的问题
如
b.js
```js
var foo = 'hello'
exports.foo = 'hello'
exports.add = function(x, y){
    return x + y
}
```
a.js
```js
var ret = require('./b.js')
console.log(ret.foo)
console.log(ret.add(1,2))
```
输出
```bash
hello
3
```
- 如果某个模块需要直接导出某个成员，而不是通过挂载的方式，那么只能用下面这种方法：
b.js
```js
module.exports = add
```
a.js
```js
var fooObj = require('./b.js')    // fooObj就等于add方法 
console.log(fooObj(1,2))
```
- 不可以直接exports = add，<font color=red>这样exports的引用变了,断开了和module.exports之间的引用</font>, 而返回的是`module.exports`
- 同理，给module.exports重新赋值也断开了和exports之间的引用
- node中，每个模块内部都有一个module对象和exports对象，exports默认为空
默认代码开始有：
```js
var module = {
    exports: {

    }
}
exports = module.exports   // 为简化编程，二者建立同一个引用关系
```
默认代码后面有:
```js
return module.exports
```
- remote.address, remote.port是访问服务器的客户端的IP：port

## 模块化
- 模块化概念
    - 具有文件作用域
    - 具有通信规则（可以加载，导出）
- 优先从缓存加载
如：
main.js
```js
require('./a.js')
require('./b.js')       // 这一句不会被执行，但是可以直接从缓存中拿到 b里面的接口对象
```
a.js
```js
console.log('a被加载了')
require('./b.js')
```
b.js
```js
console.log('b被加载了')
```
实际输出
```js
a被加载了
b被加载了    // 虽然多次被require，但是只输出一次，因为性能 ，避免重复加载
```
- JS天生`不支持`模块化, Node环境中对JS进行了特殊的模块化支持，被叫做`CommonJS`
- 浏览器中也可以像node中的模块一样进行编程
    - `<script>`标签来引用加载
    - require.js 第三方库，术语 AMD
    - sea.js   第三方库  术语CMD
无论是commonJS，AMD，CMD，UMD，ES6的modules官方规范，都是为了解决JS的模块化问题
    - commonJS，AMD，CMD是民间搞出来的
    - ES是官方规范定义
    - 所以ES6发布了新的官方标准，即语言天生可以支持模块化；但是虽然标准发布了，很多JS运行环境还不支持

# Content-type
- 服务端返回的数据是`utf-8`编码，所以中文也支持
- 但是浏览器不知道是utf-8,会按照当前操作系统的默认方式进行解码，中文操作系统默认是`gbk`编码，所以中文会乱码
```js
server.on('request', function(req, res){
    res.setHeader('Content-Type','text/plain;charset=utf-8')  // 可看官方文档
    res.send('世界')
})
```
text/plain:表示普通文本
text/html:会对标签进行解析渲染
```js
if (url === '/'){
}
else if (url === '/a'){
    fs.readFile('resource/a.html',function(err, data){
        if (err){
            res.setHeader('Content-Type','text/plain;charset=utf-8')
            res.end('文件读取失败，稍后重试')
        }
        else{
            res.setHeader('Content-Type','text/html;charset=utf-8')
            res.send(data)
        }
    })
}
```
不同资源对应的content-type不一样，可在[网址](https://tool.oschina.net/commons)查看;**图片可以不用指定编码**，一般只有字符数据需要编码

# 模板引擎
- `art-template`不仅可以在浏览器中使用，也可以在node中使用，是js引擎
参考官方文档下载方式
- {{ value }}:是`mustache`语法，八字胡语法
- html中这样使用:
```html
<script src="node_modules/art-template/lib/template-web.js"></script>
<script type="text/template" id="tpl">
我叫{{name}}
我今年{{age}}岁了
</script>
<script>
var ret = template('tpl',{
    name:"Jack",
    age:13
})
</script>
```
- node中使用模版引擎
tpl.html
```html
...
<p>我叫{{name}}</p>
<p>我今年{{age}}岁了</p>
...
```
a.js
```js
var template = require('art-template')
var fs = require('fs')
fs.readFile('./tlp.html',function (err, data){
    if (err){

    }
    else{
        var ret = template.render(data.toString(),{
            name: "Jack",
            age:13
        })
    }
})
```
- 服务端渲染：在服务端使用模板引擎,一次性返回给客户端页面+数据
- 客户端渲染：第一次从服务器拿到页面，从上到下依次执行，当发现有异步代码时，第二次从服务器拿数据，然后解析渲染
- 右键-->查看网页源代码，如果可以查到元素，说明是服务端渲染，如果看不到，说明是客户端渲染
- 点击下一页，整个页面刷新，则是服务端渲染；否则是客户端渲染
- 客户端，异步渲染速度更快
- 比如京东的商品是服务端渲染，评论是客户端渲染
- 异步，ajax渲染的数据，爬虫爬取不到；客户端渲染的数据不利于SEO，搜索不到，所以如果商品采用客户端渲染，虽然速度更快，但是搜索引擎搜索不到

# 原生语言实现小型留言板
浏览器收到html文件响应内容后，从上往下进行解析，解析过程中如果发现
`link`,`script`,`img`,`iframe`,`video`,`audio`等具有==src==或者==link==的href属性的时候，这些资源被称为静态资源，浏览器会自动的发请求
这里没有a链接，因为==a链接是由用户主动触发的==
app.js(主文件)
```js
var fs = require('./fs')
var http = require('http')
http
.server(function(req, res){
    res.end('hello')
    url = req.url
    if (url.indexOf('/public') == 0){ # 以public开头的url
        // 这里将url拼接成./的相对路径形式，资源放在public目录下
        fs.readFile('.'+url,function(err, data){
            
        })
    }
}).listen(3000, funciton (){
    console.log('running......')
})
```
index.html
```html
<script src='/public/css/bootstrap.css'></script>
```
浏览器解析到这行代码时会自动拼接成`http://domain/public/css/bootstrap.css`的请求
```js
var comments = [
{
    name: '张三',
    message: 'dgsgfhgfd',
    datetime: '2020-07-19'
},
{
    name: '李四',
    message: 'dgsfgfh',
    datetime: '2020-06-13'
},
{
    name: '王麻子',
    message: 'dfsgds',
    datetime: '2020-07-12'
}
]
var url = require('url')
// url.parse将路径转换为可操作的对象，通过query访问 
var parseObj = url.parse('req.url', true)
var pathname = parseObj.pathname  // 这里获取url中的路径，？之前
res.end(JSON.stringify(parse.query))
var comment = parse.query   // 评论采用的get方法，会被拼接至URL上
comments.push(comment)      // 添加至数组末尾
comments.unshift(comment)   // 添加至数组开头 
```
通过服务端设置重定向
1. 状态码设置为302
2. 响应头中设置location，告知客户端往哪儿重定向
3. 客户端发现状态码为302，会自动查找location
4. 用户可以看到浏览器自动跳转
```js
res.statusCode = 302
res.setHeader('Location', '/')
res.end()   // 发送完状态码和响应头，直接结束响应 
```
- 301:永久重定向，浏览器会记住，第二次浏览器会直接发送请求到重定向后的地址
- 302:临时重定向，浏览器不记忆 ，第二次浏览器还是会先请求服务器，再请求重定向后的地址