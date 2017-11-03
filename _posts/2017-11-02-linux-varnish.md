---
layout: post
title:  varnish详解及应用的实现
date:   2017-11-02 08:00:00
categories: Linux 
tags:  
---

## 介绍

> Varnish 的作者Poul-Henning Kamp是FreeBSD的内核开发者之一，他认为现在的计算机比起1975年已经复杂许多。在1975年时，储存媒介只有两种：内存与硬盘。但现在计算 机系统的内存除了主存外，还包括了cpu内的L1、L2，甚至有L3快取。硬盘上也有自己的快取装置，因此squid cache自行处理物件替换的架构不可能得知这些情况而做到最佳化，但操作系统可以得知这些情况，所以这部份的工作应该交给操作系统处理，这就是 Varnish cache设计架构。

### 1、varnish系统架构

> varnish主要运行两个进程：Management进程和Child进程(也叫Cache进程)。
>
>> Management进程主要实现应用新的配置、编译VCL、监控varnish、初始化varnish以及提供一个命令行接口等。
>> Management进程会每隔几秒钟探测一下Child进程以判断其是否正常运行，如果在指定的时长内未得到Child进程的回应，Management将会重启此Child进程。

> Child进程包含多种类型的线程，常见的如：
>>	Acceptor线程：接收新的连接请求并响应；
>>	Worker线程：child进程会为每个会话启动一个worker线程，因此，在高并发的场景中可能会出现数百个worker线程甚至更多；
>>	Expiry线程：从缓存中清理过期内容；

> Varnish依赖“工作区(workspace)”以降低线程在申请或修改内存时出现竞争的可能性。在varnish内部有多种不同的工作区，其中最关键的当属用于管理会话数据的session工作区。


### 2、varnish日志

> 为了与系统的其它部分进行交互，Child进程使用了可以通过文件系统接口进行访问的共享内存日志(shared memory log)，因此，如果某线程需要记录信息，其仅需要持有一个锁，而后向共享内存中的某内存区域写入数据，再释放持有的锁即可。而为了减少竞争，每个worker线程都使用了日志数据缓存。
> 共享内存日志大小一般为90M，其分为两部分，前一部分为计数器，后半部分为客户端请求的数据。varnish提供了多个不同的工具如varnishlog、varnishncsa或varnishstat等来分析共享内存日志中的信息并能够以指定的方式进行显示。

### 3、vcl的基本介绍

> Varnish Configuration Language (VCL)是varnish配置缓存策略的工具，它是一种基于“域”(domain specific)的简单编程语言，它支持有限的算术运算和逻辑运算操作、允许使用正则表达式进行字符串匹配、允许用户使用set自定义变量、支持if判断语句，也有内置的函数和变量等。使用VCL编写的缓存策略通常保存至.vcl文件中，其需要编译成二进制的格式后才能由varnish调用。事实上，整个缓存策略就是由几个特定的子例程如vcl_recv、vcl_fetch等组成，它们分别在不同的位置(或时间)执行，如果没有事先为某个位置自定义子例程，varnish将会执行默认的定义。
> VCL策略在启用前，会由management进程将其转换为C代码，而后再由gcc编译器将C代码编译成二进制程序。编译完成后，management负责将其连接至varnish实例，即child进程。正是由于编译工作在child进程之外完成，它避免了装载错误格式VCL的风险。因此，varnish修改配置的开销非常小，其可以同时保有几份尚在引用的旧版本配置，也能够让新的配置即刻生效。编译后的旧版本配置通常在varnish重启时才会被丢弃，如果需要手动清理，则可以使用varnishadm的vcl.discard命令完成。

### 4、varnish的后端存储

> varnish支持多种不同类型的后端存储，这可以在varnishd启动时使用-s选项指定。后端存储的类型包括：
	- (1)`file`：使用特定的文件存储全部的缓存数据，并通过操作系统的mmap()系统调用将整个缓存文件映射至内存区域(如果条件允许)；
	- (2)`malloc`：(默认使用)使用malloc()库调用在varnish启动时向操作系统申请指定大小的内存空间以存储缓存对象；
	- (3)`persistent(experimental)`：与file的功能相同，但可以持久存储数据(即重启varnish数据时不会被清除)；仍处于测试期；

> varnish无法追踪某缓存对象是否存入了缓存文件，从而也就无从得知磁盘上的缓存文件是否可用，因此，file存储方法在varnish停止或重启时会清除数据。而persistent方法的出现对此有了一个弥补，但persistent仍处于测试阶段，例如目前尚无法有效处理要缓存对象总体大小超出缓存空间的情况，所以，其仅适用于有着巨大缓存空间的场景。

> 选择使用合适的存储方式有助于提升系统性，从经验的角度来看，建议在内存空间足以存储所有的缓存对象时使用malloc的方法，反之，file存储将有着更好的性能的表现。然而，需要注意的是，varnishd实际上使用的空间比使用-s选项指定的缓存空间更大，
> 一般说来，其需要为每个缓存对象多使用差不多1K左右的存储空间，这意味着，对于100万个缓存对象的场景来说，其使用的缓存空间将超出指定大小1G左右。另外，为了保存数据结构等，varnish自身也会占去不小的内存空间。
> file中的granularity用于设定缓存空间分配单位，默认单位是字节，所有其它的大小都会被圆整。

### 5、varnish的工作原理及过程

> 官方工作原理及过程如下图

![](/assets/pictures/varnish/yuanli.png)

> 工作过程解释说明：

> vcl_recv的结果如果可以查询缓存并可以识别，那就要到vcl_hash这步了，如果无法识别那就通过pipe(管道)送给vcl_pipe，如果能识别，但不是一个可缓存的对象(例如：包含用户的账号和密码或者是包含用户相关的cookie信息就不能够将缓存信息放置到缓存服务器上)，那就通过pass送到vcl_pass去，vcl_hash之后就可查看缓存中有没有了，有这个请求的对象就表示命中 (vcl_hit)，如果没有那就表示未命中(vcl_miss)，如果命中的就可以直接通过deliver直接送给vcl_deliver响应了，如果未命中就通过fetch交给vcl_fatch去后端服务器上去取数据，取回数据之后如果数据可以缓存就缓存(cache)，本地缓存完之后再构建响应， 如果不可以缓存就不做缓存交给vcl_deliver响应了；而如果命中了交给vcl_pass，交给pass之后就要到Fetch objet from backend后端服务器上去取数据了，vcl是在其中的状态不断转换中，也叫vcl的有限状态机。

###  varnish的配置

> varnish的程序环境：

```

#配置文件
/etc/varnish/varnish.params： 配置varnish服务进程的工作特性，例如监听的地址和端口，缓存机制；
/etc/varnish/default.vcl：配置各Child/Cache线程的缓存策略；
#主程序：
	/usr/sbin/varnishd
#CLI interface：
	/usr/bin/varnishadm
#Shared Memory Log交互工具：
	/usr/bin/varnishhist
	/usr/bin/varnishlog
	/usr/bin/varnishncsa
	/usr/bin/varnishstat
	/usr/bin/varnishtop		
#测试工具程序：
	/usr/bin/varnishtest
#VCL配置文件重载程序：
	/usr/sbin/varnish_reload_vcl
#Systemd Unit File：
	/usr/lib/systemd/system/varnish.service
#varnish服务
	/usr/lib/systemd/system/varnishlog.service
	/usr/lib/systemd/system/varnishncsa.service	
		
#varnish启动代码
				
 varnishd -f /etc/varnish/default.vcl -s file,/var/varnish_cache,1G -T 127.0.0.1:2000 -a 0.0.0.0:6082

各参数的含义如下：
-f 指定 varnish 的配置文件位置
-s 指定 varnish 缓存存放的方式，常用的方式有：“-s file,<dir_or_file>,<size>”。
-T address:port 设定 varnish 的 telnet 管理地址及其端口
-a address:port 表示 varnish 对 http 的监听地址及其端口


```

####  查看/etc/varnish/varnish.params选项参数

```

[root@centos7 /etc/varnish]#cat varnish.params 
#重新启动时是否重新读取vcl，并重新编译
RELOAD_VCL=1

#默认读取的vcl文件
VARNISH_VCL_CONF=/etc/varnish/default.vcl

#varnish监听的端口，默认为6081
VARNISH_LISTEN_PORT=6081

#管理监听的接口的地址及端口
VARNISH_ADMIN_LISTEN_ADDRESS=127.0.0.1
VARNISH_ADMIN_LISTEN_PORT=6082

#管理接口的秘钥文件放置处
VARNISH_SECRET_FILE=/etc/varnish/secret

#定义存储的类型
VARNISH_STORAGE="malloc,256M"

#定义进程默认使用的用户和组
VARNISH_USER=varnish
VARNISH_GROUP=varnish

# 其他选项可以查看varnishd(1)的man帮助
#定义进程池数，线程最大值最小值，线程时长
#DAEMON_OPTS="-p thread_pool_min=5 -p thread_pool_max=500 -p thread_pool_timeout=300"

```

> 定义后端的服务器

```
vim  /etc/varnish/default.vcl
#这里先进行设定成另一台主机为后端的web服务器
backend default {
    .host = "172.18.18.30";
    .port = "80";
}

#启动varnish服务
systemctl start vrnish

```

> 管理varnish服务

```

#本机登录varnish服务器进行管理
[root@centos7 /etc/varnish]#varnishadm -S /etc/varnish/secret -T 127.0.0.1:6082
200        
-----------------------------
Varnish Cache CLI 1.0
-----------------------------
Linux,3.10.0-514.el7.x86_64,x86_64,-smalloc,-smalloc,-hcritbit
varnish-4.0.4 revision 386f712

Type 'help' for command list.
Type 'quit' to close CLI session.

vcl.list
200        
active          0 boot

#通过help命令查看有哪些命令选项
help
200        
help [<command>]
ping [<timestamp>]
auth <response>
quit
banner
status
start
stop
vcl.load <configname> <filename>
vcl.inline <configname> <quoted_VCLstring>
vcl.use <configname>
vcl.discard <configname>
vcl.list
param.show [-l] [<param>]
param.set <param> <value>
panic.show
panic.clear
storage.list
vcl.show [-v] <configname>
backend.list [<backend_expression>]
backend.set_health <backend_expression> <state>
ban <field> <operator> <arg> [&& <field> <oper> <arg>]...
ban.list

#配置文件相关：
	vcl.list 
	vcl.load：装载，加载并编译；
	vcl.use：激活；
	vcl.discard：删除；
	vcl.show [-v] <configname>：查看指定的配置文件的详细信息；
				
#运行时参数：
	param.show -l：显示列表；
	param.show <PARAM>
	param.set <PARAM> <VALUE>
	
#缓存存储：
	storage.list
	
#后端服务器：
	backend.list 

```

### varnish状态引擎(state engine)

> VCL用于让管理员定义缓存策略，而定义好的策略将由varnish的management进程分析、转换成C代码、编译成二进制程序并连接至child进程。varnish内部有几个所谓的状态(state)，在这些状态上可以附加通过VCL定义的策略以完成相应的缓存处理机制，因此VCL也经常被称作“域专用”语言或状态引擎，“域专用”指的是有些数据仅出现于特定的状态中。

#### 1、VCL状态引擎

> 在VCL状态引擎中，状态之间具有相关性，但彼此间互相隔离，每个引擎使用return(x)来退出当前状态并指示varnish进入下一个状态。

> varnish开始处理一个请求时，首先需要分析HTTP请求本身，比如从首部获取请求方法、验正其是否为一个合法的HTT请求等。当这些基本分析结束后就需要做出第一个决策，即varnish是否从缓存中查找请求的资源。这个决定的实现则需要由VCL来完成，简单来说，要由vcl_recv方法来完成。如果管理员没有自定义vcl_recv函数，varnish将会执行默认的vcl_recv函数。然而，即便管理员自定义了vcl_recv，但如果没有为自定义的vcl_recv函数指定其终止操作(terminating)，其仍将执行默认的vcl_recv函数。事实上，varnish官方强烈建议让varnish执行默认的vcl_recv以便处理自定义vcl_recv函数中的可能出现的漏洞。

#### 2、VCL语法

> VCL的设计参考了C和Perl语言，因此，对有着C或Perl编程经验者来说，其非常易于理解。其基本语法说明如下：

- (1)//、#或/* comment */用于注释
- (2)sub $name 定义函数     用来实现类似于iptables当中的钩子函数，在不同的位置设定sub域
- (3)不支持循环，有内置变量
- (4)使用终止语句return()，没有返回值    return()用于退出当前的域，执行下一个域，用户自定义最好不要使用return()，让其使用自定义的配置文件进行后续的判断
- (5)域专用
- (6)操作符：=(赋值)、==(等值比较)、~(模式匹配)、!(取反)、&&(逻辑与)、||(逻辑或)
	
> VCL的函数不接受参数并且没有返回值，因此，其并非真正意义上的函数，这也限定了VCL内部的数据传递只能隐藏在HTTP首部内部进行。
> VCL的return语句用于将控制权从VCL状态引擎返回给Varnish，而非默认函数，这就是为什么VCL只有终止语句而没有返回值的原因。
> 同时，对于每个“域”来说，可以定义一个或多个终止语句，以告诉Varnish下一步采取何种操作，如查询缓存或不查询缓存等。

#### 3、VCL的内置函数

> VCL提供了几个函数来实现字符串的修改，添加bans，重启VCL状态引擎以及将控制权转回Varnish等。

```
regsub(str“查找字符串”,“regex查找什么”,sub“替换成什么”)
regsuball(str,regex,sub)：这两个用于基于正则表达式搜索指定的字符串并将其替换为指定的字符串；但regsuball()可以将str中能够被regex匹配到的字符串统统替换为sub，regsub()只替换一次；
ban(expression)：
ban_url(regex)：Bans所有其URL能够由regex匹配的缓存对象，匹配进行清理；
purge：从缓存中挑选出某对象以及其相关变种一并删除，这可以通过HTTP协议的PURGE方法完成；
hash_data(str)：
return()：当某VCL域运行结束时将控制权返回给Varnish，并指示Varnish如何进行后续的动作；其可以返回的指令包括：lookup、pass、pipe、hit_for_pass、fetch、deliver和hash等；但某特定域可能仅能返回某些特定的指令，而非前面列出的全部指令；
return(restart)：重新运行整个VCL，即重新从vcl_recv开始进行处理；每一次重启都会增加req.restarts变量中的值，而max_restarts参数则用于限定最大重启次数。

```

#### 4、变量类型：

> 内建变量：

```
req.*：request，表示由客户端发来的请求报文相关；
	req.http.*
		req.http.User-Agent, req.http.Referer, ...
bereq.*：由varnish发往BE主机的httpd请求相关；
	bereq.http.*
beresp.*：由BE主机响应给varnish的响应报文相关；
	beresp.http.*
resp.*：由varnish响应给client相关；
obj.*：存储在缓存空间中的缓存对象的属性；只读；

#常用变量：
bereq.*, req.*：
	bereq.http.HEADERS
	bereq.request：请求方法；
	bereq.url：请求的url；
	bereq.proto：请求的协议版本；
	bereq.backend：指明要调用的后端主机；
	
	req.http.Cookie：客户端的请求报文中Cookie首部的值； 
	req.http.User-Agent ~ "chrome"
						
beresp.*, resp.*：
	beresp.http.HEADERS
	beresp.status：响应的状态码；
	reresp.proto：协议版本；
	beresp.backend.name：BE主机的主机名；
	beresp.ttl：BE主机响应的内容的余下的可缓存时长；
						
obj.*
	obj.hits：此对象从缓存中命中的次数；
	obj.ttl：对象的ttl值
						
server.*
	server.ip：varnish主机的IP；
	server.hostname：varnish主机的Hostname；
client.*
	client.ip：发请求至varnish主机的客户端IP；
	
#还可以用户自定义：
	set 
	unset 
示例：
    set resp.http.X-cache = "Hit" + server.ip ;   自定义成新的设定值

```

> 下面对常用的函数进行详细的介绍

#### 1、vcl_recv

> vcl_recv是在Varnish完成对请求报文的解码为基本数据结构后第一个要执行的子例程，它通常有四个主要用途：
	
- (1)修改客户端数据以减少缓存对象差异性；比如删除URL中的www.等字符；
- (2)基于客户端数据选用缓存策略；比如仅缓存特定的URL请求、不缓存POST请求等；
- (3)为某web应用程序执行URL重写规则；
- (4)挑选合适的后端Web服务器；

> 可以使用下面的终止语句，即通过return()向Varnish返回的指示操作：

- pass：绕过缓存，即不从缓存中查询内容或不将内容存储至缓存中；
- pipe：不对客户端进行检查或做出任何操作，而是在客户端与后端服务器之间建立专用“管道”，并直接将数据在二者之间进行传送；此时，keep-alive连接中后续传送的数据也都将通过此管道进行直接传送，并不会出现在任何日志中；
- lookup：在缓存中查找用户请求的对象，如果缓存中没有其请求的对象，后续操作很可能会将其请求的对象进行缓存；
- error：由Varnish自己合成一个响应报文，一般是响应一个错误类信息、重定向类信息或负载均衡器返回的后端web服务器健康状态检查类信息；
例如：return(pass)

> vcl_recv也可以通过精巧的策略完成一定意义上的安全功能，以将某些特定的攻击扼杀于摇篮中。同时，它也可以检查出一些拼写类的错误并将其进行修正等。

> Varnish默认的vcl_recv专门设计用来实现安全的缓存策略，它主要完成两种功能：
- (1)仅处理可以识别的HTTP方法，并且只缓存GET和HEAD方法；
- (2)不缓存任何用户特有的数据；

> 安全起见，一般在自定义的vcl_recv中不要使用return()终止语句，而是再由默认vcl_recv进行处理，并由其做出相应的处理决策。

#### 2、vcl_fetch

> 如前面所述，相对于vcl_recv是根据客户端的请求作出缓存决策来说，vcl_fetch则是根据服务器端的响应作出缓存决策。在任何VCL状态引擎中返回的pass操作都将由vcl_fetch进行后续处理。vcl_fetch中有许多可用的内置变量，
> 比如最常用的用于定义某对象缓存时长的beresp.ttl变量。
> 通过return()返回给varnish的操作指示有：
- (1)deliver：缓存此对象，并将其发送给客户端(经由vcl_deliver)；
- (2)hit_for_pass：不缓存此对象，但可以导致后续对此对象的请求直接送达到vcl_pass进行处理；
- (3)restart：重启整个VCL，并增加重启计数；超出max_restarts限定的最大重启次数后将会返回错误信息；
- (4)error code [reason]：返回指定的错误代码给客户端并丢弃此请求；

> 默认的vcl_fetch放弃了缓存任何使用了Set-Cookie首部的响应。



#### 1、设定相应报文中是否命中

> 配置/etc/varnish/default.vcl进行设置

```
#定义后端的服务器
backend web1 {
    .host = "172.18.18.30";
    .port = "80";
}

#在sub vcl_deliver设定投递报文
sub vcl_deliver {
    if (obj.hits>0) {   
		set resp.http.X-cache = "Hit" + server.ip ;
    }else {
		set resp.http.X-cache = "Miss" + server.ip ; 
  }
}

解释说明：obj.hits是内建变量，用于保存某缓存项的从缓存中命中的次数
		  为0，表示缓存服务器中没有请求的资源，需要去后端取对应的资源
		  大于0，表示缓存服务器中有请求的资源，在设定的缓存生效时间内都不会去访问后端服务器

```

> 查看访问特定的资源响应报文是否，出现设定的提示信息

> 第一次访问，响应报文

![miss](/assets/pictures/varnish/miss-message)

> 在此刷新，响应报文

![hit](/assets/pictures/varnish/hit-message)


#### 2、强制对某类请求资源不检查缓存

```

#之前的配置不变，添加新的内容至/etc/varnish/default.vcl文件中
vcl_recv {
	if (req.url ~ "(?i)^/(login|admin)") {
		return(pass);   直接送往后端查询
	}
}

#当访问后端服务器主目录下的以.(jpg|gif)结尾的，就将其送至pass，不会再缓存服务器上进行查询

```

#### 3、对特定的资源，取消其特定的标识，并设定缓存时长
> 对于特定类型的资源，例如公开的图片等，取消其私有标识，使其可以进行缓存的查询，并强行设定其可以由varnish缓存的时长,由缓存服务器进行响应
> 例如用户的图片等不重要的信息，没必要进行cookie认证去后端服务器进行查询，把cookie去掉后就能够让其进行缓存的查询，能够减少后端服务器的压力

```
if (beresp.http.cache-control !~ "s-maxage") {   			#后端服务器没有匹配到缓存的时长
	if (bereq.url ~ "(?i)\.(jpg|jpeg|png|gif|css|js)$") {	#并且请求的资源类型是图片之类的
		unset beresp.http.Set-Cookie;						#撤销后端服务器请求报文中的cookie
		set beresp.ttl = 3600s;								#定义由varnish指定的缓存时长
	}
}

```

#### 4、设定显示请求资源时的真实的请求者

```
#定义在vcl_recv中
if (req.restarts == 0) {    		#URL重写了就会重新的发起请求
	if (req.http.X-Fowarded-For) {	#请求的报文当中是否能够匹配到X-Fowarded-For（通过转发发送到本机的报文）
		set req.http.X-Forwarded-For = req.http.X-Forwarded-For + "," + client.ip;
	} else {
		set req.http.X-Forwarded-For = client.ip;
	}
}

```

> 可通过在日志当中增加一项进行观察
> LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\" \"%{X-Forward-For}i\""  combined

> 查看后端服务器http日志
> [root@Centos6 ~]#tail /var/log/httpd/access_log -f

![real-ip-log](/assets/pictures/varnish/real-ip-log.png)

### 5、缓存对象修剪

> 清理缓存对象有两种：purge,ban

####  purge
> 只对某特定的主机来设定

```
(1) 定义purge的操作域，在自带的默认的脚本中（builtin中已经定义），这里可不用指定，在调用的时候会自动去调用默认当中的设置
	sub vcl_purge {
		return (synth(200,"Purged"));
	}
				
(2) 定义执行purge操作的条件
	sub vcl_recv {
		if (req.method == "PURGE") {  
			return(purge);  调用purge，如请求的方法为purge则就会将缓存服务器当中的缓存删除
							在此请求会访问后端的服务器
		}
	
	}

```

> 但是，如果不加以限制，任何人只要知道如何去对相应的服务器进行purge操作，都会导致缓存服务器当中的缓存文件失效，相当于间接的攻击，所以需要对此操作权限进行控制

```
#添加此类请求的访问控制法则,添加访问列表的白名单
acl purgers {
	"127.0.0.0"/8;  定义只有本机才能够有权限使用purge
	"10.1.0.0"/16;
}
				
sub vcl_recv {
	if (req.method == "PURGE") {
		if (!client.ip ~ purgers) {
			return(synth(405,"Purging not allowed for " + client.ip));
		}
		return(purge);
	}
	
}

```

####  ban

> 将针对某一类，某范围当中的主机进行设定

```
(1) varnishadm：(命令行当中使用)
	ban <field> <operator> <arg>
	
	示例：
		ban req.url ~ .jpg$  将所有的以jpg结尾的都会将缓存禁止掉
		
(2) 在配置文件中定义，使用ban()函数；
				
	示例：
	if (req.method == "BAN") {
		ban("req.http.host == " + req.http.host + " && req.url == " + req.url);
		# Throw a synthetic page so the request won't go to the backend.
		return(synth(200, "Ban added"));
	}	
			
ban req.http.host == www.ilinux.io && req.url == /test1.html


```


### 设定使用多个后端主机，实现动静分离

> 就是来进行设定，动静分离，对于静态文件基本就是不会改变的，通常更新发布时，会更改动态文件

```

backend web1 {
	.host = "172.16.100.6";
	.port = "80";
}

backend web2 {
	.host = "172.16.100.7";
	.port = "80";
}
			
sub vcl_recv {				
	if (req.url ~ "(?i)\.php$") {
		set req.backend_hint = web1;   动态的访问送往web1
	} else {
		set req.backend_hint = web2;   其他的静态送往web2
	}	
	
	
}


#通过定义组进行负载均衡
varnish module； 
	使用前需要导入：
		import directors；
 
负载均衡示例：
	import directors;    # 加载directors模块 

	backend server1 {
		.host = "172.16.100.6";
		.port = "80"; 
}
	backend server2 {
		.host = "172.16.100.7";
		.port = "80";
}
	sub vcl_init {
		new GROUP_NAME = directors.round_robin();
			GROUP_NAME.add_backend(server1);
			GROUP_NAME.add_backend(server2);
}

	sub vcl_recv {
		set req.backend_hint = GROUP_NAME.backend();
}

#基于cookie的session sticky（会话绑定）：
	sub vcl_init {
		new  h = directors.hash();
		h.add_backend(server1, 1);   
		h.add_backend(server2, 1);   
	}

	sub vcl_recv {
		set req.backend_hint = h.backend(req.http.cookie);
	}

```

### 健康状态检测

> Varnish可以检测后端主机的健康状态，在判定后端主机失效时能自动将其从可用后端主机列表中移除，而一旦其重新变得可用还可以自动将其设定为可用。为了避免误判，Varnish在探测后端主机的健康状态发生转变时(比如某次探测时某后端主机突然成为不可用状态)，通常需要连续执行几次探测均为新状态才将其标记为转换后的状态。
> 每个后端服务器当前探测的健康状态探测方法通过.probe进行设定，其结果可由req.backend.healthy变量获取，
> 也可通过varnishlog中的Backend_health查看或varnishadm的debug.health查看。

```

backend web1 {
	.host = "www.magedu.com";
	.probe = {     					 #健康检测模块，也可以将其单独定义成probe模块然后进行调用
		.url = "/.healthtest.html";
		.interval = 1s;
		.window = 5;
		.threshold = 3;
	}
}

# .probe中的探测指令常用的有：

(1) .url：探测后端主机健康状态时请求的URL，默认为“/”；
(2) .request: 探测后端主机健康状态时所请求内容的详细格式，定义后，它会替换.url指定的探测方式；比如：
	.request =
		"GET /.healthtest.html HTTP/1.1"
		"Host: www.magedu.com"
		"Connection: close";
(3) .window：设定在判定后端主机健康状态时基于最近多少次的探测进行，默认是8；
(4) .threshold：在.window中指定的次数中，至少有多少次是成功的才判定后端主机正健康运行；默认是3；
(5) .initial：Varnish启动时对后端主机至少需要多少次的成功探测，默认同.threshold；
(6) .expected_response：期望后端主机响应的状态码，默认为200；
(7) .interval：探测请求的发送周期，默认为5秒；
(8) .timeout：每次探测请求的过期时长，默认为2秒；

因此，如上示例中表示每隔1秒对此后端主机www.magedu.com探测一次，请求的URL为http://www.magedu.com/.healthtest.html，
在最近5次的探测请求中至少有2次是成功的(响应码为200)就判定此后端主机为正常工作状态。

如果Varnish在某时刻没有任何可用的后端主机，它将尝试使用缓存对象的“宽容副本”(graced copy)，当然，此时VCL中的各种规则依然有效。因此，更好的办法是在VCL规则中判断req.backend.healthy变量显示某后端主机不可用时，为此后端主机增大req.grace变量的值以设定适用的宽容期限长度。

在varnishadm命令当中进行查看
backend.list 

同时也可以手动设定backend主机的状态：
    sick：管理down;   示例：backend.set.healthy  server1  sick 
    healthy：管理up；
    auto：probe auto；


在设定健康状态监测的同时还可设置后端的主机属性：
backend BE_NAME {
	.connect_timeout = 0.5s;
	.first_byte_timeout = 20s;
	.between_bytes_timeout = 5s; #两次连接的中间间隔时长
	.max_connections = 50;       #设定最大的连接时长
}	
	
```

### 线程相关的参数

> varnish为每个连接使用一个线程，因此，其worker线程的最大数决定了varnish的并发响应能力。下面是线程池相关的各参数及其配置：

```
thread_pool_add_delay      2 [milliseconds]		添加线程的延时时间
thread_pool_add_threshold  2 [requests]
thread_pool_fail_delay     200 [milliseconds]
thread_pool_max            500 [threads]
thread_pool_min            5 [threads]			进程当中的线程数
thread_pool_purge_delay    1000 [milliseconds]
thread_pool_stack          65536 [bytes]
thread_pool_timeout        120 [seconds]
thread_pool_workspace      16384 [bytes]
thread_pools               2 [pools]   			定义一共几个进程池
thread_stats_rate          10 [requests]

```

> 其中最关键的当属thread_pool_max和thread_pool_min，它们分别用于定义每个线程池中的最大线程数和最少线程数。因此，在某个时刻，至少有thread_pool_min*thread_pools个worker线程在运行，但至多不能超出thread_pool_max*thread_pools个。根据需要，这两个参数的数量可以进行调整，varnishstat命令的n_wrk_queued可以显示当前varnish的线程数量是否足够，如果队列中始终有不少的线程等待运行，则可以适当调大thread_pool_max参数的值。但一般建议每台varnish服务器上最多运行的worker线程数不要超出5000个。
> 当某连接请求到达时，varnish选择一个线程池负责处理此请求。而如果此线程池中的线程数量已经达到最大值，新的请求将会被放置于队列中或被直接丢弃。默认线程池的数量为2，这对最繁忙的varnish服务器来说也已经足够。

































































































