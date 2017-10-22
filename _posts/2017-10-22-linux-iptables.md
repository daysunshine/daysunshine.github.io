---
layout: post
title:  iptables介绍及相关配置
date:   2017-10-22 08:00:00
categories: Linux 
tags:  iptables
---

## 介绍
> iptables能够确保信息安全的一种设备，设备上有一些特定的规则，允许或拒绝数据包通过。通过防火墙可以隔离风险区域与安全区域的连接,同时不会妨碍风险区域的访问。当然需要注意的是世界上没有绝对的安全,防火墙也只是启到一定的安全防护。大多数的安全风险还是在内网当中！

#### 防火墙分类
> - 1、软件防火墙：件防火墙需要运行在特定的计算机上,而且需要计算机的操作系统的支持。
> - 2、硬件防火墙：硬件防火墙其实就是一个普通pc机的架构,然后上面跑有专门的操作系统。
> - 3、芯片级防火墙：这种防火墙基于专门的硬件平台,没有操作系统,专有的ASIC芯片使它们比其他类的防火墙速度更快,处理能力极强,性能更高,但是价格昂贵。

### iptables的表和链

> 对于本文我们先进行命令规则的说明，再文章的后半部分进行举例一一说明

#### 1、iptables的链

> 钩子函数(hook function): 用于放在内核TCP/IP模块的特定位置的函数，并对每个通过模块的报文进行检查，查看是否能够通过防火强设定的规则
> 在iptables的设定中共有5个钩子函数：

```

prerouting 	：(路由做出决策之前)刚刚进入本机的网卡还未进入路由表之前的设定规则的地方
input  		：进入本机
output 		：本机出去
forward		：转发的
postrouting ：(路由做出决策之后)通过了本机的模块并进行判定要进行转发，在要经过本机的网卡要出去的时候的设定规则的地方

```

#### 2、iptables的表

> 四个表包括filter、nat、mangle、raw

```

filter  :过滤规则表，根据预定义的规则过滤符合条件的数据包
nat 	:network address translation 网络地址转换规则表
mangle	:修改数据标记位规则表
Raw		:关闭NAT表上启用的连接跟踪机制，加快封包穿越防火墙速度

表的优先级由高到低的顺序为:raw-->mangle-->nat-->filter

```

> 关于链与表的关系可以参见图如下：

![链与表的关系](/assets/pictures/iptables/chain.png)


### iptables命令

> 下面介绍如何使用iptables在不同的钩子函数出设定规则链

> iptables的命令格式

```
iptables [-t TABLE] COMMAND CHAIN [num] 匹配标准 -j 处理办法
```

> 选项：

#### COMMAND ：

```

管理规则
	-A：(append)附加一条规则，添加在链的尾部
	-I CHAIN [num]: (insert)插入一条规则，插入为对应CHAIN上的第num条
	-D CHAIN [num]: (delete) 删除指定链中的第num条规则
	-R CHAIN [num](): 替换指定的规则
管理链：
	-F [CHAIN]：flush，清空指定规则链，如果省略CHAIN，则可以实现删除对应表中的所有链
	-P CHAIN: 设定指定链的默认规则
	-N：自定义一个新的空链
	-X: 删除一个自定义的空链
	-Z：置零指定链中所有规则的计数器
	-E: 重命名自定义的链
查看类：
	-L: 显示指定表中的规则；分链进行显示
		-n: 以数字格式显示主机地址和端口号
		-v: 显示链及规则的详细信息
		-vv: 更加详细的信息
		-x: 显示计数器的精确值
		--line-numbers: 显示规则号码

```

#### CHAIN ：

```

#CHAIN包含：
PREROUTING、INPUT、FORWARD、OUTPUT和POSTROUTING

#链与表的作用关系
filter:
	INPUT、FORWARD和OUTPUT

nat:
	PREROUTING(DNAT)、OUTPUT、POSTROUTING(SNAT)

mangle:
	PREROUTING、INPUT、FORWARD、OUTPUT和POSTROUTING

raw:
	PREROUTING、OUTPUT

	
[num]
对应链的表中的第几条规则

```
#### 处理办法 ：

```

ACCEPT		：接受
DROP		：丢弃，不会提示信息
REJECT		：拒绝，会提示拒绝信息
DNAT		：目标地址转换
SNAT		: 源地址转换
REDIRECT	：端口重定向
MASQUERADE	：地址伪装
LOG			：日志

```

#### 匹配标准 ：

##### 1、通用匹配

```

-s  ：源地址
-d	：目标地址
-p {tcp|udp|icmp} ：报文的协议
-i 	：指定数据报文流入的接口
-o	：指定数据报文流出的接口

```

##### 2、扩展匹配

> (1)隐含扩展

```

#格式
-p tcp
	--sport PORT[-PORT]: 源端口  eg:80-110  支持连续的端口
	--dport PORT[-PORT]: 目标端口
	--tcp-flags mask comp: 只检查mask指定的标志位，是逗号分隔的标志位列表；comp：此列表中出现的标记位必须为1，comp中没出现，而mask中出现的，必须为0；
		--tcp-flags SYN,FIN,ACK,RST  SYN 等同于 --syn
	--syn   对应三次握手的第一次

-p icmp
	--icmp-type 
		0: echo-reply   回应包
		8: echo-request 请求包

-p udp
	--sport  源端口号
	--dport	 目标端口号

```

> (2)显式扩展 (使用额外的匹配机制)

> 需要使用-m指定模块，来进行使用

> - 1、state: 状态扩展

```

结合ip_conntrack追踪会话的状态，根据IP进行追踪，需要iptables加载ip_conntrack模块
	NEW：新发出请求；连接追踪信息库中不存在此连接的相关信息条目，因此，将其识别为第一次发出的请求
	ESTABLISHED：NEW状态之后，连接追踪信息库中为其建立的条目失效之前期间内所进行的通信状态
	RELATED：新发起的但与已有连接相关联的连接，如：ftp协议中的数据连接与命令连接之间的关系
	INVALID：无效的连接，如flag标记不正确
	UNTRACKED：未进行追踪的连接，例如，在ftp的被动模式服务当中，用于实现未知端口的传递数据时能够正常传递
				
首先要装载ip_conntrack_ftp和ip_nat_ftp模块
在/etc/sysconfig/iptables-config文件下的选项IPTABLES_MODULES=将对应的模块添加即可（多个模块空格隔开）
				
#示例
iptables -A INPUT -d 172.16.100.10 -p tcp -m multiport --dports 22,80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables-A OUTPUT -s 172.16.100.10 -p tcp -m multiport --sports 22,80 -m state --state ESTABLISHED -j ACCEPT

```

> - 2、multiport: 离散的多端口匹配扩展

```

#格式
--source-ports       :源地址端口号
--destination-ports	 :目标端口号
--ports				 :端口号

#示例
iptables -A INPUT -d 172.16.100.7 -p tcp -m multiport --destination-ports 21,22,80 -j ACCEPT   #同时指定多个离散的端口号

```

> - 3、iprange扩展

> 指明连续的（但一般不是整个网络）ip地址范围

```

#格式
--src-range from[-to]源IP地址范围
--dst-range from[-to]目标IP地址范围

#示例
iptables -A INPUT -d 172.16.100.10 -p tcp --dport 80 -m iprange --src-range 172.16.100.5-172.16.100.10 -j DROP

```

> - 4、mac扩展

> 指明源MAC地址,适用于：PREROUTING, FORWARD，INPUT链

```

#格式
--mac-source XX:XX:XX:XX:XX:XX

#示例
iptables -A INPUT -s 172.16.0.100 -m mac --mac-source 00:50:56:12:34:56 -j ACCEPT
iptables -A INPUT -s 172.16.0.100 -j REJECT

```

> - 5、string扩展

> 对报文中的应用层数据做字符串模式匹配检测

```

#格式
--string pattern：要检测的字符串模式
--hex-string pattern：要检测字符串模式，16进制格式

#字符串匹配检测算法
--algo {bm|kmp}
	bm：Boyer-Moore
	kmp：Knuth-Pratt-Morris

#示例
iptables -A OUTPUT -s 172.16.100.10 -d 0/0 -p tcp --sport 80 -m string --algo bm --string “google" -j REJECT   #禁止访问特定的"google"字符串

```

> - 6、time扩展

> 根据将报文到达的时间与指定的时间范围进行匹配，centos7默认是UTC时间注意时间问题，centos6则不用指定，默认使用本地时间

```

#格式
--monthdays day[,day...] 每个月的几号
--weekdays day[,day...] 星期几
--datestart YYYY[-MM[-DD[Thh[:mm[:ss]]]]] 开始日期
--datestop YYYY[-MM[-DD[Thh[:mm[:ss]]]]]  结束日期
	--timestart hh:mm[:ss]    
	--timestop hh:mm[:ss]	

#示例
iptables -A INPUT -s 172.16.0.0/16 -d 172.16.100.10 -p tcp --dport 80 -m time --timestart 14:30 --timestop 18:30 --weekdays Sat,Sun --kerneltz -j DROP

```

> - 7、connlimit扩展

> 根据每客户端IP做并发连接数数量匹配,通常分别与默认的拒绝或允许策略配合使用
> 可防止CC(Challenge Collapsar挑战黑洞)攻击

```

#格式
--connlimit-upto   n：连接的数量小于等于n时匹配
--connlimit-above n：连接的数量大于n时匹配

#示例
iptables -A INPUT -d 172.16.100.10 -p tcp --dport22 -m connlimit --connlimit-above 2 -j REJECT

```

> - 8、limit扩展

> 基于收发报文的速率做匹配

```

#格式
--limit rate[/second|/minute|/hour|/day]    明确说明限制的速率
--limit-burst number					    请求的次数达到多少开始执行限制

#示例
iptables -I INPUT -d 172.16.100.10 -p icmp --icmp-type 8 -m limit --limit 3/minute --limit-burst 5 -j ACCEPT
iptables -I INPUT 2 -p icmp -j REJECT

```

> 在iptables的设定中的相关目录文件

```

#已经追踪到的并记录下来的连接信息库
/proc/net/nf_conntrack

#调整连接追踪功能所能够容纳的最大连接数量
/proc/sys/net/nf_conntrack_max

#不同的协议的连接追踪时长
/proc/sys/net/netfilter/

注意：CentOS7 需要加载模块：modprobe  nf_conntrack
	  默认的nf_conntrack_max为 31376 但是需要注意一旦超过默认的设定值会导致各种状态的超时链接会从表中删除；当模板满载时，后续连接会因超时被丢弃

#解决方法
(1) 加大nf_conntrack_max值
	vim /etc/sysctl.conf
	添加内容：
	net.nf_conntrack_max= 393216
	net.netfilter.nf_conntrack_max= 393216
(2) 降低nf_conntracktimeout时间
	vim /etc/sysctl.conf
	添加内容：
	net.netfilter.nf_conntrack_tcp_timeout_established= 300
	net.netfilter.nf_conntrack_tcp_timeout_time_wait= 120
	net.netfilter.nf_conntrack_tcp_timeout_close_wait= 60
	net.netfilter.nf_conntrack_tcp_timeout_fin_wait= 120
	  
```

### 开放被动的ftp服务

> 被动的ftp的服务，因为传输数据的端口未知所以防火墙不允许即行通过，通过在防火墙上设定规则来实现ftp的数据传输

```

(1) 装载ftp连接追踪的专用模块：
	实现被动的服务需要加载连接模块，跟踪模块路径：/lib/modules/kernelversion/kernel/net/netfilter
	vim /etc/sysconfig/iptables-config 配置文件
	IPTABLES_MODULES=" nf_conntrack_ftp "
	#加载模块
	modproble   nf_conntrack_ftp
(2) 放行请求报文：
	命令连接：NEW, ESTABLISHED
	数据连接：RELATED, ESTABLISHED
	iptables –I INPUT -d 172.18.18.18 -p tcp -m state --state RELATED,ESTABLISHED -j  ACCEPT
	iptables -A INPUT -d 172.18.18.18 -p tcp --dport 21 -m state --state NEW -j  ACCEPT
(3) 放行响应报文：
	iptables -I OUTPUT -s 172.18.18.18 -p tcp -m state --state ESTABLISHED -j  ACCEPT

```

### 对处理办法进行详细介绍

> 基本的处理命令：ACCEPT， DROP， REJECT， RETURN因为比较简单这里不再进行过多的介绍

> 扩展的处理命令；LOG， SNAT， DNAT， REDIRECT， MASQUERADE，等等
> 这里只对较为重要的几项进行介绍

#### LOG

> 本身并不允许和拒绝，通常是拒绝和允许的规则前，并将日志记录在/var/log/messages系统日志中

```

#格式
--log-level level 级别： emerg, alert, crit, error,warning, notice, info or debug
--log-prefix prefix ：日志前缀，用于区别不同的日志，最多29个字符

#示例
iptables -I INPUT -s 10.0.1.0/24 -p tcp -m multiport --dports 80,21,22,23 -m state --state NEW -j LOG --log-prefix "new connections: "

```

> 那么对于iptables的规则该有怎样的设置呢

> 任何不允许的访问，应该在请求到达时给予拒绝
> 规则在链接上的次序即为其检查时的生效次序
> 基于上述，规则优化:
```
	1、安全放行所有入站和出站的状态为ESTABLISHED状态连接
	2、谨慎放行入站的新请求
	3、有特殊目的限制访问功能，要在放行规则之前加以拒绝
	4、同类规则（访问同一应用），匹配范围小的放在前面，用于特殊处理
	5、不同类的规则（访问不同应用），匹配范围大的放在前面
	6、应该将那些可由一条规则能够描述的多个规则合并为一条
	7、设置默认策略，建议白名单（只放行特定连接）
```

> 保存规则
> 对于需要对规则进行保存该如何实现

```

#保存规则至指定的文件
CentOS 6当中:
service iptables save  #默认将规则覆盖保存至/etc/sysconfig/iptables文件中

CentOS 7当中: 可用下面方法保存规则
iptables -S > /PATH/TO/SOME_RULES_FILE
iptables-save > /PATH/TO/SOME_RULES_FILE

#导入规则
CentOS 6：
service iptables restart  #会自动从/etc/sysconfig/iptables 重新载入规则

CentOS 7：
iptables-restore < /PATH/FROM/SOME_RULES_FILE
	-n, --noflush：不清除原有规则
	-t, --test：仅分析生成规则集，但不提交

```

### NAT

> NAT(network address translation),网络地址转换
> PREROUTING，INPUT，OUTPUT，POSTROUTING
>>	请求报文：修改源/目标IP，由定义如何修改
>>	响应报文：修改源/目标IP，根据跟踪机制自动实现

> SNAT：source NAT POSTROUTING, INPUT
>> 让本地网络中的主机通过某一特定地址访问外部网络，实现地址伪装
>> 请求报文：修改源IP

> DNAT：destination NAT PREROUTING, OUTPUT
>> 把本地网络中的主机上的某服务开放给外部网络访问(发布服务和端口映射)，但隐藏真实IP
>> 请求报文：修改目标IP

> PNAT: port nat，端口和IP都进行修改

#### SNAT 源地址转换

> 修改IP报文中的源IP地址，让本地网络中的主机能够使用同一地址与外部主机进行通信，实现地址伪装。请求有内网发出，修改原IP是由管理员指定。相应报文则是修改目标IP，是由NAT自动根据会话表中的追踪机制进行相应的修改，对应的IP还分为两种。

```

#SNAT：固定IP
	--to-source [ipaddr[-ipaddr]][:port[-port]]
	--random
	
#示例：
iptables-t nat -A POSTROUTING -s 10.0.1.0/24 ! –d 10.0.1.0/24 -j SNAT --to-source 172.18.100.6-172.18.100.9

#MASQUERADE：动态IP,如拨号网络,目标地址是变换的(效率不高)
	--to-ports port[-port]
	--random

#示例：
iptables -t nat -A POSTROUTING -s 10.0.1.0/24 ! –d 10.0.1.0/24 -j MASQUERADE  #有多个内网访问接口，可以在-s后指定多个网段即可

```

> 注意:当进行源地址转换时，相应的iptables规则应该作用到POSTROUTING上，在出口处进行设定

> SNAT的图示参见如下：

![snat](/assets/pictures/iptables/snat.png)

#### DNAT 目标地址转换

> 同样是修改报文当中的IP地址，但是与SNAT不同的是请求来自外网主机，通过NAT修改目标地址，来达到访问内网的主机的目的

```

#DNAT格式
	--to-destination [ipaddr[-ipaddr]][:port[-port]]

#示例：将外网访问本地的地址转换成能够访问本地主机的NAT上
iptables -t nat -A PREROUTING  -d 172.18.100.6 -p tcp--dport 22 -j DNAT --to-destination 10.0.1.22
iptables -t nat -A PREROUTING  -d 172.18.100.6 -p tcp--dport 80 -j DNAT --to-destination 10.0.1.22:8080


```

> 注意:当进行目标地址转换时，相应的iptables规则应该作用到PREROUTING上，在进入内网处进行设定

#### 端口转发

```

#REDIRECT：应用于NAT表的PREROUTING OUTPUT 自定义链上，通过改变目标IP和端口，将接受的包转发至不同地址
	--to-ports port[-port]

#示例：将访问本机的8080 端口转到本机的80端口
iptables-t nat -A PREROUTING -d 172.16.100.10 -p tcp--dport 80 -j REDIECT --to-ports 8080

```

> ##### 示例：

```

1、不允许ping
[root@Centos6 ~]# iptables -A INPUT -p icmp -j DROP

2、删除3号规则
[root@Centos6 ~]# iptables -L --line-number
Chain INPUT (policy DROP)
num  target     prot opt source               destination        
1    ACCEPT     tcp  --  192.168.8.71         anywhere            tcp dpt:ssh
2    DROP       icmp --  anywhere             anywhere            
3    ACCEPT     tcp  --  192.168.8.71         anywhere            tcp dpt:ssh

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination        

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination        
[root@Centos6 ~]# iptables -D INPUT 3
[root@Centos6 ~]# iptables -L --line-number
Chain INPUT (policy DROP)
num  target     prot opt source               destination        
1    ACCEPT     tcp  --  192.168.8.71         anywhere            tcp dpt:ssh
2    DROP       icmp --  anywhere             anywhere            

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination        

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination      

3、防止广播包进入局域网：
[root@Centos6 ~]# iptables  -A INPUT -s 255.255.255.255 -i eth0 -j DROP
[root@Centos6 ~]# iptables -A INPUT -s 224.0.0.0/224.0.0.0 -i eth0 -j DROP
[root@Centos6 ~]# iptables -A INPUT -d 0.0.0.0 -i eth0 -j DROP

4、修改第3条规则：
[root@Centos6 ~]# iptables -R INPUT 3 -s 192.168.8.72 -j ACCEPT

5、在第3条规则上面插入一条新的规则：
[root@Centos6 ~]# iptables -I INPUT 3 -s 192.168.8.73 -j ACCEPT

6、把ping的信息定向到日志：
[root@Centos6 ~]# iptables -A INPUT -p icmp -s 172.16.13.13 -j LOG
[root@Centos6 ~]#  tail -0f /var/log/messages

7、连续端口的写法：25:110
iptables -A INPUT -p tcp --dport 25:110 -j ACCEPT

8、允许loopback!(不然会导致DNS无法正常关闭等问题)
[root@Centos6 ~]# iptables -A INPUT -i lo -p all -j ACCEPT
[root@Centos6 ~]# iptables -A OUTPUT -o lo -p all -j ACCEPT（output链为DROP的情况下）

9、减少不安去的端口连接
有些些特洛伊木马会扫描端口31337到31340(即黑客语言中的 elite 端口)上的服务。既然合法服务都不使用这些非标准端口来通信,所以拒绝这些端口的连接是有必要的
[root@Centos6 ~]#  iptables -A OUTPUT -p tcp --sport 31337:31338 -j DROP
[root@Centos6 ~]#  iptables -A OUTPUT -p tcp --sport 31339:31340 -j DROP

# FORWARD链上(要开启转发功能，有两块网卡)
注：在做NAT时,FORWARD默认规则是DROP时,必须要做的：
[root@Centos6 ~]# iptables -A FORWARD -i eth0 -o eth1 -m state --state RELATED,ESTABLISHED -j ACCEPT
[root@Centos6 ~]# iptables -A FORWARD -i eth1 -o eh0 -j ACCEP

(1)丢弃坏的TCP包
[root@Centos6 ~]# iptables -A FORWARD -p TCP ! --syn -m state --state NEW -j DROP

(2)处理IP碎片数量,防止攻击,允许每秒100个
[root@Centos6 ~]# iptables -A FORWARD -f -m limit --limit 100/s --limit-burst 100 -j ACCEPT

(3)设置ICMP包过滤,允许每秒1个包,限制触发条件是10个包.
[root@Centos6 ~]# iptables -A FORWARD -p icmp -m limit --limit 1/s --limit-burst 10 -j ACCEPT
[root@tp rc.d]# iptables -t nat -L
如果你想清除,命令是
[root@Centos6 ~]# iptables -F -t nat
[root@Centos6 ~]# iptables -X -t nat
[root@Centos6 ~]# iptables -Z -t nat

(4)添加规则
[root@Centos6 ~]# iptables -t nat -A PREROUTING -i eth0 -s 10.0.0.0/8 -j DROP
[root@Centos6 ~]#  iptables -t nat -A PREROUTING -i eth0 -s 172.16.0.0/12 -j DROP
[root@Centos6 ~]# iptables -t nat -A PREROUTING -i eth0 -s 192.168.0.0/16 -j DROP

(5)禁用FTP(21)端口
[root@Centos6 ~]# iptables -t nat -A PREROUTING -p tcp --dport 21 -j DROP

(6)拒绝非法连接
[root@Centos6 ~]# iptables -A INPUT     -m state --state INVALID -j DROP
[root@Centos6 ~]# iptables -A OUTPUT    -m state --state INVALID -j DROP
[root@Centos6 ~]# iptables -A FORWARD -m state --state INVALID -j DROP

(7)接收已经建立的连接
[root@Centos6 ~]# iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
[root@Centos6 ~]# iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

```



























