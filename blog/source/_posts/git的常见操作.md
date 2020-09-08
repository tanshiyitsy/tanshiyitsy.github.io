---
title: git的常见操作
date: 2020-09-06 09:41:19
categories:
- 工具手册
- git
tags:
- git
---
# 基本操作
``` bash
$ git status
$ git add -A   # -A 可以换成指定的文件
$ git commit -m "修改信息"
$ git remote update --prune   # 获取远端所有分支
$ git fetch   		# 更新本地同远端一致
$ git pull
$ git push origin tanshiyi			# 只是把本地改变推上去了
$ git push origin HEAD:tanshiyi   	# 如果不存在分支则新建, 并且发起MR
```
# 分支操作
``` bash
git branch
git branch -a
git checkout -f tanshiyi
```