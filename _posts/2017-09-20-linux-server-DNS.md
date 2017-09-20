---
layout: post
title:  DNS服务
date:   2017-09-20 08:00:00
categories: Linux 
tags:  DNS
---

## 简介

> &emsp;&emsp;DNS 是计算机域名系统 (Domain Name System 或Domain Name Service) 的缩写，它是由解析器和域名服务器组成的。域名服务器是指保存有该网络中所有主机的域名和对应IP地址，并具有将域名转换为IP地址功能的服务器。其中域名必须对应一个IP地址，而IP地址不一定有域名。域名系统采用类似目录树的等级结构。域名服务器为C/S模式中的服务器方，它主要有两种形式:主服务器和转发服务器。将域名映射为IP地址的过程就称为"域名解析"。
> 在Internet上域名与IP地址之间是一对一(或者多对一)的，也可采用DNS轮循实现一对多，域名虽然便于人们记忆，但机器之间只认IP地址，它们之间的转换工作称为域名解析，域名解析需要由专门的域名解析服务器来完成，DNS就是进行域名解析的服务器。DNS 命名用于 Internet等 TCP/IP网络中，通过用户友好的名称查找计算机和服务。当用户在应用程序中输入 DNS 名称时，DNS 服务可以将此名称解析为与之相关的其他信息，
> 如 IP 地址。因为，你在上网时输入的网址，是通过域名解析系统解析找到了相对应的IP地址，这样才能上网。其实，域名的最终指向是IP。


> 本篇文章将对DNS的几种常见的功能进行配置实现，DNS的基本概念这里不再过多的介绍

#### 有关DNS的几个重要的相关文件

> 在进行深入介绍时先来看一下DNS的几个重要的文件

```shell

/etc/named.conf		#DNS的主配置文件
/var/named		#数据库默认存放的位置
/var/run/named		#named程序执行时默认将pid-file放置在此目录内
/var/named/named.ca	#根域的文件

```

> DNS的端口是怎样的呢，我们来看一下

```shell

#查看DNS的服务端口，只做部分显示
[root@Centos6 /var/named]#ss -ntul

Netid State      Recv-Q Send-Q                      Local Address:Port                        Peer Address:Port 
udp   UNCONN     0      0                            172.18.18.18:53                                     *:*     
udp   UNCONN     0      0                               127.0.0.1:53                                     *:*     
udp   UNCONN     0      0                                     ::1:53                                    :::*     
tcp   LISTEN     0      3                                     ::1:53                                    :::*     
tcp   LISTEN     0      3                            172.18.18.18:53                                     *:*     
tcp   LISTEN     0      3                               127.0.0.1:53                                     *:*     
tcp   LISTEN     0      128                                   ::1:953                                   :::*     
tcp   LISTEN     0      128                             127.0.0.1:953                                    *:*     
tcp   LISTEN     0      100                                   ::1:25                                    :::*     
tcp   LISTEN     0      100                             127.0.0.1:25                                     *:*     

```

> 从上知道DNS会同时启用udp/tcp 的53端口，而其中还有一个953端口其实是用来远程控制DNS的服务named的控制服务
> 这里还要说明：

```
	TCP 53端口是同步用的
	UDP 53端口提供用户查询和同步用的
```

####  /etc/named.conf配置文件

> 让我们了解一下这个重要的配置文件

```shell

[root@Centos6 /var/named]#cat /etc/named.conf

// named.conf

options {
	listen-on port 53 { localhost; };			#默认监听的53端口(localhost表示本机的所有的地址)
	listen-on-v6 port 53 { ::1; };			
	directory 	"/var/named";				#指定默认的数据库文件位置
	dump-file 	"/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
	allow-query     { any; };				#允许查询的DNS主机
	recursion yes;						#是否允许递归查询
	allow-transfer {localhost;};				#设定谁能够从主DNS服务器获得数据信息，一般为了安全考虑，将此处设定为只有从服务器才可以进行更新及下载访问数据，并且将从服务器设定为allow-transfer {none;};
	
	dnssec-enable yes;
	dnssec-validation yes;
...
部分省略
...
#日志有关的定义	
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};
#root(.)的域的定义
zone "." IN {
	type hint;
	file "named.ca";
};

include "/etc/named.rfc1912.zones";  		#默认读取定义目录下的文件，一般我们将域的设定放置在/etc/named.rfc1912.zones这里
include "/etc/named.root.key";

```

> 我们来看一看/etc/named.rfc1912.zones这个文件

```shell

[root@Centos6 /var/named]#cat /etc/named.rfc1912.zones

...
部分省略
...
zone "localhost" IN {
	type master;				#指定类型，[master | slave]
	file "named.localhost";			#指定DNS数据库的文件名称(注意在/var/named下，与设定的文件名称相同)
	allow-update { none; };
};
...
部分省略
...

```

#### 正向解析记录文件

> 正向解析记录文件默认放置在/var/named/下

```shell

[root@Centos6 /var/named]#ll

total 36
drwxr-x---. 7 root  named 4096 Sep 17 13:08 chroot		#切换根的目录
drwxrwx---. 2 named named 4096 Sep 17 13:43 data		
drwxrwx---. 2 named named 4096 Sep 18 11:52 dynamic
-rw-r-----. 1 root  named 3171 Jan 11  2016 named.ca		#根域的信息文件
-rw-r-----. 1 root  named  152 Dec 15  2009 named.empty
-rw-r-----. 1 root  named  152 Jun 21  2007 named.localhost	#正向解析数据参考文件
-rw-r-----. 1 root  named  168 Dec 15  2009 named.loopback	#反向解析数据参考文件
drwxrwx---. 2 named named 4096 Mar 23 04:26 slaves		#奴隶文件(主从复制时放置副DNS数据的地址)
-rw-r-----. 1 root  named  252 Sep 18 11:33 yan.com.zone

```

> 这里我们自己设定了一个本机的DNS正向解析数据库，我们来看一下，yan.com.zone 文件

```shell 

[root@Centos6 /var/named]#cat yan.com.zone 

$TTL 1D									#设定全局的TTL值
@	IN SOA	dns1   adamin.yan.com.  (		#SOA开始认证标识
					2017091901	; serial	#序列号                              
					1D	; refresh		.
					1H	; retry			.
					1W	; expire		.
					3H )	; minimum		参数

#指定域名的解析服务器					
@	NS	dns1								
dns1	A	172.18.18.18					
					
#别名设定
www 	CNAME 	websrv						
websrv  A 	172.18.18.10

#邮件的地址解析
@ 	MX	10 mailsrv							
mailsrv A 	172.18.18.20

```

正向解析数据库的类型常用的有一下几类，我们分别来进行介绍

[domain]| IN| [RR type]| [RR data]
:---:|:---:|:---:|:---:
主机名.| IN| A| IPv4的IP地址  
主机名.| IN| AAAA| IPv6的IP地址 
域名.| IN| NS| 提供域名解析服务器的主机名
域名.| IN| SOA| 管理这个域名的七个重要的参数
域名.| IN| MX 顺序数字| 接受邮件服务器的主机名字
主机别名.| IN| CNAME| 实际代表这个主机别名的主机名字

> - 1、A、AAAA: 查询IP的记录

> A的类型代表查询某个主机名所对应的IP

```shell

[root@centos7 ~]#dig www.yan.com @172.18.26.6
;; ANSWER SECTION:
websrv.yan.com.		86400	IN	A	172.18.18.10

#主机FQDN.              TTL                      主机的IP地址

```

> 这里需要特别注意，如果主机名是全名，结尾必须加上小数点(.)，否则也可以用@来代替域名，或者去继承，如果IP设置是IPv6的地址需要使用AAAA类型的才可以

> - 2、NS(name server): 查询管理域名(zone)的服务器主机名

> 如果需要知道www.yan.com 的这个主机名是由哪台DNS服务器提供的，就需要用NS类型来指定

```shell

[root@centos7 ~]#dig www.yan.com @172.18.26.6

;; AUTHORITY SECTION:
yan.com.		86400	IN	NS	dns1.yan.com.
;; ADDITIONAL SECTION:
dns1.yan.com.		86400	IN	A	172.18.18.18

#在实际生产当中，DNS服务器是很重要的，因此至少有两台以上

```

> - 3、SOA: 域名服务器管理信息
>
> 如果有多台DNS服务器管理同一域名，那么我们最好使用master/slave的方式来进行管理，而要进行管理就要声明被管理的zonefile是如何进行传输的，此时就需要SOA的类型了

```shell

[root@centos6 /var/named]#dig -t SOA www.yan.com @172.18.18.18

;; QUESTION SECTION:
;www.yan.com.			IN	SOA

;; AUTHORITY SECTION:
yan.com.		10800	IN	SOA 	dns1.yan.com. adamin.yan.com. 2017091902 86400 3600 604800 10800


#SOA后的参数有七个

1、master DNS服务器的主机名
	说明哪台DNS服务器为master
	
2、管理员的mail
	因为在数据库文件中@有特别的意义，所以只能通过(.)来代替
	
3、序号
	序号代表这个数据库文件的新旧程度，序号越大表示越新，当slave要判断是否主动下载新的数据库时，会通过序号来判断，若序号比slave的大则会自动下载更新反之则不会，所以要记得当修改数据库文件时，需要将此序号增大才可以，但序号不可以大于2的32次方，必须小于4294976296才可以

4、更新频率
	定义了什么时候slave会去向master要去更新数据，每次还会判断序号的大小
	
5、失败重新尝试的时间
	若因故导致slave无法对master进行连接，过多长时间slave会尝试重新连接
	
6、失效时间
	如果一直连接失败，持续时间达到此设定值，则会不再尝试连接并且尝试删除这份下载的zonefile文件
	
7、缓存时间
	如果这个数据库的每个记录都没有指定TTL的时间，那么则会以这次设定SOA的时间为准
	
```

> - 4、CNAME: 主机别名
>
>当我们不想针对某个主机名设置A的标志，而是想通过另外一台主机名的A来规范这个新的主机名，这时可以使用别名来进行设置

```shell

;; ANSWER SECTION:
www.beijing.yan.com.	86400	IN	CNAME	websrv.beijing.yan.com.
websrv.beijing.yan.com.	86400	IN	A	172.18.18.88

```

> - 5、MX: 邮件服务器主机名
>
> MX是邮件交换的意思，通常设定的整个区域会设置一个MX，表示所有的信息都要发送到所指定的主机名上

```shell

[root@Centos6 /var/named]#dig -t MX  yan.com @127.0.0.1

;; ANSWER SECTION:
yan.com.		86400	IN	MX	10 mailsrv.yan.com.

;; ADDITIONAL SECTION:
mailsrv.yan.com.	86400	IN	A	172.18.26.6

#其中的10表示等级，数字越小优先级越高

```

#### 反向解析数据参考文件

> /etc/named.rfc1912.zones的更改

```shell

#设定172.18.18为反向解析的域
zone "18.18.172.in-addr.arpa" IN {
	type master;
	file "172.18.18.zone";
};

```

> DNS反向数据库文件

```shell

[root@Centos6 /var/named]#cat 172.18.18.zone 

$TTL 1D
@	IN SOA	dns1.yan.com. admin.yan.com. (
					1		; serial
					1D		; refresh
					1H		; retry
					1W		; expire
					3H )	; minimum
	NS	dns1.yan.com.
18	PTR	dns1.yan.com.
10	PTR	websrv.yan.com.
20	PTR	mailsrv.yan.com.

```


**测试**

```shell

[root@centos7 ~]#dig -t ptr 10.18.18.172.in-addr.arpa @172.18.18.18

; <<>> DiG 9.9.4-RedHat-9.9.4-37.el7 <<>> -t ptr 10.18.18.172.in-addr.arpa @172.18.18.18
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 57285
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;10.18.18.172.in-addr.arpa.	IN	PTR

;; ANSWER SECTION:
10.18.18.172.in-addr.arpa. 86400 IN	PTR	websrv.yan.com.

;; AUTHORITY SECTION:
18.18.172.in-addr.arpa.	86400	IN	NS	dns1.yan.com.

;; ADDITIONAL SECTION:
dns1.yan.com.		86400	IN	A	172.18.18.18

;; Query time: 2 msec
;; SERVER: 172.18.18.18#53(172.18.18.18)
;; WHEN: Wed Sep 20 09:11:28 CST 2017
;; MSG SIZE  rcvd: 117

```

#### DNS主从复制

> DNS主从服务/etc/named.rfc1912.zones的设置

```shell

zone "yan.com" IN {
	type slave;
	masters {172.18.18.18;};
	file "slaves/yan.com.slave.zone";
};

```

> 重新加载后，从服务器会自动从主服务器拉去DNS的数据并重新在slave下命名成yan.com.slave.zone
> 在centos6中从服务器的DNS数据是明文的可以看到的，但centos7 中至进行加密的，我们来看一下centos6中的从DNS服务器的数据

```shell

[root@centos6 /var/named/slaves]#cat yan.com.slave.zone 

$ORIGIN .
$TTL 86400	; 1 day
yan.com			IN SOA	dns1.yan.com. adamin.yan.com. (
				2017091901 ; serial
				86400      ; refresh (1 day)
				3600       ; retry (1 hour)
				604800     ; expire (1 week)
				10800      ; minimum (3 hours)
				)
			NS	dns1.yan.com.
			MX	10 mailsrv.yan.com.
$ORIGIN yan.com.
dns1			A	172.18.18.18
mailsrv			A	172.18.18.20
websrv			A	172.18.18.10
www			CNAME	websrv

```

> 与上文我们设定的正向解析数据信息相比，是一样的，下面我们来进行测试

**测试**

>我们在centos7测试DNS服务是否可用，这里centos6 的从服务器的IP地址是172.18.26.6

```shell

[root@centos7 ~]#dig www.yan.com @172.18.26.6

; <<>> DiG 9.9.4-RedHat-9.9.4-37.el7 <<>> www.yan.com @172.18.26.6
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 33650
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 1, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.yan.com.			IN	A

;; ANSWER SECTION:
www.yan.com.		86400	IN	CNAME	websrv.yan.com.
websrv.yan.com.		86400	IN	A	172.18.18.10

;; AUTHORITY SECTION:
yan.com.		86400	IN	NS	dns1.yan.com.

;; ADDITIONAL SECTION:
dns1.yan.com.		86400	IN	A	172.18.18.18

;; Query time: 2 msec
;; SERVER: 172.18.26.6#53(172.18.26.6)
;; WHEN: Wed Sep 20 09:25:20 CST 2017
;; MSG SIZE  rcvd: 112

```

#### DNS子域设置

> 对于子域的设定来说，有两种实现方法：

> 1、对于一个访问量不大的子域来说，我们可以直接在主DNS服务器上直接设定一条NS记录即可，指定子域及其对应的IP地址或者和设定正向解析域一样，新增加一条zone域，在对应的创建出来相应的数据文件即可成功设定好，因上文中已经介绍如何设定这里就不在过多的介绍

> 2、当主域与子域不再同一服务器上时

> 其实这种实现方法也比较简单，就是将子域委派出去，我们来看看如何实现

> 首先，我们想要建立一个子域，需要让主的DNS服务器知道子域是谁，在哪里，我们就需要在主DNS的数据记录中添加设定，假设这里的子域名为beijing.yan.com.


```shell

$TTL 1D
@       IN SOA  dns1   adamin.yan.com.  (
                                        2017091902      ; serial
                                        1D      ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      dns1
beijing NS      dns2					#这里进行了改变，指定beijing.yan.com.的域名指向dns2
dns1    A       172.18.18.18
dns2    A       172.18.18.20				#这里指定了dns2的IP地址，通过这两条就知道了beijing域，委派给dns2来管理了
websrv  A       172.18.18.10
www     CNAME   websrv
@       MX      10 mailsrv
mailsrv A       172.18.18.20

```

> 而后，我们需要在子域的DNS服务器上进行如正向解析的同样的方式设定

```shell

zone "beijing.yan.com" IN {
	type master;
	file "beijing.yan.com.zone";
};

```

> 以及子域的DNS数据文件beijing.yan.com.zone

```shell

[root@centos7 /var/named]#cat beijing.yan.com.zone 

$TTL 1D
@	IN SOA	dns1   adamin  (
					2017091902	; serial
					1D	; refresh
					1H	; retry
					1W	; expire
					3H )	; minimum
	NS	dns1
dns1	A	172.18.18.10
websrv  A 	172.18.18.88
www 	CNAME 	websrv

```

> 我们来看一下测试的情况

**测试**

```shell

[root@centos6 /var/named]#dig www.beijing.yan.com @172.18.18.18

; <<>> DiG 9.8.2rc1-RedHat-9.8.2-0.62.rc1.el6 <<>> www.beijing.yan.com @172.18.18.18
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 59000
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 1, ADDITIONAL: 1

;; QUESTION SECTION:
;www.beijing.yan.com.		IN	A

;; ANSWER SECTION:
www.beijing.yan.com.	86400	IN	CNAME	websrv.beijing.yan.com.
websrv.beijing.yan.com.	86400	IN	A	172.18.18.88

;; AUTHORITY SECTION:
beijing.yan.com.	86400	IN	NS	dns1.beijing.yan.com.

;; ADDITIONAL SECTION:
dns1.beijing.yan.com.	86400	IN	A	172.18.18.10

;; Query time: 6 msec
;; SERVER: 172.18.18.18#53(172.18.18.18)
;; WHEN: Tue Sep 19 13:17:52 2017
;; MSG SIZE  rcvd: 109

```


#### DNS转发

> 全局转发
>
>	全局转发，指定转发的DNS服务器，当由本机的DNS服务器不能解析的全部转发至指定的服务器，只需要在/etc/named.conf文件中option中增加两行

```shell

	forward  only | first		#first:表示转发后没有结果的时候，会自行解析 ，only则是只负责转发，不负责解析
	forwarders {IP;};		#指定转发的IP地址
	
```

> 条件转发
>
>	条件转发，是会根据查询者的搜索的域名来进行转发的，其设定同样需要在/etc/named.rfc1912.zone中设定
>
> 举例说明：

```shell

zone "yaya.com" IN {
	type forward;			#设定此zone的类型为forward
	forward first;			#设定forward的类型
	forwarders { IP; };		#指定转发到的IP地址
};

```

##### 以上只是简单的介绍了DNS，还有许多不足之处，望请谅解！










