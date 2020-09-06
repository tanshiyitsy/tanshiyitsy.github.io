---
title: CRUD
date: 2020-07-29 19:56:37
categories:
- 前端
- node
tags:
---
- CRUD:
Create, Read, Update, Delete
# 持久化数据
1. 样式准备
```bash
npm i -S bootstrap      # 安装框架bootstrap
```
index.html
```html
<!--html中的循环-->
<div>
    {{ each fruits }}
        <h4> {{ $value }} </h4>
    {{ /each }}
</div>
<!--按钮-->
<a class="btn btn-success" href=""> add student </a>
```
app.js
```js
app.get('/fruits', function(req, res){
    res.render('index.html', {
        fruits: [     // 这些数据是放在内存中，做持久化需要放在文件，数据库中
            'apple',
            'pear',
            'banana'
        ]
    })
})
```
2. 持久化后的
db.json
```json
{
    "students":[
        {
             "id":1,
             "name", "张三",
             "gender":0，   // 用数字不用汉字原因，数字占用内存更小
             "age":12,
             "hobbies", "吃饭，睡觉，打豆豆"
        },
        {},{},{},{}
    ]
}
```
app.js 读取文件内容
```js
var fs = require('fs')
app.get('/', function(req, res){
    /* 
       第二个参数可选
       读取文件默认为二进制数据，utf-8将其转换为utf-8编码
       如果没有第二个参数，需要通过
       data.toString()方式转换为字符串
    */
    fs.readFile('./db.json', 'utf-8', funciton(err, data){
        if(err){
            return res.status(500).send('Server error')
        }
        console.log(typeof data)    // 输出string
        res.render('index.html', {
            students： JSON.parse(data).students // 这里将字符串转换为对象，然后将对象里的students属性拿出来
        })
    })
})
```
index.html 模版
```html<tbody>
    {{ each students }}
        <tr>
            <td>{{ $value.id }}</td>
            <td>{{ $value.name }}</td>
            <td>{{ $value.gender }}</td>
        </tr>
    {{ /each }}
</tbody>
```
# 路由设计及解耦路由和服务

|请求方法 |请求路径|get参数|post参数|备注|
|-------|-------|------|------|----|
|GET|students| |    | 渲染首页|
|GET|/students/new| |  |渲染添加学生首页|
|POST|/students/ new|  | name,age,gender,hobbies|处理添加学生信息|
|GET|/students/edit|id| |渲染编辑页面|
|POST|/students/edit| |id,name,age,gender,hobbies|处理编辑请求|
|GET|/students/delete|id| |  处理删除请求|
app.js 入口文件
```js
var express = require（'express')
var router = require('./router')    // 单独把路由模块拿出来
var app = express()
router(app)
app.lister(3000,function(){
    console.log('running.......)
})
```
router.js
传入参数以后，里面的路由模块就可以用了
```js
module.exports = function(app){
    app.get('/students', function(req, res){

    })
}
```
但是这种方式需要自己封装函数，express提供了更好的方式
router.js
职责：
根据不同的请求方法&&请求路径，处理路由
```js
var express = require('express')    // 这里引入express是为了用Router
// 1. 创建路由容器
var router = express.Router()
// 2. 把路由挂载到router路由容器中
router.get('/students', function(req, res){   // 这里只是把原来的app换成router

})
// 3. 导出router
module.exports = router
// 谁加载该文件，谁就得到router容器
```
app.js
作用：
1. 创建服务
2. 做一些服务相关配置（如模板引擎，body-parser，提供静态资源服务）
3. 挂载路由
4. 监听端口，启动服务
```js
var express = require('express')
// 1. 得到router
var router = require('./router')
var app = express()
// 2. 把路由容器挂在到app服务中
app.use(router)
```

# CRUD
router.js
```js
var express = require('express')
var fs = require('fs')
var router = express.Router()
router.get('/students', function(req, res){
    //...渲染首页，代码可参考 持久化部分的数据
})
router.get('/students/new', function(req, res){

})
router.post('/students/new', function(req, res){

})
router.get('/students/edit', function(req, res){

})
router.post('/students/edit', function(req, res){

})
router.get('/students/delete', function(req, res){

})
modules.exports = router
```
index.html
```html
<a class="btn btn-success" href="/students/new"> add students </a>
<tbody>
    {{ each students }}
    <tr>
        <td>{{ $value.id }}</td>
        <td>{{ $value.name }}</td>
        <td>{{ $value.gender }}</td>
        <td>
            <a href="/students/edit?id={{ $value.id }}">编辑</a>
            <a href="/students/delete?id={{ $value.id }}">删除</a>
        </td>
    </tr>
</tbody>
```
students.js
职责：封装students的数据操作模块；只处理数据，不关心业务
```js
var fs  = require('fs')
//获取所有学生列表
dbPath = './db.json'
exports.find = function(callback){
    fs.readFile(dbPath， function(err, data){
        if (err){
            return callback(err)
        }
        callback(null, JSON.parse(data).students)
    })
}
// 添加保存学生
exports.save = function(student, callback){    # JSON参数student是一个对象
    fs.readFile(dbPath, 'utf-8', function(err, data){
        if (err){
            return callback(err)   # 让业务方处理错误,把错误往上层抛出
        }
        var students = JSON.parse(data).students   # 拿到新增的student信息
        student.id = students[students.length - 1].id + 1   # 处理ID
        students.push(student)       # 加到数组
        var fileData = JSON.stringify({   # 将对象转换为字符串
            students: students
        })
        fs.write(dbPath, fileData, function(err){
            if (err){
                return callback(err)
            }
            callback(null)   # 成功就没错，错误对象就为null
        })
    })
}
// 更新学生
exports.updateById = function(student, callback){
    fs.readFile(dbPath, 'utf-8', function(err, data){
        if (err){
            return callback(err)
        }
    })
    var students = JSON.parse(data).students
    student.id = parseInt(student.id)
    /*
        ES6中的一个数组方法：find
        接收一个函数作为参数
        当某个遍历项符合
        item.id === student.id时，find会终止遍历，同时返回子项
    */
    var stu = students.find(function(item){
        return item.id === student.id
    })
    # 循环修改，遍历拷贝对象
    for (var key in student){
        stu[key] = student[key]
    }
    # 。。。下面就是把新的字符串students保存在文件当中
})

// 删除学生
exports.deleteById = function(id, callback){
    fs.readFile(dbPath, 'utf-8', function(err, data){
        if(err){
            return callback(err)
        }
        var students = JSON.parse(data).students
        # 这个函数是返回符合条件的下标
        var deleteId = students.findIndex(function(item){
            return item.id === parseInt(id)
        })
        # 根据下表删除对应的学生对象，第一个表示从下标开始删；第二个表示删除一个；如果没有第二个参数，表示删到末尾
        students.splice(deleteId, 1)
        # 。。。再把新的students重写文件
    })
})
// 查找某个student
exports.findById = function(id, callback){
    fs.readFile(dbPath, 'utf-8', function(err, data){
        if (err){
            return callback(err)
        }
        var students = JSON.parse(data).students
        var ret = students.find(function(item){
            return item.id === parserInt(id)
        })
        callback(null, ret)
    })
}
```
ES6对数组新增了许多方法（find，findIndex等）
find接收一个方法作为参数，方法内部返回一个条件
find会遍历所有元素，判断给定的条件
如果遍历结束，还没有符合条件的元素，则返回`undefined`
router.js
```js
var Student = require('./students.js')
router.get('/students', function(req, res){
    Student.find(function(err, students){
        if (err){
            return res.status(500).send('Server error.')
        }
        res.render('index.html', {
            students: students
        })
    })
})
router.post('/studetns/new', function(req, res){
    Students.save(req.body, function(err){
        if (err){
            return res.status(500).send('Server erro')
        }
        res.redirect('/students')
    })
})
router.get('students/edit', function(req, res){
    Student.findById(parseInt(req.query.id), function(err, student){
        if(err){
            return res.statusCode(500).send('Server error')
        }
    })
})
```
注意：如果需要获取函数中异步操作（setTimeout，readFile，writeFile，ajax）的结果，则必须通过回调函数来获取
<font color=#CD5555>一般异步函数都伴随一个回调函数</font>
```js
function fn(callback){
    setTimeout(function(){
        var data = 'hello'
        callback(data)
    }, 1000)
}
fn(function(data){
    console.log(data)    // 这里获取异步操作的结果
})
```
- ajax
浏览器搜索mdn-->搜索xmlhttprequest


