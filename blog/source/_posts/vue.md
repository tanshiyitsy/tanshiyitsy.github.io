---
title: vue
date: 2020-09-08 11:48:34
categories:
- 前端
- Vue
tags:
- 前端
---
# Vue基础
> 官网：https://cn.vuejs.org/  
> API文档：https://cn.vuejs.org/v2/api/

## 是什么：渐进式JS框架
1. 渐进式：声明式渲染-->组件系统 --> 客户端路由 --> 集中式状态管理-->项目构建
2. JS框架：提供基础性服务
2. 库和框架概念
	- 库以提供API为主，比如jQuery
	- 框架是提供基础性的服务
	- 但是目前二者并没有怎么区分

## 基本使用  
1. Vue基本使用步骤：  
    1. 提供标签用于填充数据
    2. 引入vue.js库文件（去官网上下载vue.js文件，然后放在工程目录文件js/下
    3. 使用vue的语法实现功能了
    4. 把vue提供的数据填充至标签里面
    相较于原生js,和jquery的优点：不需要操作DOM了
2. Vue代码运行原理：  
编译过程：`Vue代码-----（Vue框架）---->原生代码`
3. 使用实例
```html
<!DOCTYPE html>
<html>
<head>
	<title></title>
</head>
<body>
	<!--1. 提供标签-->
	<div id="app">
		<!--4. 插入数据，语法结构：插值表达式-->
		<div>{{msg}}</div>
	</div>
	<!--2. 引入vue.js库文件-->
	<script type="text/javascript" src="js/vue.js">
		// 3. 使用vue的语法实现功能
		var vm = new Vue({
			el:'#app',  // id前加#，因为这是一个标准的id选择器
			data:{
				msg: 'Hello,I am the data'
			}
		});
	</script>
</body>
</html>
```
	- 差值表达式，两个大括号
		1. 将数据插入HTML中 
		2. 支持基本的计算（符合js语法的都可）
	- new一个vue实例，参数是一个对象，里面有两个关键属性el,data
		1.  el == element 元素的挂载位置，用于表明在哪个位置填充数据，可以是CSS选择器，或者DOM元素
		2.  data：模型数据，是一个对象，表明填充的业务数据
