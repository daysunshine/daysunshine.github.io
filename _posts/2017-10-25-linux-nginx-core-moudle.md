---
layout: post
title:  Nginx的ngx_http_core_module详解
date:   2017-10-25 09:00:00
categories: Linux 
tags:  http核心模块
---


### Nginx的默认配置文件

> nginx提供给用户默认的配置文件/etc/nginx/nginx.conf,配置为文件里会加载/etc/nginx.d/下的自配置文件，我们先来看看，具体的选项。

```
#http的配置
http {
    # 日志格式 日志别名
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    
	# 定义日志路径和使用的日志格式（用上面定义的别名）
    access_log  /var/log/nginx/access.log  main;
   
    sendfile            on;    # sendfile零拷贝
    tcp_nopush          on;    # 在sendfile模式下，是否启用TCP_CORK选项
    tcp_nodelay         on;    # 在keepalived模式下的连接是否启用TCP_NODELAY选项
    keepalive_timeout   65;    # 设定保持连接的超时时长，0表示禁止长连接；默认为65s
    types_hash_max_size 2048;
	
    include             /etc/nginx/mime.types;     # 支持的MIME类型文件
    default_type        application/octet-stream;  # 
    
	# Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;  	# 载入confi.d下的子配置文件
    
server {
    listen  80 default_server;  	# 监听地址；default_server表示是设定为默认虚拟主机
    listen  [::]:80 default_server; # ipv6的监听地址
    server_name  _;  				# 列出所有服务器名称。
    root  /usr/share/nginx/html;  	# http服务器的根目录相对于系统的路径
        
	# Load configuration files for the default server block.
    include /etc/nginx/default.d/*.conf; # 载入default.d下的配置文件
        
		location / {  # 根据请求URI设置配置，这里请求的URI是/
		}
        
	error_page 404 =200 /404.html; 	#设置404错误页面为/404.html,并设定防止错误页面劫持的现象，让其返回状态码为200
        location = /40x.html { 	# 这里又缩进了一层location，表示404的error_page实际指向是http root目录的40x.html,根据上文root路径，可得出是系统下/usr/share/nginx/html/40x.html
    }
    
	error_page 500 502 503 504 /50x.html;
        location = /50x.html {
    } 原理同上，是把500，502 503，504的error_page都定位到/50x.html上
}

```

> https的设定

```

# Settings for a TLS enabled server.
    server {
        listen  443 ssl http2 default_server;   	# 端口为443，开启了ssl，协议为HTTP2.0标准，设置为默认服务器。
        listen  [::]:443 ssl http2 default_server;  # ipv6的
        server_name  _;  							# 服务器名字列表
        root  /usr/share/nginx/html;  				# https服务根目录
        ssl_certificate "/etc/pki/nginx/server.crt";# 服务器证书
        ssl_certificate_key "/etc/pki/nginx/private/server.key"; # 服务器认证私钥
        ssl_session_cache shared:SSL:1m; 			# SSL会话缓存模式为共享，时间为1分钟。
        ssl_session_timeout  10m; 					# SSL会话超时时间10分钟
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;
        
		# Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;  
        location / {
        }
        error_page 404 /404.html;
            location = /40x.html {
        }
        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
}

```

### ngx_http_core_module模块

> 对于nginx的未知模块可访问官方的文档查询[http://nginx.org/en/docs/](http://nginx.org/en/docs/)

### 套接字相关配置

**1、`server` 配置虚拟主机**
server { ... }  #只能用在http里

```
#配置一个虚拟主机
server {
	listen  	address[:PORT]|PORT;
	server_name SERVER_NAME;
	root  		/PATH/TO/DOCUMENT_ROOT;
}

```

**2、`listen`设定IP端口**

```
listen PORT|address[:port]| unix:/PATH/TO/SOCKET_FILE
listen address[:port] [default_server] [ssl] [http2 | spdy] [backlog=number] [rcvbuf=size] [sndbuf=size]
	default_server	设定为默认虚拟主机
	ssl				限制仅能够通过ssl连接提供服务
	backlog=number 	超过并发连接数后，新请求进入后援队列的长度
	rcvbuf=size		接收缓冲区大小
	sndbuf=size 	发送缓冲区大小

注意：
(1) 基于port；
	listen PORT; 指令监听在不同的端口
(2) 基于ip的虚拟主机
	listen IP:PORT; IP 地址不同
(3) 基于hostname
	server_namefqdn; 指令指向不同的主机名

```

**3、`server name`虚拟主机名称**

```
格式：server_name  name ...;

虚拟主机的主机名称后可跟多个由空白字符分隔的字符串
支持*通配任意长度的任意字符
	server_name *.magedu.com www.magedu.*
支持~起始的字符做正则表达式模式匹配，性能原因慎用
	server_name ~^www\d+\.magedu\.com$   \d 表示[0-9]

匹配优先级机制从高到低：
(1) 首先是字符串精确匹配如：www.magedu.com
(2) 左侧*通配符如：*.magedu.com
(3) 右侧*通配符如：www.magedu.*
(4) 正则表达式如：~^.*\.magedu\.com$
(5) default_server

```

**4、`tcp_nodelay`**
> 可用于：http, server, location

```
tcp_nodelay on | off;

在keepalived模式下的连接是否启用TCP_NODELAY选项
当为off时，延迟发送，合并多个请求后再发送
默认On时，不延迟发送

```

**5、sendfile  on \| off **

```
是否启用sendfile功能，在内核中封装报文直接发送
默认Off
```

**6、server_tokenson | off | build | string**
> 是否在响应报文的Server首部显示nginx版本


### 定义路径相关配置

**7、root  主站点文件位置**
> 设置web资源的路径映射；用于指明请求的URL所对应的文档的目录路径，可用于http, server, location, if in location

```
server {
		...
		root /data/www/vhost1;
}

示例:
http://www.magedu.com/images/logo.jpg --> /data/www/vhosts/images/logo.jpg

```

**8、location **

```

在一个server中location配置段可存在多个，用于实现从uri到文件系统的路径映射；ngnix会根据用户请求的URI来检查定义的所有location，并找出一个最佳匹配，而后应用其配置
location [ = | ~ | ~* | ^~ ] uri{ ... }
location @name { ... }

location   URI  {}:	对当前路径及子路径下的所有对象都生效；

location =  URI {}:	精确匹配指定的路径，不包括子路径，因此，只对当前资源生效；

location ~  URI {}:
location ~* URI {}:	模式匹配URI，此处的URI可使用正则表达式，~区分字符大小写，~*不区分字符大小写；

location ^~ URI {}:	不使用正则表达式

匹配优先级从高到低：  =, ^~, ~/~*, 不带符号

```

示例：

```
server {
		...
		server_name  www.magedu.com;
		location /images/ {
					root /data/imgs/;
				}
}

#在访问的时候会访问在location定义root下的目录下的文件，对应上面的设置是这样的
http://www.magedu.com/images/logo.jpg  --> /data/imgs/images/logo.jpg


root /vhosts/www/htdocs/
http://www.magedu.com/index.html --> /vhosts/www/htdocs/index.html

server {
	root /vhosts/www/htdocs/
		location /admin/ {
				root /webapps/app1/data/
		}
}

#对上面的设置是这样的
http://www.magedu.com/admin/index.html --> /webapps/app1/data/admin/index.html

#同样也可以在server内设置root的主站点目录

```

**9、`alias path`定义路径别名**

> 路径别名，文档映射的另一种机制；仅能用于location上下文
> 示例：

```
	http://www.magedu.com/bbs/index.php
location /bbs/ {
	alias /web/forum/;
} 		--> /web/forum/index.html
location /bbs/ {
	root /web/forum/;
} 		--> /web/forum/bbs/index.html

注意：location中使用root指令和alias指令的意义不同
(a) root，给定的路径对应于location中的/uri/左侧的/
(b) alias，给定的路径对应于location中的/uri/右侧的/

```

**10、`index file` 默认网页资源**

> 指定默认网页资源，注意：ngx_http_index_module模块

**11、`error_page` 定义错误页面**

> 定义错误页，以指定的响应状态码进行响应
> 可用位置：http, server, location, if in location

```
格式
error_pagecode ... [=[response]] uri

示例：
error_page 404 /404.html
error_page 404 =200 /404.html  #在显示错误页面时，返回200正确的状态码，防止错误页面被网站劫持

```

**12、`try_files  file ... uri`**

> 按顺序检查文件是否存在，返回第一个找到的文件或文件夹（结尾加斜线表示为文件夹），如果所有的文件或文件夹都找不到，会进行一个内部重定向到最后一个参数。只有最后一个参数可以引起一个内部重定向，之前的参数只设置内部URI的指向。最后一个参数是回退URI且必须存在，否则会出现内部500错误

```

格式：
try_files file ... uri;
try_files file ... =code;

示例：
location /images/ { 
		try_files $uri /images/default.gif; 
}
location / { 
		try_files $uri $uri/index.html $uri.html =404;  都找不到返回404
}


```

### 定义客户端请求相关设置


**13、keepalive_timeout timeout [header_timeout];**

> 设定保持连接超时时长，0表示禁止长连接，默认为75s

**14、keepalive_requests number;**

> 在一次长连接上所允许请求的资源的最大数量
> 默认为100

**15、keepalive_disable none \| browser ...**

> 对哪种浏览器禁用长连接

**16、send_timeout time;**

> 向客户端发送响应报文的超时时长，此处是指两次写操作之间的间隔时长，而非整个响应过程的传输时长

**17、`client_body_buffer_size  size`**

> 用于接收每个客户端请求报文的body部分的缓冲区大小；默认为16k；超出此大小时，其将被暂存到磁盘上的由client_body_temp_path指令所定义的位置

**18、client_body_temp_path  path [level1 [level2 [level3]]];**
> 设定用于存储客户端请求报文的body部分的临时存储路径及子目录结构和数量
> 目录名为16进制的数字；

```
client_body_temp_path  /var/tmp/client_body  1 2 2
1的含义： 1级目录占1位16进制，即2^4=16个 目录0-f
2的含义： 2级目录占2位16进制，即2^8=256个目录00-ff
2的含义： 3级目录占2位16进制，即2^8=256个目录00-ff

```

### 对客户端进行限制相关配置

**19、limit_rate  rate**
> 限制响应给客户端的传输速率，单位是bytes/second
> 默认值为0，表示无限制


**20、limit_expect   限制客户端使用除了指定的请求方法之外的其它方法**
> 仅用于location

```

#格式
limit_expect  method ... {
				.........
}

method选项：
GET, HEAD, POST, PUT, DELETE，MKCOL, COPY, MOVE, OPTIONS, PROPFIND,PROPPATCH, LOCK, UNLOCK, PATCH

示例：
limit_except GET {         #允许GET的同时，HEAD也会被允许
	allow 192.168.1.0/24;
	deny all;
} 

#示例中表示：指定的网段能够访问所有的方法，其他的只能访问设定的方法

```

### 文件操作优化设置

**21、aio  on | off | threads[=pool]**
> 是否启用aio(异步)功能

**22、directio  size | off**
> 是否同步（直接）写磁盘，而非写缓存，在Linux主机启用O_DIRECT标记，则文件大于等于给定大小时使用，例如directio 4m

**23、open_file_cache off;**

```
#格式：
open_file_cache  max=N [inactive=time];

max=N：可缓存的缓存项上限；达到上限后会使用LRU算法实现管理
inactive=time：缓存项的非活动时长，在此处指定的时长内未被命中的或命中的次数少于open_file_cache_min_uses指令所指定的次数的缓存项即为非活动项，将被删除

nginx可以缓存以下三种信息：
(1) 文件元数据：文件的描述符、文件大小和最近一次的修改时间
(2) 打开的目录结构
(3) 没有找到的或者没有权限访问的文件的相关信息

```

**24、open_file_cache_errors on | off**
> 是否缓存查找时发生错误的文件一类的信息
> 默认值为off

**25、open_file_cache_min_uses number**
> open_file_cache指令的inactive参数指定的时长内，至少被命中此处指定的次数方可被归类为活动项
> 默认次数值为1

**26、open_file_cache_valid  time**
> 缓存项有效性的检查频率
> 默认值为60s


#### 本文只列举Nginx的核心模块ngx_http_core_module常用的选项，如有错误还望批评指正，敬请谅解！


