---
title: mangoDB
date: 2020-07-29 21:42:52
categories:
- 前端
- node
tags:
---
# 关系型和非关系型数据库
**表就是关系**
- 所有的关系型数据库需要通过`sql`语言来操作
- 所有的关系型数据库操作之前需要设计表结构
- 关系型数据库支持约束（`唯一性、主键、默认值、非空`等）
非关系型数据库非常灵活，有的非关系型数据库存储的就是key-value对儿
- mongoDB是长得最像关系型数据库的非关系型数据库
    - 数据库--数据库
    - 数据表--集合（数组）
    - 表记录--文档对象
- MangoDB不需要设计表结构，即可以任意往里面存数据，没有结构性这一说
文档结构很灵活，没有任何限制
mongodb非常灵活，不需要像mysql一样先创建数据库、表、设计表结构；当插入数据的时候，只需要往哪个数据库的哪个集合操作就可以了，一切由mongodb自动帮忙完成
# 基本命令
- 查看版本，验证是否安装
```bash
mongod --version
```
- 启动：mongodb 默认使用执行`mongod`命令所处盘符根目录下的`/data/db` 作为自己的数据存储目录
所以在第一次执行该命令前，需要*手动新建目录/data/db*
```bash
mongod     # 执行该命令，即启动数据库
```
如果想要修改默认的数据存储目录，可以
```bash
mongod --dbpath=数据存储目录路径
```
停止：`ctrl+c` 或者 `关闭终端`
连接数据库：
```bash
mongo    # 该命令默认连接本地的mongodb服务
```
关闭：连接状态下输入exit退出连接
```bash
show tables：查看所有数据库
show dbs：
use db_name:切换到指定数据库（如果没有，则新建）
db：查看当前操作的数据库
db.students.insertOne({"name":"Jack"}):students代表集合，插入json数据，插入的必须是对象
show collections：结果为students
db.students.find():结果为{"id":xxxxx, "name":"Jack"}
```
# nodejs操作mongodb
可以通过原生方式连接
也可以通过第三方包：`mongoose`
```bash
npm i mongoose   # 安装
var mongoose = require('mongoose');
# 连接test数据库
mongoose.connect('mongodb://localhost/test', { useMongoClient:True }};
# 创建一个模型，就是在设计数据库
# MongoDB是动态的，非常灵活，只需要在代码中设计数据库就可以了
# 实际上的集合名称为小写&&复数，即为cats
var Cat = mongoose.model('Cat', { name: String }};
# 实例化一个cat
var kitty = new Cat({ name: "zidfsf" });
# 持久化保存Kitty实例
kitty.save(function(err){
    if（err){

    }
    else{

    }
})
db.cats.find()  # 命令行这里查看内容
```
# 小示例
这些可以查找[官网API](https://mongoosejs.com/docs/guide.html)
```js
var mongoose  = require('mongoose')
var Schema = mongoose.Schema
// 1. 连接的数据库不需要存在，当插入第一条数据之后会被自动的创建出来
mongoose.connect('mongodb://localhost/itcast')
// 2. 设计集合结构
// 数据类型就是JS支持的类型
// 约束的目的是保证数据的完整性，不产生脏数据
var userSchema = new Schema({
    username:{
        type: String,
        required: true 
    },
    password:{
        type: String,
        required: true
    },
    email:{
        type: String
    }
});
// 3. 将文档结构发布为模型
// mongoose.model方法就是将一个model发布为model
// 第一个参数：传入大写开头的&&单数 字符串表示数据库，mongoose会自动转换为小写&&复数的集合名称
// 第二个参数：架构schema
// 返回值：模型构造函数
var User = mongoose.model('User', userSchema)
// 4.可以对模型构造函数中的数据增删改查了
// create
var admin = new User({ # new 一个实例
    username: 'admin',
    password: '123456',
    email: 'admin@admin.com'
})
admin.save(function(err, ret){ # 持久化数据
    if(err){
    }
    else{
        console.log(ret)    // ret就是刚刚插入的数据
    }
})
// Read
// 通过模型构造函数来查
User.find(function(err, ret){
    if (err){}
    else{
        console.log(ret)  // ret就是查询到的数据，这里查到的是所有数据；结果为数组类型
    }
})
User.find({
    username: 'zs'   // 按条件查询
},function(err, ret){

})
User.findOne({  // 查找符合条件的第一个，返回类型为对象
    username: 'zs'   // 按条件查询
},function(err, ret){

})
# Update
User.findByIdAndUpdate({},function(err, data){})
# Delete
User.remove({
    username:'zs'
},function(err, data){})
```
