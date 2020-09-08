---
title: Linux下的安装
categories:
- 工具手册
- Linux
tags:
- linux
---
# ubuntu使用git
1.安装git
``` bash
$ sudo git config --global user.name "tanshiyitsy"
$ sudo git config --global user.email "1365519162@qq.com"
```
2.生成私钥

``` bash
$ ssh-keygen -C "1365519162@qq.com" -t rsa
$ ls ~/.ssh
$ cat id_rsa.pub 
```
把生成的公钥添加至github上，这样github才能知道提交代码的是谁，否则会拒绝

3.配置config文件,其他的不用更改
``` bash
Host github.com
User 1365519162@qq.com     （github登录时的用户名）
Hostname ssh.github.com
PreferredAuthentications publickey
IdentityFile /home/tansy/.ssh/id_rsa   （私钥地址）
```
4. 验证是否可以链接

```bash
ssh -T git@github.com
```
> 结果应为：
> Hi tanshiyitsy! You've successfully authenticated, but GitHub does not provide shell access.

# 安装输入法：
1.在这里下载安装包 https://pinyin.sogou.com/linux/?r=pinyin


2.在终端 切换到安装包所在目录

3.终端输入命令：

```bash
sudo dpkg -i sogoupinyin_2.3.1.0112_amd64.deb
```
如果失败, 重新打开一个终端输入：
``` bash
sudo apt-get install -f
```
再次`切换至安装包所在目录`，并输入：
``` bash
sudo dpkg -i sogoupinyin_2.3.1.0112_amd64.deb
```

4.重启Ubuntu可以切换啦

5.遇见错误：
- /usr/bin/env: node: No such file or directory

原因：通过apt-get 安装后没有添加链接

解决：
```ln -s /usr/bin/nodejs /usr/bin/node```
# Ubuntu常见命令
- 查询设置的代理：

```git config --global http.proxy```
- 判断是否可以用ssh连接


```unset HTTP_PROXY HTTPS_PROXY http_proxy https_proxy```

