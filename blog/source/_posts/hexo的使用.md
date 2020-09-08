---
title: hexo的快速使用
categories:
- 工具手册
tags:
- hexo
---

## Quick Start

``` bash
$ hexo new "My New Post"
```
More info: [Writing](https://hexo.io/docs/writing.html)

### Run server
``` bash
$ hexo server
```
More info: [Server](https://hexo.io/docs/server.html)

### Generate static files
``` bash
$ hexo generate
```
More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)
> Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## 搭建博客过程
### step1. 安装必要插件
1. 安装[node.js](https://nodejs.org/en/)

`linux`下安装nodejs: 

```
sudo apt-get install nodejs
node -v
npm -v
```

> 如果出错：

> E: Could not get lock /var/lib/dpkg/lock - open (11: Resource temporarily unavailable)
E: Unable to lock the administration directory (/var/lib/dpkg/), is another process using it?

> 原因：apt还在运行，所以解决方案，找到并且杀掉apt-get,和apt进程
> ps -A | grep apt
> 
> 得到的结果为
```
user	pid	%CPU	%MEM```
杀掉相关进程：```sudo kill -9 pid```

2.安装淘宝的cnpm管理器
```
 npm install -g cnpm --registry=http://registry.npm.taobao.org
 cnpm -v
```
3.安装hexo框架
```
cnpm install -g hexo-cli
hexo -v
```

### step2. 编写博客
1. 第一次使用

```
mkdir blog
cd blog
hexo init		# 生成博客
```
2. 从github上拉取下来

```
cd blog
hexo n "my first blog"	 # 创建新的文章，new
hexo clean 				 # 清理
hexo g 					 # 生成
```
3. 本地启动

```
hexo s 				# start
```

### step3. 远端部署
1.github上创建一个新的project
  - 命名格式必须为YourGithubName.github.io
  - 首次登陆需要设置邮箱，用户名


2.<font color=red>在博客目录下（此处为blog)安装git部署插件</font>
```
cnpm install --save hexo-deployer-git
```
3.配置_config.yml文件(在blog目录下)
主要配置type，repo（为创建的GitHub project链接），branch
```
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
type: git
repo: https://github.com/YourGithubName/YourGithubName.github.io.git
branch: master
```

4.部署到GitHub仓库
```
hexo d    # 部署到远程仓库
```
其他人可以通过这个地址访问博客：https://YourGithubName.github.io/
### step4. 修改默认主题
1.下载yilia主题到本地(themes目录下)
```
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
2.修改hexo根目录下的 _config.yml 文件 ： theme: yilia
3.重新启动
```
hexo c # 清理一下 clean
hexo g # 重新生成 generate
hexo d # 部署到远端GitHub
```
