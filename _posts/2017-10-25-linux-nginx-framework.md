---
layout: post
title:  Nginx的程序架构
date:   2017-10-25 08:00:00
categories: Linux 
tags:  nginx程序架构、框架
---

### Nginx的程序架构

#### **Nginx进程：**

> nginx与Apache的preform模式相同进程，为master/worker模式

> - 一个master进程：负载加载和分析配置文件、管理worker进程、平滑升级
> - 一个或多个worker进程：处理并响应多个用户请求
> - 缓存相关的进程：
>  - cache loader：载入缓存对象
>  - cache manager：管理缓存对象

#### **Nginx模块：**

> nginx高度模块化，但其模块早期不支持DSO机制;1.9.11版本支持动态装载和卸载

> 模块分类：

- 核心模块：
  - **core module**
- 常用的标准模块：
  - **HTTP modules**：ngx_http_*
	- **ngx_http_core_modules** 	http核心功能模块（重要）
	- **ngx_http_ssl_module** 		http信道加密模块（重要）
	- **ngx_http_upstream_module** 	http定义服务器组模块（重要）
	- **ngx_http_fastcgi|uWSGI|SCGI_module** http web api接口模块（重要）
	- **ngx_http_proxy_module** 	http反向代理模块（重要）
	- `ngx_http_gzip_module` 		http gzip压缩传输模块（较重要）
	- `ngx_http_log_module` 		http日志模块（较重要）
	- `ngx_http_referer_module` 	http防盗链模块（较重要）
	- `ngx_http_rewrite_module` 	http重定向模块（较重要）
	- `ngx_http_access_module` 		http权限控制模块
	- `ngx_http_auth_basic_module` 	http认证模块
	- `ngx_http_stub_status_module` http状态模块
	- `ngx_http_headers_module` 	http首部信息模块
  - **Mail modules**：用的少
  - **Stream modules**：`ngx_stream_core_module` http的伪四层负载均衡模块
- 第三方模块

#### **Nginx的功用：**

- 静态的web资源服务器；(图片服务器，或js/css/html/txt等静态资源服务器)
- 结合FastCGI/uwSGI/SCGI等协议反代动态资源请求；
- http/https协议的反向代理；
- imap4/pop3协议的反向代理；
- tcp/udp协议的请求转发（反向代理）；

### Nginx的安装

> 官方：http://nginx.org/packages/centos/7/x86_64/RPMS,官网会提供yum源

> Fedora-EPEL:https://mirrors.aliyun.com/epel/7/x86_64/


### NGINX目录及结构命令

- 主程序文件：`/usr/sbin/nginx`
- systemd服务：`nginx.service`
- 配置文件(`/etc/nginx`下）：
  - 主配置文件：`nginx.conf`（include了`conf.d/*.conf`）
  - fastcgi， uwsgi，scgi等协议相关的配置文件：`fastcgi_params`、`uwsgi_params`、`scgi_params`
  - 支持的MIME类型配置文件：`mime.types`

> nginx的命令：

```
Nginx：默认为启动nginx
	-h 查看帮助选项
	-t 测试nginx语法错误
	-c filename 指定配置文件(default: /etc/nginx/nginx.conf)
	-s signal 发送信号给master进程，signal可为：stop, quit,reopen, reload
	
示例：-s stop 停止
		nginx -s reload 加载配置文件
	  -g directives 在命令行中指明全局指令
```

### NGINX主配置文件

> 放在/etc/nginx/nginx.conf的文件

#### 配置文件的结构：

> - **段**：如`http{}` 、`event{}`、`server{}`、`location{}`等，段可以并列，也可以嵌套。
> - **块**：指的就是在段里定义的一组directives（配置指令）。
> - **directive**：指的就是块里的一条条配置指令。
> - 配置指令方法：`directive value [value2 ...];`

> 注意：
> (1) 指令必须以分号结尾。
> (2)嵌套的段是有层级结构的，比如http段里，遵循http{}–>server{}–>location{}这种嵌套结构。
> (3) 支持使用配置变量：

> - 内建变量：由Nginx模块引入，可直接引用。
> - 自定义变量：由用户使用set命令定义。
>> - 定义变量：`set variable_name value;`。
>> - 引用变量：`$variable_name`

#### 主配置文件结构：

```
# main block：主配置段，也即全局配置段,对http，mail都有效
...

# 事件驱动相关的配置段
events { 
    ...			
	   }    
	   
# http block：http/https 协议相关的公共配置段
http {
		...
    server {			每个server用于定义一个虚拟主机
        # server block：虚拟主机公共相关配置段
        location URL {
            # location block：资源位置相关公共配置段
					  }
        ... # 其他location
		   }    
    ...  	# 其他server
     }     

#mail 协议相关配置段
mail {
    ...
}

#stream 服务器相关配置段
stream {
    ...
}

```

### main block主配置段，也即全局配置段

> main block常见配置指令分类：

> - 正常运行必备配置
> - 优化性能相关配置
> - 调试及定位问题相关配置
> - 事件驱动相关配置

#### **正常运行必备的配置：**

```

1、user USERNAME;

指定worker进程的运行身份，如组不指定，默认和用户名同名。如果不写user字段，默认为nobody用户和nobody组。

2、pid /PATH/TO/PID_FILE;

指定存储nginx主进程进程号码的文件路径

3、 include file | mask;

指明包含进来的其它配置文件片断

4、load_module file 

模块加载配置文件：/usr/share/nginx/modules/*.conf
指明要装载的动态模块路径。
例如`geoip.conf`文件里：`load_module "/usr/lib64/nginx/modules/ngx_http_geoip_module.so";`

```

#### **性能优化相关的配置：**

```

1、worker_processes NUMBER | auto;

worker进程的数量；NUMBER通常应该等于小于当前主机的cpu的物理核心数。
auto：自动为当前主机物理CPU核心数。
例如：worker_processes 2

2、worker_cpu_affinity cpumask ...;

worker_cpu_affinity  AUTO | CPUMASK;
配置cpumask可以指定绑定CPU,提高缓存命中率

示例：
CPU MASK：
0000 0001：0号CPU
0000 0010：1号CPU
0000 0100：2号CPU
... ...

如nginx进程数为4
worker_cpu_affinity 0001 0010 0100 1000;就是把4个nginx进程分别绑在`0123`号cpu上。
worker_cpu_affinity 0101 1010;把第一个nginx进程绑在`0和2`号cpu上，第二个cpu进程绑在`1和3`号cpu上

3、worker_priority NUMBER;

指定worker进程的nice值，设定worker进程优先级：[-20,19]

4、worker_rlimit_nofile NUMBER;

单个worker进程所能够打开的文件数量上限,如65535，太大也没有意义，进程一共有65535个还要去掉系统保留的

```

#### **调试和定位问题相关配置：**

```

1、daemon on|off;

是否以守护进程方式运行nignx，默认是on，守护进程方式

2、master_process on|off;

是否以master/worker模型运行nginx；默认为on，off将不启动worker

3、error_log PATH/TO/LOGFILE [LEVEL];

PATH/TO/LOGFILE 为日志文件路径
LEVEL为日志级别,一般不修改，出于调试需要，可设定为debug。
系统默认为：`error_log /var/log/nginx/error.log;`

```

#### **事件驱动相关的配置:**

> 在events里定义

```
events {
    ...
}

```

```
1、worker_connections number;

每个worker进程所能够打开的最大并发连接数数量，如 10240
总最大并发数：worker_processes * worker_connections

2、use METHOD

指明并发连接请求的处理方法 ,一般不用设置，Nginx会默认自动选择适合系统的最优方法。

如果系统是Linux，会自动使用epoll方法；如果是FreeBSD和macOS，会自动使用kqueue；如果是Solaris和HP/UX，会自动使用/dev/poll
有两个普通方法select和poll，已经被淘汰，不做讨论。

3、accept_mutex on | off

> 是否接受互斥。
> 指的是处理新的连接请求的方法；on指由各个worker轮流处理新请求 ，Off指每个新请求的到达都会通知(唤醒)所有的worker进程，但 只有一个进程可获得连接，造成“惊群”，影响性能，默认`on`

```