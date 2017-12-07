---
layout: post
title:  SSH相关应用
date:   2017-09-13 08:00:00
categories: Linux 
tags:   pssh  密钥认证  端口转发 秘钥分发脚本
---


### 目录

> - ssh命令及认证
> - scp远程传输
> - rsync高效远程传输文件
> - pssh批量推送
> - 端口转发


### ssh 

> ssh有两种认证方式，密码认证及密钥认证，接下来本文会进行分别解释

### 基于密码认证

> 在说明ssh命令之前我们先来看一下ssh的配置文件,客户端的配置文件/etc/ssh/ssh_config，服务器端的配置文件/etc/ssh/sshd_config

存放在/etc/ssh/下

```shell

[root@Centos6 /etc/ssh]#ls

moduli       ssh_host_dsa_key      ssh_host_key.pub
ssh_config   ssh_host_dsa_key.pub  ssh_host_rsa_key
sshd_config  ssh_host_key          ssh_host_rsa_key.pub

dsa_key、dsa_key.pub 与 rsa_key、rsa_key.pub  是适用于SSHv2版本的公钥及私钥
host_key、host_key.pub是适用于SSHv1版本的公钥及私钥

```
我们看一下客户端的ssh文件

```shell

cat /etc/ssh/ssh_config

# Host *
#   ForwardAgent no
#   ForwardX11 no						是否支持X11
#   RhostsRSAAuthentication no
...
部分省略
...
#   StrictHostKeyChecking ask			首次连接时是否询问
#   IdentityFile ~/.ssh/identity		
#   IdentityFile ~/.ssh/id_rsa			RSA算法私钥存放的位置
#   IdentityFile ~/.ssh/id_dsa			DSA算法私钥存放的位置
#   Port 22								设定默认端口号
#   Protocol 2,1						sshv2,1版本都支持
...
部分省略
...
#   PermitLocalCommand no
#   VisualHostKey no

```

> 以上许多选项都可以自行的在配置文件中进行设置

> 再看看服务器端的配置文件

```shell

[root@Centos6 /etc/ssh]#cat /etc/ssh/sshd_config

...
部分省略
...
#Port 22							设定默认的端口号
#AddressFamily any					在哪一类IP提供服务(IPV4、IPV6),any表示都可以
#ListenAddress 0.0.0.0				指定地址，ssh只能在指定的地址上提供服务，0.0.0.0，表示不指定地址，所有地址都可以
#ListenAddress ::					
...
部分省略
...
# activation of protocol 1
Protocol 2							允许在SSHV2版本使用
...
部分省略
...
# Lifetime and size of ephemeral version 1 server key
#KeyRegenerationInterval 1h			当客户端与服务端建立连接后，过多长时间随机生成秘钥，来在此互相确认
#ServerKeyBits 1024					服务器端秘钥长度多少位
...
部分省略
...
#LoginGraceTime 2m					登录等待时间
#PermitRootLogin yes				是否允许管理员直接登录
#StrictModes yes					是否使用严格限定模式
#MaxAuthTries 6						最大尝试错误登录次数
#MaxSessions 10						最多会话的次数
...
以下省略
...

```

> 关于服务器端的ssh设定还有很多，这里只部分介绍，不再赘述

#### 上面介绍ssh的配置，下面介绍ssh相关的命令

> 基于密码认证

**原理**

> - 1、客户端发起ssh请求，服务器会把自己的公钥发送给用户
> - 2、用户会根据服务器发送的公钥对密码进行加密
> - 3、加密后的信息回传给服务器，服务器用私钥解密，如密码匹配，则用户登录成功

> 命令格式 ： ssh [-l user] host [ ' COMMAND ' ]

**选项**

```shell

-l: 指定以那个用户进行ssh连接远程主机
-p port: 远程服务器监听的端口
-b: 指定连接的源IP
-v: 调试模式
-C: 压缩方式
-X: 支持x11转发
-Y: 支持信任x11转发
-t: 强制伪tty分配（间接连接将server1作为跳板机去连接server2）

```

### 基于秘钥的认证

**原理及过程**

> - 1、首先需要在客户端生成一对密钥
> - 2、将客户端的公钥ssh-copy-id拷贝到服务端对应的用户的家目录.ssh/authorized_keys下
> - 3、当客户端再次发送一个连接请求后，服务端得到客户端的请求后，会访问authorized_keys文件，查找是否有该用户，有就会随机生成一个字符串，并通过公钥进行加密，发送给客户端
> - 6、得到服务端发来的消息后，客户端会使用自己的私钥进行解密，然后将解密的字符串发送给服务端
> - 7、服务端收到客户端发来解密后的字符串后，会跟之前的随机字符串进行对比，如果一致，就允许免密码登录

> #### 实现过程 

> - (1)、在客户端生成密钥对

> 命令格式 ： ssh-keygen  -t   rsa[-P '' ]  [-f “~/.ssh/id_rsa"]

```shell

-f 	秘文件保存位置
-t 	加密算法rsa或dsa
-P 	设定生成的私钥口令

```

> - (2)、公钥文件传输至服务器对应家目录

> 命令格式： ssh-copy-id  [-i[identity_file]] [user@]hostid

```shell

ssh-copy-id  -i ~/.ssh/id_rsa.pub  172.18.18.18:~/.ssh

```

> 创建多个公钥文件时只需要累加到.ssh/authorized_keys目录下即可

#### 重设私钥口令

> 目的就是为私钥进行加密确保安全 

```shell	

ssh-keygen -p 
	
```
#### 添加代理来管理ssh连接

> 命令格式： ssh-agent bash (添加代理) ; ssh-add (添加代理的口令)


### scp命令

> scp命令类似于cp命令，用于两个主机间的文件传输

> 命令格式： scp [options]  SRC...    DEST/

> 两种方式：

```shell

scp[options]  [user@]host:/sourcefile/destpath  	# 将目标文件复制到本地目录下
scp[options]  /sourcefile  [user@]host:/destpath	# 本地文件传输至目标地址的目录下

```
**选项**

```shell

-C: 压缩数据流
-r: 递归复制
-p: 保持原文件的属性信息
-q: 静默模式
-P PORT: 指明remote host的监听的端口

```

> scp的命令在日常当中的使用还是很频繁的

### sftp命令

> sftp其实就是sslftp更加安全的ftp传输工具，用法与ftp相似

> 用法和传统的ftp工具相似
> 利用ssh服务实现安全的文件上传和下载
> 使用ls cd mkdir rmdir pwd get put等指令，可用？或help获取帮助信息

> sftp   [user@]host

> get  从目标主机或得获得文件    
> mget 一次获取多个文件

#### 关于ssh的总结

> - 1、密码经常换，且足够复杂
> - 2、使用非默认端口
> - 3、限制登录客户地址
> - 4、禁止管理员直接登录
> - 5、仅允许有限用户登录
> - 6、使用基于秘钥的认证
> - 7、禁止使用SSHv1版本
> - 8、限制ssh的访问频度和并发在线数
> - 9、做好日志，经常分析


### rsync命令

> rsync (只复制特定的文件)(增量备份)是它的特性，基于ssh和rsh服务实现高效率的远程系统之间复制文件，并且使用安全的shell连接做为传输方式
> 简单常用的命令

```shell

rsync  –av  /etc  server1:/tmp复制目录和目录下文件
rsync  –av  /etc/  server1:/tmp只复制目录下文件

```
> 比scp更快，只复制不同的文件,增量备份

**选项：**

```shell

-n 模拟复制过程
-v 显示详细过程
-r 递归复制目录树
-p 保留权限
-t 保留时间戳
-g 保留组信息
-o 保留所有者信息
-l 将软链接文件本身进行复制（默认）
-L 将软链接文件指向的文件复制
-a 存档，相当于–rlptgoD，但不保留ACL（-A）和SELinux属性（-X）

```

### pssh命令

> pssh是一个python编写可以在多台服务器上执行命令的工具，用于批量操作，也可实现文件复制,非常的便捷

> #### 来看一下pssh的选项参数

**选项**

```shell

--version：查看版本
-h：主机文件列表，内容格式”[user@]host[:port]”   	# 常用
-H：主机字符串，内容格式”[user@]host[:port]”		# 常用
-l：登录使用的用户名
-p：并发的线程数[可选]								#常用
-o：输出的文件目录[可选]
-e：错误输入文件[可选]
-t：TIMEOUT 超时时间设置，0无限制[可选]
-O：SSH的选项
-v：详细模式
-A：手动输入密码模式
-x：额外的命令行参数使用空白符号，引号，反斜线处理
-X：额外的命令行参数，单个参数模式，同-x
-i：每个服务器内部处理信息输出						# 最常用
-P：打印出服务器返回信息

```

#### pssh的示例

```shell

#执行命令参数 -H -i
[root@Centos6 ~/.ssh]#pssh -H 172.18.18.10 -i hostname

[1] 01:35:21 [SUCCESS] 172.18.18.10
centos7.3ymd

#命令参数-h从文件当中读取
#先编辑文件，用存放IP地址
[root@Centos6 ~/bin]#cat test.txt 

172.18.18.10
172.18.26.6

#读取文件并执行命令
[root@Centos6 ~/bin]#pssh -h test.txt -i hostname

[1] 01:55:36 [SUCCESS] 172.18.18.10
centos7.3ymd
[2] 01:55:36 [SUCCESS] 172.18.26.6
centos6.8

#实际当中的示例，用来多台同时操作，禁用selinux
[root@Centos6 ~/bin]#pssh -h test.txt -i sed -r -i  's/^SELINUX=/SELINUX=disabled/' /etc/selinux/config

[1] 02:04:48 [SUCCESS] 172.18.26.6
[2] 02:04:48 [SUCCESS] 172.18.18.10

```

### 端口转发

> 首先进行说明一下(下同)：

```shell

A机器：172.18.18.18
B机器：172.18.26.6
C机器：172.18.18.10

```

> 通过端口转发来间接实现A与C机器的网络连接，接下来对不同环境下进行解释说明

#### 本地端口转发：

> 本地转发的命令格式：

> - ssh -L localport:remotehost:remotehostport  sshserver

> 命令变量说明

```shell

localport: 本地指定端口
remotehost: 目标地址
remotehostport: 目标地址端口
sshserver: 中间的ssh服务器

```

**选项**

```shell

-L 本地转发
-f 后台启用
-N 不打开远程shell，处于等待状态
-g 启用网关功能

```

> 前提：因各种原因，A不能访问C机器，但是B能够连接A、C机器，下面解释如何实现
> 这里要进行说明一下: C作为本访问的机器，要确保A访问的中间服务器B对应服务端口要打开

**示例**

```shell

#本机进行隧道连接

[root@Centos6 ~]#ssh -L 2332:172.18.18.10:23 -Nf 172.18.26.6    #在隧道进行连接成功后，服务器B会进行对指定的端口进行监控

#通过访问本机指定的端口

[root@Centos6 ~]#telnet 127.0.0.1 2332    #访问指定的隧道入口时，服务器B会自动将A的连接请求转发至C机器

Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.

Kernel 3.10.0-514.el7.x86_64 on an x86_64
centos7 login: yan
Password: 
[yan@centos7 ~]$ 

```

#### 远程端口转发

> 远程转发的命令格式：

> - ssh  -R localport:remotehost:remotehostport sshserver

**选项**

```shell

-L 本地转发
-f 后台启用
-N 不打开远程shell，处于等待状态
-g 启用网关功能

```

> A处于外网、而B、C处于同一内网，A因防火墙不能对BC的内网进行访问，但B能够进行与A、C的连接

**示例**

```shell

#B机器主动与A机器进行ssh隧道的连接，并在B的机器上开启指定的端口
[root@centos6.8 ~]# ssh -R 2332:172.18.18.10:23 -Nf 172.18.18.18   

#A机器进行服务的连接
[root@Centos6.9 ~]#telnet 127.0.0.1 2332   #A机器通过本地地址及指定的端口进行对C的访问

Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.

Kernel 3.10.0-514.el7.x86_64 on an x86_64
centos7 login: yan
Password: 
Last login: Tue Sep 12 22:14:37 from ::ffff:172.18.18.18
[yan@centos7 ~]$ 

```

> 远程转发特别适用于，例如：外部某个开发人员，想要连接进内网的机器，但由于防火墙的限制，却不能进入内网的场景


#### 动态端口转发

> 动态端口转发，是A机器由于防火墙的策略限制不能访问外网的服务器C，但B可以访问C，此时就可以动态端口转发，将B机器设定为A的代理，从而进行对C的访问

> 动态端口转发命令格式

> - ssh  -D 1080 root@sshserver

**选项**

```shell

-D : 动态转发
1080: 可自己制定的端口号
root@sshserver: 代理机器的IP地址

```

```shell

#进行动态端口连接
[root@Centos6.9 ~]#ssh  -D 1080 172.18.26.6
Last login: Tue Sep 12 09:53:14 2017 from 172.18.18.17
[root@centos6.8 ~]# 

```

> 开启代理功能：

> - curl --sockes5 127.0.0.1 1080   http://172.18.18.10


#### 脚本实现秘钥的分发
> 以下列出实现脚本分发的脚本
> 可根据实际的情况进行IP的更改即可

```
#!/bin/bash

#make key
\rm -f /root/.ssh/id_dsa
ssh-keygen -t rsa -f /root/.ssh/id_dsa -P "" -q

#send pub key 
for ip in 10 15 30;do
        echo ====send key to host 172.18.18.$ip====
                sshpass -p123456 ssh-copy-id -i /root/.ssh/id_dsa.pub "-o StrictHostKeyChecking=no ro
ot@172.18.18.$ip"
        echo ===============send end==============
        echo ""
done

```

> #### **&emsp;&emsp;以上就是ssh相关的全部内容，由于本片文章是基于部分操作写成的，难免有不足及错误的地方，敬请谅解！**

