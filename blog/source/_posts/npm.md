---
title: npm
date: 2020-07-29 17:28:58
categories:
- 前端
- node
tags:
---
# 是什么
1. node package manager
> 网站：npmjs.com
2. 第二层含义是：命令行工具
```bash
npm --version  # 查看版本
npm install --global npm   # 升级自己
```
# package.json
- 是包说明文件
- 该文件可以通过`npm init`方式初始化出来
```bash
npm install art-template --save
或者
npm install --save art-template  # --save选项会在package.json文件里记录安装的包信息
```
如果不小心将node_modules文件夹删除了，package.json会记录项目依赖的第三方包，可以直接通过`npm install`命令安装package.json文件里dependency的`所有`依赖项

# package.json和package-lock.json
## 概念
- `npm5`以前没有package-lock.json这个文件，npm5以后加入的
- 安装包时，会自动`创建`或者`更新`package-lock.json这个文件
- npm5以后版本安装包，不需要加`--save`参数，会自动保存依赖信息
- package-lock.json会保存node_modules中<font color=red>所有包</font>的信息（版本、下载地址等）,包括依赖的依赖
  - 这样的话，npm install的速度会提升
  - lock可以用于`锁定版本`，防止自动升级新版。
  - 比如package.json中jquery的版本为`^1.11.1`,下载时会下载高于1.11.1版本的最新版本的包，而lock文件，指定多少版本，就是多少版本的

## 常用命令
```bash
npm init
    npm init -y # 跳过向导，直接生成
npm install     # 下载package.json文件里面的依赖项
npm install package_name
npm install package_name --save   # 下载并保存依赖项 
    简写：npm i -S package_name
npm uninstall package_name    # 只删除，如果有依赖项依然保存
npm uninstall --save package_name   # 删除同时删除依赖信息
    简写：npm un -S package_name
npm --help
npm uninstall --help  # 查看命令uninstall的帮助
npm config list    # 查看npm配置信息 
```
npm的存储包文件的服务器在国外，有时候被墙，速度非常慢
镜像就是备份的意思
安装淘宝的cnpm
```shell
npm install --global cnpm    # 在任意目录下执行均可，--global表示全局安装
```
安装时把npm换成cnpm
```bash
# 这里是国外服务器，速度较慢
npm install jquery
# 这里是从淘宝镜像下载，淘宝十分钟和国外同步一次
cnpm install jquery
```
