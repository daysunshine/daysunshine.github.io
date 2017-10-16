---
layout: post
title:  基于LAMP实现NFS文件共享及NDS负载均衡
date:   2017-10-16 08:00:00
categories: Linux 
tags:  NFS文件共享  DNS负载均衡  
---
## 介绍

> 本文在于实现基于lamp下的NFS网络文件系统的设置，并实现DNS的轮巡实现负载均衡

> 下面列举各个主机的IP及设定

IP|设定
:---:| :---:
172.18.18.40| 客户端及DNS服务器
172.18.18.10| WEB服务器
172.18.18.30| WEB服务器
172.18.18.12| NFS服务器
172.18.18.18| Mysql服务器


### 1、mysql的设定

> 在之前的文章进行过详细的介绍，mysql的设置，这里只介绍如何实现

```

#创建数据库，并对web服务器进行授权,设定账户和密码
mysql> create database wpdb;
mysql> grant all on wpdb.* to test@'172.18.18.%' identified by 'centos';

```

### 2、NFS的设定

> 本文的NFS将WordPress放置于NFS服务器上，进行共享

```

#编辑配置文件/etc/exports，添加本机共享的文件
/app/wordpress      172.18.18.10(ro)  172.18.18.30(rw)   #本文将/app的文件进行对特定用户的共享并设定选项

选项：

• ro,rw 				只读和读写
• async 				异步，数据变化后不立即写磁盘，性能高
• sync（1.0.0后为默认）	同步，数据在请求时立即写入共享
• no_all_squash 		（默认）保留共享文件的UID和GID
• all_squash 			所有远程用户(包括root)都变成nfsnobody
• root_squash 			（默认）远程root映射为nfsnobody,UID为65534，早期版本是4294967294 (nfsnobody)
• no_root_squash 		远程root映射成root用户
• anonuid和anongid 		指明匿名用户映射为特定用户UID和组GID，而非nfsnobody,可配合all_squash使用

#重读配置文件，并共享目录
exportfs -r 

其它选项：
	–v  查看本机所有NFS共享
	–r  重读配置文件，并共享目录
	–a  输出本机所有共
	–au 停止本机所有共享
	
```

**注意**
> 在实际的设定当中，所选参数通常会设定对应的账户名称固定的ID号，例如，在web服务器当中用到的Apache账户，在进行共享文件更新时，就会自动设定文件的访问用户为Apache，这样使用者通过Apache账户就能够访问相应的文件

#### 3、设定自动挂载

> 对本文NFS来说设定自动挂载需要在两台web服务器上进行设定，步骤相同
> 如要进行手动挂载执行mount命令进行挂载，也可以写到/etc/fstab内，使其开机挂载

```

#查看NFS服务器的共享目录
[root@Centos6 ~]#showmount -e 172.18.18.12
Export list for 172.18.18.12:
/app 172.18.18.30,172.18.18.10   #确认已经将目录进行共享设置

#编辑/etc/auto.master文件，进行绝对路径配置
/-   /etc/nfsauto   #将配置完全交于/etc/nfsauto进行管理

#编辑/etc/nfsauto文件，设定自动挂载的目录
/var/www/html/blog   172.18.18.12:/app/wordpress   #这里需要注意所挂载的目录不需要提前创建，在进行自动连接是会自动生成

#重新启动autofs程序
service autofs restart

```

### 4、web服务器的设定

> 设定web服务器的时候,与之前的实验大体相同，只需要设定NFS挂载选项即可，以上已经完成

```

#设定连接数据库的PHP测试代码，两台web服务器的设定是相同的
vim /var/www/html/index.php
添加如下内容
<?php
$mysqli=new mysqli("172.18.18.18","test","centos");
if(mysqli_connect_errno()){
echo "连接数据库失败!";
$mysqli=null;
exit;
}
echo "连接数据库成功!  I am 172.18.18.30";
$mysqli->close();
phpinfo();
?>

```

### 5、客户端及DNS服务器 

> 本实验将客户端和DNS服务器用同一台机器实现

```

#对named.conf进行修改，使其能够对它的IP进行开放使用
#本文只是将其进行注释，来达到目的，详细的可参照本网站的博客《DNS》
options {
//	listen-on port 53 { 127.0.0.1; };
	listen-on-v6 port 53 { ::1; };
	directory 	"/var/named";
	dump-file 	"/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
//	allow-query     { localhost; };

#设定DNS解析域，假设访问的域名为www.yan.com,对DNS的配置文件进行设定，这里只做简单介绍
vim /etc/named.rfc1912.zones
添加如下内容
zone "yan.com" IN {
        type master;
        file "yan.com.zone";
};

#设定指定域的文件
vim /var/named/yan.com.zone
内容为：
$TTL 1D
@	IN SOA	dns1   mail  (
					2017091902	; serial
					1D	; refresh
					1H	; retry
					1W	; expire
					3H )	; minimum
	NS	dns1
dns1	A	172.18.18.40
websrv  A 	172.18.18.10   #指定对应的web服务器地址
websrv  A   172.18.18.30   #指定对应的web服务器地址
www 	CNAME 	websrv

#重新启动DNS服务
service named restart 

```

### 6、测试

> 通过命令links进行访问测试
> 测试页面如下：

> (1)WEB1

![测试成功页面](/assets/pictures/mini-lamp/success1.png)

> (2)WEB2

![测试成功页面](/assets/pictures/mini-lamp/success2.png)

> 通过以上两次连续的测试可以发现，DNS能够通过DNS的轮巡来进行对流量的分流从而达到负载均衡的目的

> 浏览器当中访问http://www.yan.com/blog/
> 出现以下页面

![登录测试页面](/assets/pictures/mini-lamp/ready.png)

> 表示设置成功，后续的详细操作可以参照本站点的博客 [《LAMP》](http://www.daysunshine.com/blog/)

> #### 至此基于lamp，通过DNS的轮巡实现负载均衡完毕，如有错误还望批评指正，敬请谅解! 


















