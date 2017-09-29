---
layout: post
title:  httpd服务基本配置
date:   2017-09-29 08:00:00
categories: Linux 
tags:  Apache httpd基础配置
---

## 介绍

> Apache HTTP Server（简称Apache）是Apache软件基金会的一个开放源码的网页服务器，可以在大多数计算机操作系统中运行，由于其多平台和安全性被广泛使用，是最流行的Web服务器端软件之一。它快速、可靠并且可通过简单的API扩展，将Perl/Python等解释器编译到服务器中。

### 显示服务器版本信息

```shell

1、ServerTokens OS
#显示服务的版本信息与操作系统

选项:
ServerTokens Prod[uctOnly]
	  (e.g.): Server: Apache
ServerTokens Major
	  (e.g.): Server: Apache/2
ServerTokens Minor
	  (e.g.): Server: Apache/2.0
ServerTokens Min[imal]
	  (e.g.): Server: Apache/2.0.41
ServerTokens OS
	  (e.g.): Server: Apache/2.0.41 (Unix)
ServerTokens Full (or not specified)
	  (e.g.): Server: Apache/2.0.41 (Unix) PHP/4.2.2 MyMod/1.2

#可选选项，对应可以显示不同详细程度的信息，建议设定成ServiceToken  prod


2、ServerRoot "/etc/httpd"
#指定服务器的根目录位置
#目录下包括logs、error、modules数据等


3、PidFile run/httpd.pid
#放置PID的文件
#相对于ServerRoot的设定值，放置在/etc/httpd/run/httpd.pid，这里只写出其相对路径，下同


4、Timeout 60
#无论接接收还是发送，当持续连接等待超过60秒，则会中断该次连接

```

### 持久连接

```shell

1、KeepAlive Off     	-->最好将默认的off改为on
#是否允许持续性的连接
#建立连接，每个资源获取完成后不会断开连接，而是继续等待其它的请求完成，默认关闭持久连接

2、MaxKeepAliveRequests 100
#该次连接最大传输数量，0代表不限制
#为了提高效率可以设定大一些

3、KeepAliveTimeout 15
#该次传输完成后等待延时时间，当超过设定值时连接会中断


```

### 多路处理模块

```shell

1、MPM 多路处理模块
   包含三类：prefork, worker, event

<IfModule prefork.c>
StartServers         8		开启的子进程数
MinSpareServers      5		最小空闲的服务
MaxSpareServers      20		最大空闲服务
ServerLimit          256	最多进程数,最大20000
MaxClients           256	最大并发
MaxRequestsPerChild  4000	子进程最多能处理的请求数量
</IfModule>

<IfModule worker.c>
StartServers         4
MaxClients         300
MinSpareThreads     25
MaxSpareThreads     75
ThreadsPerChild     25
MaxRequestsPerChild  0
</IfModule>

```

> - StartServers: 代表启动Apache时就启动的进程数量

> - MinSpareServers、MaxSpareServers: 代表最大最小的备用程序数量

> - MaxClients: 最大同时连接的数量，包括备用的进程

> - MaxRequestsPerChild: 每个程序能够提供的最大传输次数的要求，在处理达到设定个请求之后,子进程将会被父进程终止，这时候子进程占用的内存就会释放(为0时永远不释放)，并重新切换为另一个程序

> 在上面的MaxClients设定当中，要注意不是MaxClients的数量越高越好，每个开启的程序都会占用物理内存，所以要考虑实际情况来进行设定，如果设置太高超出物理内存的范围，效率反而会降低

> 我们可以使用ab命令测试apache服务器性能：

> 示例：[root@Centos6 ~]#ab -c 100 -n 10000  http://172.18.18.18/index.html

```shell

选项:
	-c concurrency：一次性发起的请求个数，默认为1；
	-i：测试时使用HEAD方法，默认为GET；
	-k：启用HTTP长连接请求方式；
	-n requests：发起的模拟请求个数；默认为1个；请求数要大于等于并发连接数；
	-q：静默模式，在请求数大于150个时不输出请求完成百分比；

```

> 那么如果想更改模块的设定可以去更改/etc/sysconfig/httpd来设定使用哪个模块

> 说明简单的命令

```shell

查看静态编译的模块
	httpd  -l
查看静态编译及动态装载的模块
	httpd  -M
对httpdp配置文件文件进行语法检查
	httpd  –t
	service httpd configtest

```

### 修改监听的IP和Port

```shell

Listen 80
#地址及端口服务监听，默认开放在所有的网络接口上

修改监听的IP和Port
Listen [IP:]PORT
	(1) 省略IP表示为0.0.0.0；
	(2) Listen指令至少一个，可重复出现多次
		Listen 80
		Listen 8080
	(3) 修改监听socket，重启服务进程方可生效

Apache官方文档示例：

#设定指定监听的端口
For example, to make the server accept connections on both port 80 and port 8000, use:
	Listen 80
	Listen 8000
	
#设定只对特定的IP端口进行监听
To make the server accept connections on two specified interfaces and port numbers, use
	Listen 192.170.2.1:80
	Listen 192.170.2.5:8000
	
#指定端口IP及https加密通讯协议	
You only need to set the protocol if you are running on non-standard ports. For example, running an https site on port 8443:
	Listen 192.170.2.1:8443 https

```

### DSO动态模块配置

```shell

LoadModule auth_basic_module modules/mod_auth_basic.so
...
以下省略
...
#加载动态模块设置，Apache提供众多的模块

Include conf.d/*.conf
#加载上面的模块的同时也会读取/etc/httpd/conf.d/*.conf的所有文件内容中的设定的模块

配置模块加载格式：
	LoadModule  <mod_name>  <mod_path>
模块文件路径可使用相对路径：
	相对于ServerRoot（默认/etc/httpd）
	
示例：
	LoadModule  auth_basic_module  modules/mod_auth_basic.so

```

> 在动态加载的模块时，如果我们想要添加一些模块最好添加至/etc/httpd/conf.d/*.conf内，这样在进行迁移的时候会比较方便

### 用户主机名相关设置

```shell

User apache
Group apache
#与前面设定的prework、worker等模块启动的进程的属组属主设置
#将来提供的网页文件能否被浏览与该设置有关

ServerAdmin root@localhost
#系统管理员的mail

ServerName www.example.com:80
#设定主机名，如果被注释或者没有指定，则会默认以hostname为标准

UseCanonicalName Off
#是否设定标准的主机名，默认为off

```

> 在许多不同的环境当中，当需要启动多个Apache服务时，或者80端口被占用的时候，可以修改listen的设定端口值

### 定义'Main' server的文档页面路径

```shell

DocumentRoot "/var/www/html"  --> 可以设定成放置首页的目录

#文档路径映射，指向的路径为URL的起始路径
#虽然设置值内容可以变更，但是要注意设置目录的权限及iptables和selinux的状态

<Directory />
    Options FollowSymLinks
    AllowOverride None
</Directory>
#服务器的对'/'的设置

<Directory "/var/www/html">
    Options Indexes FollowSymLinks
    AllowOverride None
    Order allow,deny
    Allow from all
</Directory>	
#对/var/www/html目录进行限制

```
> 下面就来进行介绍，当中的参数的含义

> - #### option (目录参数)
> - &emsp;&emsp;option的设置是表示在指定的目录能够进行的权限操作，后跟1个或多个以空白字符分隔的选项列表，在选项前的+，-表示增加或删除指定选项
> 常见选项
>> - indexes : 如果在此目录下找不到首页文件(默认为index.html)时，列出文件夹的目录(建议不去使用该设置)，首页的文件名还与DirectoryIndex的设定有关

>> - FollowSymLinks : 是否支持软连接，访问符号链接文件所指向的源文件
>> &emsp;&emsp None  全部禁用
>> &emsp;&emsp All   全部允许
		
>> - ExecCGI : 让此目录具有执行CGI权限

> 示例: 

```shell

<Directory /web/docs>
	Options Indexes FollowSymLinks
</Directory>

<Directory /web/docs/spec>
	Options +Includes -Indexes
</Directory>

```

> - #### AllowOverride
> - &emsp;&emsp;是否允许额外的配置文件.htaccess的某些参数覆盖之前的配置指令，只对<directory>设定的语句有效

> 权限类型:

```shell

All 		:全部权限均可覆盖		
AuthConfig 	:仅网页认证的(账号与密码)可覆盖
Indexes 	:允许indexes进行覆盖
Limits 		:允许用户通过Allow、Deny、order管理可浏览的权限
None 		:不可覆盖.htaccess失效

```

> - #### Order、allow和deny
> - &emsp;&emsp;order:定义生效次序，写在后面的表示默认法则

>> - order deny,allow :以deny优先处理，没有写入规则的默认为allow，常用于拒绝所有，允许特定条件

>> - order allow,deny :以allow优先处理，没有写入规则的默认为deny，常用于允许所有，拒绝特定条件

>> - Allow from, Deny from 允许，拒绝指定的条件

>> - 若allow与deny设定的规则有冲突，则以order设定的为准

示例:

```shell

<files "*.txt">				#指定以下设定只对所有以.txt结尾的文件生效
order deny,allow			#定义优先处理次序
deny from 172.18.18.18		#拒绝来自设定IP的访问
allow from 172.18			#允许来自指定的网段的访问
</files>
#来自172.18.18.18的主机能够访问设定的文件，设定失效

<files "*.txt">
order allow,deny
deny from 172.18.18.18
allow from 172.18
</files>
#来自172.18.18.18的主机不能够访问设定的文件，设定有效

```

> 这里说明在上文中的DirectoryIndex的设定

```shell

DirectoryIndex index.html index.html.var
#这里设定了，当客户端进行访问时，默认的去寻找哪个文件，并进行显示

```


### 日志设定

```shell

LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
LogFormat "%h %l %u %t \"%r\" %>s %b" common
LogFormat "%{Referer}i -> %U" referer
LogFormat "%{User-agent}i" agent
#自带定义的日志格式

CustomLog logs/access_log  combined                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      
#默认使用的日志格式为combined  

```

### 设定默认字符集

```shell

AddDefaultCharset UTF-8
#指定了默认字符为UTF-8

中文字符集：GBK, GB2312, GB18030

```

### 定义路径别名

```shell

格式：Alias  /URL/  "/PATH/"

[root@Centos6 ~]#vim /etc/httpd/conf/httpd.conf

Alias /icons/ "/var/www/icons/"  #定义目录别名

<Directory "/var/www/icons">
    Options Indexes MultiViews FollowSymLinks
    AllowOverride None
    Order allow,deny
    Allow from all
</Directory>

ScriptAlias /cgi-bin/ "/var/www/cgi-bin/" #定义脚本别名

<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Order allow,deny
    Allow from all
</Directory>

```

### 基于用户的访问控制

> 如果作为管理者需要对网页的某些资源进行权限的限定，仅特定的用户，通过密码认证才能对资源进行访问，下面我们对认证网页的设定进行介绍如何实现

> 1、首先我们需要先确定要对主站点下的那个目录进行，设定认证网页，假设我们对/var/www/html/protect 目录下的文件进行设定

```shell

#创建受保护的目录以及添加默认的测试网页
[root@Centos6 ~]#mkdir  /var/www/html/protect
[root@Centos6 ~]#echo "hello this is test page" > /var/www/html/protect/index.html

```

> 2、创建httpd的子配置文件/etc/httpd/conf.d/authconfig.conf,并对其进行设定

```shell

[root@Centos6 ~]#vim /etc/httpd/conf.d/authconfig.conf

#添加如下内容
<directory "/var/www/html/protect">	#设定被保护的目录
allowoverride authconfig			#设定网页认证可覆盖
</directory>

```

> 3、准备.htaccess的文件

> 注意：.htaccess文件是放在被保护的文件夹下的

```shell

[root@Centos6 ~]#vim /var/www/html/protect/.htaccess

#添加如下内容
AuthType Basic						
AuthName "protect file"
AuthUserFile "/var/www/.httpdusers"
Require user wang yan

```
> 添加内容含义

>> - AuthType :认证的类型，默认的设定为basic
>>
>> - AuthName :在出现的提示输入账号密码的地方的提示符
>>
>> - AuthUserFile :保护目录放置账号密码的地方，放置位置可以自己指定，但是一般不要放在保护的目录下，防止被窃取
>>
>> - Require :后面为指定在设定的/var/www/.httpdusers内的哪些用户可以来访问，只有写在require后的才能够进行访问，这里指定wang yan 用户能够访问

> 那么我们怎样去设定用户呢

> Apache默认是通过htpasswd来建立的

> 命令格式：```htpasswd [-c] [-s] [-m] [-D] passwdfile username```

**选项**
```shell

-c :自动创建文件，仅应该在文件不存在时使用，如果使用则会覆盖之前创建的用户
-m :默认为md5格式加密
-s :sha格式加密
-D :删除指定用户

```

> 创建.httpdusers的用户(基于basic认证)

```shell

#创建.httpdusers文件及添加用户
[root@Centos6 ~]#htpasswd -c /var/www/.httpdusers wang

New password: 
Re-type new password: 
Adding password for user wang

#查看已经生成的用户
[root@Centos6 ~]#cat /var/www/.httpdusers 

wang:KSq8WAfJdH4vo
yan:9TcqoKvZRr/Dk
test1:wSBF/CxSJVmt2

```

> 在浏览器输入相应的httpd的服务器的网址下对应的被保护的目录，则会提示进行用户登录验证

![登录验证](/assets/pictures/authconfig.png)

> 基于组账号进行认证

> 这里基于上面的basic认证进行设定,同样需要对/etc/httpd/conf.d/authconfig.conf文件进行设定，方法是一样的，这里不再进行说明

```shell

[root@Centos6 ~]#vim /var/www/html/protect/.htaccess

#添加如下内容
AuthType Basic						
AuthName "protect file"
AuthUserFile "/var/www/.httpdusers"
AuthGroupFile"/var/www/.httpdgroupusers"
Require group grpname1 

[root@Centos6 ~]#vim /var/www/html/protect/.httpdgroupusers

#添加组及其成员
grpname1: wang yan   #这里指定能够访问被保护目录的用户

```

### 以上是httpd的内容，如有错误请批评指正！













































