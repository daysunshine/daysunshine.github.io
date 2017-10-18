---
layout: post
title:  文件共享服务Samba
date:   2017-10-18 08:00:00
categories: Linux 
tags:  Samba  cifs挂载  多用户挂载
---

## 介绍

> SMB：Server Message Block服务器消息块，IBM发布，最早是DOS网络文件共享协议
> SAMBA的功能：
> - 共享文件和打印，实现在线编辑
> - 实现登录SAMBA用户的身份认证
> - 可以进行NetBIOS名称解析
> - 外围设备共享

### Samba介绍

> **相关包：**
>> Samba 提供smb服务
>> Samba-client 客户端软件
>> samba-common 通用软件
>> cifs-utilssmb客户端工具
>> samba-winbind和AD相关
> **相关服务进程：**
>> smbd提供smb(cifs)服务TCP:139,445
>> nmbdNetBIOS名称解析UDP:137,138
> **主配置文件：**/etc/samba/smb.conf
>> 帮助参看：man smb.conf
> **语法检查：**testparm[-v] [/etc/samba/smb.conf]
> **客户端工具：**smbclient,mount.cifs


### Samba服务器配置

> Samba服务的配置文件在/etc/samba/smb.conf里
> 来看一下文件的内容,包了几个部分

```

#全局设置：
[global] 	#服务器通用或全局设置的部分

#特定共享设置：
[homes] 	#用户的家目录共享
[printers] 	#定义打印机资源和服务
[sharename] #自定义的共享目录配置

#在配置文件中的宏定义
宏定义：
	%m 客户端主机的NetBIOS名
	%M 客户端主机的FQDN
	%H 当前用户家目录路径
	%U 当前用户用户名
	%g 当前用户所属组
	%h samba服务器的主机名
	%L samba服务器的NetBIOS名
	%I 客户端主机的IP
	%T 当前日期和时间
	%S 可登录的用户名

#全局的服务配置
workgroup 		指定工作组名称
server string 	主机注释信息
netbios name 	指定NetBIOS名
interfaces 		指定服务侦听接口和IP
hosts allow 	可用“,” ，空格，或tab分隔，默认允许所有主机访问，也可在每个共享独立配置，如在[global]设置，将应用并覆盖所有共享设置
hosts deny 		拒绝指定主机访问
config file=/etc/samba/conf.d/%U 	用户独立的配置文件
Log file=/var/log/samba/log.%m		不同客户机采用不同日志
max log size=50 					日志文件达到50K，将轮循rotate,单位KB
Security							三种认证方式：
	share：	匿名(CentOS7不再支持)
	user：	samba用户（采有linux用户，samba的独立口令）
	domain：使用DC（DOMAINCONTROLLER)认证
passdb backend = tdbsam				密码数据库格式
	
```

### 管理samba用户

```

#添加samba用户
smbpasswd  -a <user>    #该用户必须是Linux内存在的系统用户，同时为用户添加账户和密码
pdbedit -a -u <user>

#修改用户密码
smbpasswd  <user>

#删除用户和密码：
smbpasswd –x <user>
pdbedit –x  –u <user>

#查看samba用户列表：
/var/lib/samba/private/passdb.tdb
pdbedit   –L –v    		#查看本机的Samba账户

#查看samba服务器状态
smbstatus

```

### 设定共享目录

```

#每个共享目录应该有独立的[ ]部分,选项内容为
[共享名称] 		远程网络看到的共享名称
comment 		注释信息
path 			所共享的目录路径
public			(能否匿名访问)能否被guest访问的共享，默认no，和guest ok 类似
browsable		是否允许所有用户浏览此共享,默认为yes,no为隐藏共享文件
writable=yes 	可以被所有用户读写，默认为no
read only=no 	和writable=yes等价，如与以上设置冲突，放在后面的设置生效，默认只读
write list 		三种形式：用户，@组名，+组名,用，分隔
	如writable=no，列表中用户或组可读写，不在列表中用户只读
valid users 特定用户才能访问该共享，如为空，将允许所有用户，用户名之间用空格分隔

```


### 实现Samba文件共享

> 在进行操作前请确保已经安装了Samba软件包，如未安装执行如下命令```yum install samba``` 

```

#创建Samba共享的用户和组,并将wang用户添加至admin组中
useradd -s /sbin/nologin wang
useradd -s /sbin/nologin ymd
groupadd admin
groupmems -g admin -a wang

#设定samba用户和密码
smbpasswd -a wang
smbpasswd -a ymd

#创建共享目录
mkdir  /app/share
chgrp admin /app/share

#设定samba配置文件sma.conf
#编辑/etc/samba/smb.conf
[share]
path = /app/share   	#共享文件夹的路径
write list = @admin		#可写的列表,未在列表当中的为只读

#重新启动samba服务
service smb restart

#客户端访问测试
yum -y install cifs-utils  #客户端工具包
#登录访问
[root@centos7 ~]#smbclient  //172.18.18.18/share -U wang%centos
Domain=[MYGROUP] OS=[Unix] Server=[Samba 3.6.23-41.el6]
smb: \> ls
  .                                   D        0  Mon Oct 16 07:21:08 2017
  ..                                  D        0  Mon Oct 16 07:20:38 2017
  samba                               N        0  Mon Oct 16 07:21:08 2017

		40185208 blocks of size 1024. 37987148 blocks available

```

### 挂载cifs文件系统

> 同样也需要安装户端工具包cifs-utils，这里不再赘述

```

#进行手动挂载
[root@centos7 ~]#mount -o username=wang,password=centos //172.18.18.18/share /mnt/smb
#进行查看
[root@centos7 ~]#cd /mnt/smb/
[root@centos7 /mnt/smb]#ls
samba  yan.pubkey

#开机自动挂载
编辑/etc/fstab文件，可以用文件代替用户名和密码的输入
//172.18.18.18/share    /mnt/smb     cifs    credentials=/etc/smb.txt      0 0

#编辑用户密码文件
vim /etc/smb.txt
	username=wang
	password=centos

#为了防止密码泄露更改权限
chmod  600 /etc/smb.txt

```

### 多用户挂载

> SAMBA共享默认只支持同时用一个用户挂载SMB共享
> CentOS7中可启用多用户挂载功能客户端挂载samba共享目录后，在客户端登录的不同用户访问同一个samba的挂载点，可获得不同权限

```

#Samba服务器配置
vim /etc/samba/smb.conf
[smbshare]
path=/app/share
write list= @admins

#Samba服务器创建账户及设定共享目录权限，与上面创建的用户一致，这里不再进行重新创建和设置
#同时需要在客户端也需要创建相同的账号

#samba客户端启用多用户挂载

vim  /etc/smb.txt
	username=ymd
	password=centos

chmod 600 /etc/smb.txt

#以多用户的方式挂载
vim /etc/fstab
//172.18.18.18/share    /mnt/smb     cifs    multiuser,credentials=/etc/smb.txt      0 0

mount -a 

#在客户端上进行测试
#默认是以挂载时的用户进行访问的
[root@centos7 ~]#cd /mnt/smb/
[root@centos7 /mnt/smb]#touch aaa
touch: cannot touch ‘aaa’: Permission denied    #与设定的相符，ymd账号只有读 的权限

#切换成wang用户进行测试
su - wang 
cifscreds add  172.18.18.18
[wang@centos7 ~]$ cd /mnt/smb/
[wang@centos7 smb]$ touch aaa
[wang@centos7 smb]$ ls
aaa  samba  yan.pubkey                          #能够创建文件，有读写权限

```
> 多用户实现了特定的用户的特定权限

> 这里还要进行说明，也可以再全局设定中指定子配置文件来对不同的用户进行设定

```

#在global内添加如下内容
[global]
config file  = /etc/samba/conf.d/%U

#在自己指定的配置目录写创建与用户同名的子配置文件
#那么在进行指定的用户连接时就可以，读取相应的配置文件

```

> #### 至此Samba文件共享的服务完毕，如有错误还望批评指正，敬请谅解！



