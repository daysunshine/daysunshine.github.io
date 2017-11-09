---
layout: post
title:  Haproxy的配置文件参数设定
date:   2017-10-30 09:00:00
categories: Linux 
tags:  haproxy 
---

### 介绍
> HAProxy提供高可用性、负载均衡以及基于TCP和HTTP应用的代理，支持虚拟主机，它是免费、快速并且可靠的一种解决方案。HAProxy特别适用于那些负载特大的web站点，这些站点通常又需要会话保持或七层处理。HAProxy运行在当前的硬件上，完全可以支持数以万计的并发连接。并且它的运行模式使得它可以很简单安全的整合进您当前的架构中， 同时可以保护你的web服务器不被暴露到网络上。
> HAProxy实现了一种事件驱动, 单一进程模型，此模型支持非常大的并发连接数。多进程或多线程模型受内存限制 、系统调度器限制以及无处不在的锁限制，很少能处理数千并发连接。事件驱动模型因为在有更好的资源和时间管理的用户空间(User-Space) 实现所有这些任务，所以没有这些问题。此模型的弊端是，在多核系统上，这些程序通常扩展性较差。这就是为什么他们必须进行优化以 使每个CPU时间片(Cycle)做更多的工作。

### 配置文件的参数及格式

> 在进行参数的详细介绍时，先查看配置中多参数样例，并予以解释说明，之后再进行一一的解释介绍

> HAProxy配置在/etc/haproxy/haproxy.cfg文件当中设定
> 分为5个配置段：global、default、listen、fronted、backend

```

#全局设置段
global   
    log         127.0.0.1 local3 info			#全区日志配置 使用rsyslog的local3设备   
    chroot      /var/lib/haproxy			#工作目录（安全）   
    pidfile     /var/run/haproxy.pid 			#pid文件存储目录   
    nbproc 1 						#后台进程数量    
    maxconn     40000					#每个进程最大并发数
    user        haproxy					#用户
    group       haproxy   				#组
    daemon 						#后台程序模式工作

#为后面3个配置短设定默认的配置
defaults
    mode                    http		#设定的模式    
    retries                 3   		#后端连接重试次数，超出标识不可用   
    timeout connect         10s  		#连接服务器最长等待时间   
    timeout client          30s  		#客户端发送请求最长等待时间     
    timeout server          30s 		#服务器会复客户端最长等待时间     
    timeout check           10s 		#对后端服务器的检测超时时间
	
#定义HAProxy监控页面
listen admin_stats 
    bind 0.0.0.0:6666					#设定的监听端口
    mode http
    log 127.0.0.1 local3 err 				#定义日志记录信息   
    stats refresh 30s 					#HAProxy监控页面统计自动刷新时间。  
    stats uri /haproxy-status				#设置监控页面URL路径。 http：//IP:6666/haproxy-status可查看    
    stats realm welcome login\ Haproxy			#统计页面密码框提示信息    
    stats auth admin:123456				#登录统计页面用户和密码    
    stats hide-version					#隐藏HAProxy版本信息    
    stats admin if TRUE					#设置TURE后可在监控页面手工启动关闭后端真实服务器

#定义前端虚拟节点
frontend www   
    bind *:80						#监听端口
    mode http    
    option httplog					#启用日志记录HTTP请求    
    option forwardfor					#启用后后端服务器可以获得客户端IP    
    option httpclose					#客户端和服务器完成一次连接请求后，HAProxy主动关闭TCP链接（优化选项）    
    log global						#使用全局日志配置    
    default_backend httpsrvs				#指定后端服务池（backend定义htmpool）

#定义后端真实服务器
backend httpsrvs
    mode http    
    option redispatch				#用于cookie会话保持。（如后端服务器故障，客户端cookie不会刷新，用此来把用户请求强制定向到正常服务器）    
    option abortonclose				#负载均衡很高时，自动结束当前队列处理时间长的连接    
    balance roundrobin				#负载均衡算法    
    cookie SERVERID					#允许向cookie插入SERVERID.下面server可以使用cookie定义    
    option httpchk GET /index.html	#启用HTTP服务状态检测功能 （后端服务器一定要存在此文件，不然haproxy认为其故障）
    
	#后端服务设置
    server web1 192.168.1.186:80 cookie server1 weight 6 check inter 2000 rise 2 fall 3
    server web2 192.168.1.188:80 cookie server2 weight 6 check inter 2000 rise 2 fall 3

```

### `global`全局配置段

> 包含进程及安全管理定义选项：`chroot`, `deamon`，`user`, `group`, `uid`, `gid`
> 其它配置选项：

- nbproc<number>：要启动的haproxy的进程数量，系统默认单进程，要求使用daemon模式
- ulimit-n <number>：每个haproxy进程可打开的最大文件数，系统自动会指定，不建议设置
- daemon 后端方式运行，建议使用

- log：定义全局的syslog服务器；最多可以定义两个

> 对于日志则有如下设定：
> 格式：log <address> [len<length>] <facility> [<level> [<minlevel>]]

> 默认发往本机的日志服务器；
- (1) local2.* /var/log/local2.log
- (2) $ModLoadimudp 、 $UDPServerRun514

> 日志示例：

```
#将特定信息记录在日志中
	capture cookie <name> len<length>

#捕获请求和响应报文中的cookie并记录日志
	capture request header <name> len<length>
#捕获请求报文中指定的首部并记录日志
示例:
	capture request header X-Forwarded-For len15
	capture response header <name> len<length>

#捕获响应报文中指定的首部并记录日志
示例：
	capture response header Content-length len9
	capture response header Location len15

```

> 其它性能调整选项

- maxconn <number>：设定每个haproxy进程所能接受的最大并发连接数
- maxconnrate <number>：设置每个进程每秒种所能建立的最大连接数量
- maxsessrate <number>：设置每个进程每秒种所能建立的最大会话数量
- maxsslconn <number>: 每进程支持SSL的最大连接数量
- spread-checks <0..50, in percent> 健康检测延迟时长比建议2-5之间

### `backend`后端

> 这里对一些常用的参数进行说明

> `bind`指定一个或多个前端侦听地址和端口

```
listen http_proxy
bind :80,:443
bind 10.0.0.1:10080,10.0.0.1:10443
bind /var/run/ssl-frontend.sockuser root mode 600 accept-proxy

```

### `balance`指定后端服务器组内的服务器调度算法

> 格式
```
	balance <algorithm> [ <arguments> ]
	balance url_param <param> [check_post]
```

负载均衡算法有以下几种：

- `roundrobin`：(动态)轮询。可运行时调整权重，支持慢启动，最多支持4095个后端主机。
- `static-rr`：静态轮询。运行时不可调整权重，不支持慢启动，无后端主机数量限制。
- `leastconn`：最少连接，按最少连接来调度，此处是加权最少连接，支持指定权重，是动态的，所以也就支持权重的运行时调整和慢启动。
- `first`：制定先调度至一个主机，主机连接限制到了，再调度到另外一台。
- `source`：source ip hash，源地址hash。根据请求的源地址进行hash，同一个来源ip调度到同一台主机。
- `uri`：uri hash。根据请求的uri的hash值调度，这样可以提高缓存的命中率。
- `url_param`：根据url里的某个参数的hash来调度。读取url地址里给定的参数的hash值，来调度。
- `hdr()`：根据HTTP header 的hash值进行调度。如果name不存在，或者如果不包含任何值，则采用roundrobin算法。

上述的`source`、 `uri`、 `url_param`、 `hdr()`采用的默认hash算法都是`map-based`，。还可以更改为另外一种算法`consistent`。

> `map-based`: 静态映射，可以分配权重，但是一旦服务启动后，无法动态调整权重，也不支持慢启动。而且一旦服务器发生变动，影响是全局的，不适合用来做缓存代理。这种算法虽然不好，但是占用系统资源少。
>
> `map-based`: 静态映射，可以分配权重，但是一旦服务启动后，无法动态调整权重，也不支持慢启动。而且一旦服务器发生变动，影响是全局的，不适合用来做缓存代理。这种算法虽然不好，但是占用系统资源少。
> `consistent`: 一致性hash(hash环)，服务器发生变动，只影响局部有限的服务器，可以运行中动态调整权重，支持慢启动。这种算法很好用，但是占用系统资源多。

示例：

1、缓存命中率更高，使用基于uri的一致性hash算法。对于来子与同一台服务器的请求会发往后端同一台服务器上去

```
frontend myweb
    bind *:80
    default_backend websrvs

backend websrvs
    balance uri
    server nginx-web1 192.168.2.1:80 check
    server nginx-web2 192.168.2.2080 check
    hash-type consistent
```

2、基于HTTP 头信息的浏览器来分类：

```
frontend myweb
    bind *:80
    default_backend websrvs

backend websrvs
    balance header(User-Agent)
    server nginx-web1 192.168.2.1:80 check
    server nginx-web2 192.168.2.2:80 check
    hash-type consistent
```

### `maxconn`
> 为指定的frontend定义其最大并发连接数；默认为2000

> 示例：
> 指定最大队列数，及最大并发数
```
	server nginx-web1 192.168.20.10:80 check maxconn 10000 maxqueue 2000
```

### mode { tcp|http|health}
> 定义haproxy的工作模式
- tcp：基于layer4实现代理；可代理mysql, pgsql, ssh, ssl等协议,https时使用此模式，默认模式
- http：仅当代理协议为http时使用,centos实际默认模式
- health：工作为健康状态检查的响应模式，当连接请求到达时回应“OK”后即断开连接，较少使用

#### server <name> <address>[:[port]] [param*]

> 定义后端主机的各服务器及其选项

- <name>：服务器在haproxy上的内部名称；出现在日志及警告信息
- <address>：服务器地址，支持使用主机名
- [:[port]]：端口映射；省略时，表示同bind中绑定的端口

- [param*]：
	- weight <weight>：权重，默认为1
	- maxconn <maxconn>：当前server的最大并发连接数
	- backlog <backlog>：当server的连接数达到上限后的后援队列长度
	- backup：设定当前server为备用服务器
	- check:对当前server做健康状态检测，只用于四层检测
	- disabled：标记为不可用
	- cookie:cookie的会话绑定
	- redir:临时重定向
```
frontend myweb
    bind *:80
    default_backend websrvs
backend websrvs
    server nginx-web1 192.168.0.10:80 check
    server nginx-web2 192.168.0.11:80 check
```
	
> 标记为backup的主机，不负载均衡，在服务器全挂了的时候才会调用，一般用来做sorry Server。

```
server sorry-server 192.168.0.23 backup	
```	

### `check` 健康状态检测

> 相关参数

- `check`：是否启用健康检查。不写则默认不启用健康检查。
- `addr`：可以不占用服务器主用的ip，换一个ip进行检测。
- `port`：同样，端口也可以换一个。
- `inter `：两个连续的检查之间的延时，默认为2000ms。
- `fall `：连续多少次检测结果为“失败”才标记服务器为不可用；默认为3
- `rise `：连续多少次检测结果为“成功”才标记服务器为可用；默认为2

> 示例：

```
frontend myweb
    bind 192.168.20.111:443
    mode http
    default_backend websrvs

backend websrvs
    mode http
    balance source
    server nginx-web1 192.168.2.1:443 check port 80 inter 1000 fall 3 rise 5
    server nginx-web2 192.168.2.2:443 check port 80 inter 1000 fall 3 rise 5
```

### `option httpcheck`
> 基于http协议做健康检查

> 可以指定method、uri、version

```
option httpchk
option httpchk <uri>
option httpchk <method> <uri>
option httpchk <method> <uri> <version>
```

示例：

```
backend https_relay
    mode tcp
    option httpchk OPTIONS * HTTP/1.1\r\nHost:\ www
    server apache1 192.168.1.1:443 check port 80
```

> 还有 smtpchk、mysql-check、pgsql-check 、ssl-hello-chk等检测方法

### `cookie` 

> 为当前server指定cookie值，实现基于cookie的会话黏性

> cookie的格式：cookie <name> [ rewrite | insert | prefix ] [ indirect ] [ nocache ] [ postonly ] [ preserve ] [ httponly ] [ secure ] [ domain <domain> ]* [ maxidle <idle> ] [ maxlife <life> ]
- <name>：cookie名称，用于实现持久连接
- rewrite：重写
- insert：插入
- prefix：前缀

> 示例:
> 基于cookie的session sticky的实现：

```
backend websrvs
cookie WEBSRV insert nocache indirect
server srv1 172.16.100.6:80 weight 2 check rise 1 fall 2 maxconn 3000 cookie srv1
server srv2 172.16.100.7:80 weight 1 check rise 1 fall 2 maxconn 3000 cookie srv2
```

### `redir`

> 将发往此server的所有GET和HEAD类的请求重定向至指定的URL,302状态码临时重定向
> 示例:

```
frontend myweb
    bind 192.168.2.111:443
    mode http
    default_backend websrvs

backend websrvs
    mode http
    balance source
    server nginx-web1 192.168.2.1:443 check port 80 inter 1000 fall 3 rise 5
    server nginx-web2 192.168.2.2:443 check port 80 inter 1000 fall 3 rise 5 redir http:///www.baidu.com
```

> 对192.168.2.1的访问，临时重定向到http://www.baidu.com

### `compression` 压缩

```
	compression algo <algorithm>
	compression type <mime type>
```

用在前段示例：

```
frontend myweb
    bind *:80
    default_backend websrvs
    compression algo gzip
    compression type text/html text/plain application/xml application/javascript

backend websrvs
    balance source
    server nginx-web1 192.168.2.1:80 check
    server nginx-web2 192.168.2.2:80 check
    hash-type consistent
```

### 统计接口启用相关的参数


> stats enable		#启用统计页；基于默认的参数启用stats page
	-stats uri: /haproxy?statsuri默认值
	-stats realm : HAProxy\Statistics
	-stats auth: no authentication
> stats uri<prefix>	#自定义stats page uri
> stats auth<user>:<passwd> #认证时的账号和密码，可使用多次
> stats realm <realm> #认证时的realm提示信息
> stats hide-version	#隐藏版本
> stats refresh <delay>#设定自动刷新时间间隔
> stats admin { if | unless } <cond>#启用stats page中的管理功能

> 配置示例：

```
listen stats
bind :9099
stats enable
stats realm HAPorxy\Stats\Page
stats authadmin:admin
stats admin if TRUE
```




#### Haproxy介绍完毕，如有错误还望批评指正，敬请谅解！
