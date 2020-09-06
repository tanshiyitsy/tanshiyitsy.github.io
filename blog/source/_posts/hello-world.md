---
title: hexo的快速使用
categories:
- 前端
tags:
- hexo
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

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



## 搭建博客过程
### step1. 安装
1.安装[node.js](https://nodejs.org/en/)

`linux`下安装nodejs
```
sudo apt-get install nodejs
```
如果出错：

E: Could not get lock /var/lib/dpkg/lock - open (11: Resource temporarily unavailable)
E: Unable to lock the administration directory (/var/lib/dpkg/), is another process using it?

原因：apt还在运行，所以解决方案，找到并且杀掉apt-get,和apt进程
ps -A | grep apt
得到的结果为
```
user	pid	%CPU	%MEM```
杀掉相关进程：
```sudo kill -9 pid```

2.安装淘宝的cnpm管理器
```
 npm install -g cnpm --registry=http://registry.npm.taobao.org
 hexo n "my first blog" # 创建新的文章，new
 hexo clean # 清理
 hexo g # 生成
```
3.安装hexo框架
```
cnpm install -g hexo-cli
```
4.查看版本
```
node -v
npm -v
cnpm -v
hexo -v
```
### step2. 本地启动博客
1.初始化
```
mkdir blog
cd blog
sudo hexo init # 生成博客
```
2.启动本地博客服务，可通过 `http:127.0.0.1:4000`访问
```
hexo s # start
```
### step3. 远端部署
1.github上创建一个新的project
  - 命名格式必须为YourGithubName.github.io
  - 首次登陆需要设置邮箱，用户名
2.在博客目录下（此处为blog)安装git部署插件
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
