---
title: nginx
date: 2020-07-28 10:59:27
categories: 后端
tags:
- 大数据
---
# 是什么？
高性能的HTTP和反向代理服务器；特点是占有内存少，并发能力强
# 用途
1. 反向代理：暴露的是代理服务器的IP，隐藏真实服务器的IP
2. 负载均衡：把请求分发到各个服务器上;将负载分摊到不同服务器上，保证服务的可靠性，及响应的及时性
    nginx提供的分配策略
- 轮询（默认）
  将请求按时间顺序逐一分配给不同的后端服务器，如果后端服务器down掉，可以自动剔除
- weight
  weight越大，被分配的客户端越多
- ip_hash
  每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端，可以解决session的问题，如：
```bash
upstream server_pool{
  ip_hash;
  server 192.168.5.21:80;
  server 192.168.5.22:80;
}
```
- fair(第三方）
按后端服务器的响应时间来分配请求，响应时间短的优先分配
```bash
upstream server_pool{
  server 192.168.5.21:80;
  server 192.168.5.22:80;
  fair;
}
```
3. 动静分离：动态请求和静态请求由不同的服务器解析，加快解析速度
   目前实现有两种：一是静态文件独立成单独的域名，放在独立的服务器上；二是动态和警惕啊文件混合在一起发布，通过nginx分开

# 安装及简介
官网下载：http://nginx.org/
需要安装的`pcre`,`openssl`,`zlib`,`nginx`

1. 安装pcre
   wget http://downloads.sourceforge.net/project/pcre/pcre/8.37/pcre-8.37.tar.gz5                
   解压 ---> ./configure ---> cd ./pcre  -->  make --> make install
2. 安装openssl
3. 安装zlib
   ……

查看开放端口号：

``` bash
firewall-cmd --list-all
```
​		设置开放端口号：
``` bash
firewall-cmd --add-service=http -permanent
sudo firewall-cmd --add-port=80/tcp --permanent
```
​		重启防火墙：
``` bash
firewall-cmd -reload
nginx启动： cd /usr/local/nginx/sbin && ./nginx
nginx关闭： cd /usr/local/nginx/sbin && ./nginx -s stop
nginx重新加载：cd /usr/local/nginx/sbin &&  ./nginx -s reload
```
配置文件目录：`cd /usr/local/nginx/conf`
主配置文件：`nginx.conf`
<font color=green>第一部分：全局块</font>
worker_processes 1;    # 生成的worker process数量
<font color=green>第二部分： events块</font>
主要影响nginx服务器与用户的网络连接
events {
worker_connections 1024;     # 表示每个work process支持的最大连接数为1024
}
<font color=green>第三部分：http块</font>
代理、缓存、日志定义等功能和第三方模块配置均在这里
这里分为<font color=yellow>http全局块、server块</font>

- http全局块
  包括文件引入、MIME-TYPE定义、日志自定义、连接超时时间、单链接请求数上限                        

- server块
  - 全局server块
    本虚拟机主机的监听配置&&本虚拟主机的名称or IP配置
  - location块
    一个server块可以配置多个location

操作示例

示例1:   <font color=red>反向代理：</font>访问www.123.com直接跳转至127.0.0.1:8080

```bash
server {
		listen        80           # 监听80端口
		server_name www.123.com    # 访问域名
		location / {
		proxy_pass http://127.0.0.1:8080
		index index.html index.htm index.jsp
	}
}
```
示例2: <font color=red>根据访问的路径跳转至不同端口的服务</font>
nginx监听端口为9001
访问http://127.0.0.1:9001/edu/ 跳转至  127.0.0.1:8081
访问http://127.0.0.1:9001/vod/ 跳转至127.0.0.1:8082

1. 准备两个tomcat，一个8001端口，一个8002端口
1. 修改nginx.conf
http块中添加server{}
``` bash
server {
		listen 9001;
		server_name localhost
		location ~ /edu/ {
				proxy_pass http://localhost:8081;
		}
		location ~ /vod/ {
				proxy_pass http://localhost:8082;
		}
}
```
location指令

```bash
location [ = | ~ | ~* | ^~] uri {
}
```

1. =：用于不含正则的uri前，严格匹配；如果成功，停止向下搜索并立即处理请求
2. ～：表示uri包含正则，并且区分大小写
3. ～*：表示uri包含正则，并且不区分大小写
4. ^~:表示不含正则的uri前，nginx服务器会找到标识uri和请求字符串匹配度最高的location

示例3:<font color=red>负载均衡</font>

准备两个同时启动的tomcat
nginx.conf中配置

```bash
http{
		......
		upstream myserver{
				ip_hash;
				server 115.28.52.63:8080 weight=1;
				server 115.28.52.63:8180 weight=1;
		}
		......
		server{
				location / {
					proxy_pass http://myserver;
					proxy_connect_timeout 10;
				}
	}
}
```

示例4:

```bash
server {
		listen 80;
		server_name 192.168.17.129
		location /www/ {
		root /data/;
		index index.html index.htm
		}
		location /image/ {
		root /data/;
		autoindex on;
		}
}
```

# 其他零散

1. location中expire参数可以设置有效时间，无修改从本地返回（304），有修改从服务器返回（200）
2. master-workers机制的好处
   - 独立进程，不需要加锁，省掉了锁的开销
   - 一个进程退出不影响其他进程
   - 一个进程异常退出，只影响该进程负责的请求

