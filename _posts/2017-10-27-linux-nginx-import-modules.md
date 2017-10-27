---
layout: post
title:  Nginx的其它重要模块
date:   2017-10-27 08:00:00
categories: Linux 
tags: http_ssl http_proxy http_fastcgi http_upstream
---

## 介绍的模块列表

> - ngx_http_ssl_module
> - ngx_http_proxy_module
> - ngx_http_fastcgi_module
> - ngx_http_upstream_module

### ngx_http_ssl_module模块

> httpd加密模块，包含了如下的选项设定

**1、ssl on |off**
> 是否开启HTTPS协议，建议用listen命令代替

**2、ssl_certificate file**
> 指定证书文件的位置

**3、ssl_certificate_key file**
> 与证书匹配的秘钥文件的位置

**4、ssl_protocols [SSLv2] [SSLv3] [TLSv1] [TLSv1.1] [TLSv1.2]**
> 支持的ssl协议的版本，默认为后三个

**5、ssl_session_cache off | none | [builtin[:size]] [shared:name:size]**
> 是否使用自带的缓存
> builtin[:size]：使用OpenSSL内建缓存，为每个worker进程私有
> [shared:name:size]：在各worker之间使用一个共享的缓存

**6、ssl_session_timeout time**
> 客户端连接可以复用sslsession cache中缓存的ssl参数的有效时长，默认5m

**示例：**

```

server {
	listen 443 ssl;				#设定监听端口443在ssl上
	server_name www.magedu.com;	#设定主机名
	root /vhosts/ssl/htdocs;	#指定根目录的位置
	
	ssl on;											#是否开启ssl
	ssl_certificate /etc/nginx/ssl/nginx.crt;		#指定证书文件位置
	ssl_certificate_key/ etc/nginx/ssl/nginx.key;	#指定域证书文件对应的秘钥文件的位置
	ssl_session_cache shared:sslcache:20m;			#设定为共享缓存，缓存名称为sslcache，上限为20M
	ssl_session_timeout 10m;						#设定可以使用缓存的时间
}

#创建自签名证书用于ssl测试
[root@Centos6 ~]#cd /etc/pki/tls/certs/
[root@Centos6 /etc/pki/tls/certs]#make test.crt
...
部分省略
...
填写必要的信息

#将加密的秘钥中的私钥提取出来，更名并放入指定的目录下
[root@Centos6 /etc/pki/tls/certs]#openssl rsa -in test.key -out test2.key
[root@Centos6 /etc/pki/tls/certs]#cp test.crt  /etc/nginx/ssl/nginx.crt
[root@Centos6 /etc/pki/tls/certs]#cp test2.key  /etc/nginx/ssl/nginx.key

```

### ngx_http_proxy_module模块

> 用于设定nginx的反向代理,将由本机去代理访问相应的服务器
> The ngx_http_proxy_module module allows passing requests to another server

**1、proxy_pass URI**

> 指定代理的服务器的地址
> 注意：proxy_pass后面的路径不带uri时，其会将location的uri传递给后端主机

```

示例：
server {
		...
		server_name HOSTNAME;
		location /html {
			proxy_pass http://172.18.18.18; 最后没有/
		}
	...
}

# proxy_pass后面的路径是一个uri时，其会将location的uri替换为proxy_pass的uri

#示例中当访问http://HOSTNAME/html时，会跳转至http://172.18.18.18/html目录下的html
#当proxy_pass后面URL有"/"时,代表访问http://HOSTNAME/html，会访问的是http://172.18.18.18/

#如果location定义其uri时使用了正则表达式的模式，则proxy_pass之后必须不能使用uri; 用户请求时传递的uri将直接附加代理到的服务的之后

示例：

server {
	...
	server_name HOSTNAME;
		location ~|~* /uri/ {
			proxy_pass http://host; 不能加/
		}
	...
}

#当访问：http://HOSTNAME/uri/ ，则会访问http://host/uri/

```

**2、proxy_set_header field value**

> 再转发的时候将发往后端的请求添加一些首部字段
> 作用在http, server, location

```
proxy_set_header  X-Real-IP自定义  $remote_addr;
proxy_set_header  X-Forwarded-For 	   $proxy_add_x_forwarded_for;

```

**3、proxy_cache_path**

> 用于设定代理缓存加速，定义可用于proxy功能的缓存

```
格式：proxy_cache_path  path(指定存放路径) [levels=levels] [use_temp_path=on|off] keys_zone=name:size [inactive=time] [max_size=size] [manager_files=number] [manager_sleep=time] [manager_threshold=time] [loader_files=number] [loader_sleep=time] [loader_threshold=time] [purger=on|off] [purger_files=number] [purger_sleep=time] [purger_threshold=time];

```

**4、proxy_cache zone | off; 默认off**
> 指明调用的缓存，或关闭缓存机制；Context:http, server, location

**5、proxy_cache_key string;**
> 缓存中用于“键”的内容
> 默认值：proxy_cache_key $scheme $proxy_host $request_uri;

**6、proxy_cache_valid [code ...] time;**
> 定义对特定响应码的响应内容的缓存时长
> 定义在http{...}中

```
示例:
proxy_cache_valid 200 302 10m;
proxy_cache_valid 404 1m;

```

####  `实现proxy缓存加速`

**1、http{...}段的缓存设置**

```
proxy_cache_path /var/cache/nginx/proxy_cache  levels=1:2:2 keys_zone=proxycache:20m inactive=120s max_size=1g;

```

**2、server{...}段定义调用缓存功能**

```
proxy_cache proxycache;        		#指定调用缓存的名称
proxy_cache_key $request_uri;		#指定缓存文件存放的路径
proxy_cache_valid 200 302 301 1h;	#指定特定的页面的缓存时长
proxy_cache_valid any 1m;			#指定特定的页面的缓存时长

```

**3、proxy_cache_use_stale**

> 格式：proxy_cache_use_stale  error | timeout | invalid_header| updating | http_500 | 	http_502 | http_503 | http_504 | http_403 | http_404 | off ...
> 在被代理的后端服务器出现哪种情况下，可以真接使用过期的缓存响应客户端

**4、proxy_cache_methods GET | HEAD | POST ...**
> 对哪些客户端请求方法对应的响应进行缓存，GET和HEAD方法总是被缓存

**5、proxy_hide_header field**
> 用于隐藏后端服务器特定的响应首部

**6、proxy_connect_timeout time**
> 定义与后端服务器建立连接的超时时长，如超时会出现502错误，默认为60s，一般不建议超出75s，

**7、proxy_send_timeout time**
> 把请求发送给后端服务器的超时时长；默认为60s

**8、proxy_read_timeout time**
> 等待后端服务器发送响应报文的超时时长，默认为60s


### ngx_http_fastcgi模块

> 转发请求到FastCGI服务器，不支持php模块方式,必须是PHP-fpm模式
> 定义在location内，同时也要在nginx的服务器上进行指定，fcgi服务器的地址，文本资源，及参数

**1、fastcgi_pass address**
> address为后端的fastcgi server的地址
> 可用位置：location, if in location

**2、fastcgi_index name**
> fastcgi默认的主页资源
> 示例：fastcgi_index index.php;

**3、fastcgi_param parameter value [if_not_empty]**
> 设置传递给FastCGI服务器的参数值，可以是文本，变量或组合

示例1：

```
1)在后端服务器先配置fpm server和mariadb-server

#在mysql的服务器上(172.18.18.30)，创建数据库，设定账号
mysql> create database test ;
mysql> grant all on test.* to testuser@'%' identified by 'centos';

#在后端fastcgi上设定监听端口，以及index.php的页面，并在index.php页面内设置连接数据库的PHP代码
#将fastcgi的PHP页面放在了/app/test下可以自己指定

2)在前端nginx服务上做以下配置：
	location ~* \.php$ {
		fastcgi_pass 172.18.18.10:9000; #指定后端服务器及端口
        fastcgi_index index.php;		#指定访问文件的名称
        fastcgi_param SCRIPT_FILENAME /app/test$fastcgi_script_name; #指定了文件所在的路径
        include fastcgi_params;			#添加nginx里的fastcgi的配置文件fastcgi_params	
注意：	#Nginx有两份fastcgi配置文件，分别是[fastcgi_params]和[fastcgi.conf]，它们没有太大的差异，唯一的区别是后者比前者多了一行[SCRIPT_FILENAME]的定义，因此完全可以在fastcgi内将SCRIPT_FILENAME对应的那行文件的路径先进行设定好，而后添加"include  fastcgi.conf"这段代码到location内
	…
}
```

示例2：通过/pm_status和/ping来获取fpm server状态信息

```
location ~* ^/(pm_status|ping)$ {
	include  fastcgi_params;
	fastcgi_pass 172.18.18.10:9000;
	fastcgi_param SCRIPT_FILENAME $fastcgi_script_name;
}

#对应实现的是，当访问后端的fastcgi时，能够查询后端的fastcgi的状态，以及ping它时你能够返回pang字符串来进行响应

```

#### `实现fastcgi缓存加速`

**1、定义fastcgi缓存**

> 命令格式与proxy的缓存加速设定类似,也需要定义在http段

```
格式：fastcgi_cache_path  path [levels=levels] [use_temp_path=on|off] keys_zone=name:size [inactive=time] [max_size=size] [manager_files=number] [manager_sleep=time] [manager_threshold=time] [loader_files=number] [loader_sleep=time] [loader_threshold=time] [purger=on|off] [purger_files=number] [purger_sleep=time] [purger_threshold=time]

path 			#缓存位置为磁盘上的文件系统
max_size=size	#磁盘path路径中用于缓存数据的缓存空间上限
levels=levels：	#缓存目录的层级数量，以及每一级的目录数量，例如：levels=1:2:3

```
**2、fastcgi_cache zone | off**
> 调用指定的缓存空间来缓存数据
> 可用位置：http, server, location

**3、fastcgi_cache_key string**
> 定义用作缓存项的key的字符串
> 示例：fastcgi_cache_key $request_uri;

**4、fastcgi_cache_methods GET | HEAD | POST ...**
> 为哪些请求方法使用缓存

**5、fastcgi_cache_min_uses number**
> 缓存空间中的缓存项在inactive定义的非活动时间内至少要被访问到此处所指定的次数方可被认作活动项

**6、fastcgi_keep_conn on | off**
> 收到后端服务器响应后，fastcgi服务器是否关闭连接，建议启用长连接

**7、fastcgi_cache_valid [code ...] time**
> 不同的响应码各自的缓存时长


**示例：**

> 各选项与proxy对应的含义相同，这里只列举示例不再进行解释

```
http {
	fastcgi_cache_path /var/cache/nginx/fcgi_cache levels=1:2:1 keys_zone=fcgicache:20m inactive=120s;
	...
	server {
		location ~* \.php$ {
			...
			fastcgi_cache fcgicache;
			fastcgi_cache_key $request_uri;
			fastcgi_cache_valid 200 302 10m;
			fastcgi_cache_valid 301 1h;
			fastcgi_cache_valid any 1m;
	...
	}
}

```

### ngx_http_upstream_module模块

> 用于将多个服务器定义成服务器组，而由proxy_pass, fastcgi_pass等指令进行引用

**1、upstream name { ... }**

> 定义后端服务器组，会引入一个新的上下文,默认调度算法是wrr,而后通过proxy_pass等进行引用
> 用在 http里面：

```
upstream httpdsrvs {   在httpdsrvs的组内，添加后端服务器的成员
	server   192.1668.25.2:80    weight=2;
	server	 192.1668.25.2:80    weight=1;
	...
}

#引用时为
location ~ \.php$ {
	proxy_pass  httpdsrvs;
	...
}

```

**2、server  address  [parameters]**
> 在upstream上下文中server成员，以及相关的参数
> Context:upstream

```
#address的表示格式：
	unix:/PATH/TO/SOME_SOCK_FILE
	IP[:PORT]
	HOSTNAME[:PORT]
#parameters：
	weight=number 		权重，默认为1
	max_conns			接后端报务器最大并发活动连接数，1.11.5后支持
	max_fails=number	失败尝试最大次数；超出此处指定的次数时，server将被标记为不可用,默认为1
	fail_timeout=time 	后端服务器标记为不可用状态的连接超时时长，默认10s
	backup				将服务器标记为“备用”，即所有服务器均不可用时才启用
	down				标记为"不可用"，服务器便不再向被标记的服务器进行分配，配合ip_hash使用，实现灰度发布

```

**3、ip_hash源地址hash调度方法**

**4、least_conn**
> 最少连接调度算法，当server拥有不同的权重时其为wlc，当所有后端主机连接数相同时，则使用wrr，适用于长连接

**5、hash key [consistent]**
> 基于指定的key的hash表来实现对请求的调度，此处的key可以直接文本、变量或二者组合
> 作用：将请求分类，同一类请求将发往同一个upstream server，使用consistent参数，将使用ketama一致性hash算法，适用于后端是Cache服务器如varnish时使用

```
hash $request_uri consistent;
hash $remote_addr;
```

**6、keepalive 连接数N**
> 为每个worker进程保留的空闲的长连接数量,可节约nginx端口，并减少连接管理的消耗

**7、health_check[parameters]**
> 健康状态检测机制；只能用于location上下文
> 常用参数：

```
interval=time	检测的频率，默认为5秒
fails=number	判定服务器不可用的失败检测次数；默认为1次
passes=number	判定服务器可用的失败检测次数；默认为1次
uri=uri			做健康状态检测测试的目标uri；默认为/
match=NAME		健康状态检测的结果评估调用此处指定的match配置块
注意：仅对nginx plus有效

```

**8 match name { ... }**
> 对backend server做健康状态检测时，定义其结果判断机制；只能用于http上下文
> 常用的参数：

```
status code [ code ...]: 期望的响应状态码
header HEADER [operator value]：期望存在响应首部，也可对期望的响应首部的值基于比较操作符和值进行比较
body：期望响应报文的主体部分应该有的内容
注意：仅对nginx plus有效

```

#### 如有错误，望批评指正，敬请谅解！









