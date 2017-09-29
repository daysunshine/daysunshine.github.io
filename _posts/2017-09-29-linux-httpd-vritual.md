---
layout: post
title:  httpd服务虚拟主机
date:   2017-09-29 08:00:00
categories: Linux 
tags:  httpd 虚拟主机
---



## 介绍

> 虚拟主机就是通过一台服务器，可以同时存在多个主页，从网站上来进行访问时，就像有多台主机存在一样

> 基于Apache的httpd服务的虚拟主机的设定
> 对于设定虚拟主机有三种方法可以实现

```shell

基于ip	:为每个虚拟主机准备至少一个ip地址
基于port:为每个虚拟主机使用至少一个独立的port
基于FQDN:为每个虚拟主机使用至少一个FQDN

```

> 这里我们先进行设定

IP地址| 主机名| 主网页的目录及页面
:---:| :---:| :---:
172.18.18.20| www.a.com| /var/www/a.com/index.html
172.18.18.30| www.b.net| /var/www/b.net/index.html
172.18.18.40| www.c.org| /var/www/c.org/index.html

> 建立主网页的目录及页面

```shell

#建立所需目录
[root@Centos6 ~]#mkdir /var/www/a.com/
[root@Centos6 ~]#mkdir /var/www/b.net/
[root@Centos6 ~]#mkdir /var/www/c.org/

#编辑首页测试页面文件
[root@Centos6 ~]#echo "www.a.com" > /var/www/a.com/index.html
[root@Centos6 ~]#echo "www.b.net" > /var/www/b.net/index.html
[root@Centos6 ~]#echo "www.c.org" > /var/www/c.org/index.html

#原有的/var/www/html并不改变

```
### 基于IP的虚拟主机设定

```shell

#编辑配置文件，这里建议使用额外的配置文件
[root@Centos6 ~]#vim /etc/httpd/conf.d/virtual.conf

<virtualhost 172.18.18.20:80>
documentroot "/var/www/a.com"
customlog "/var/log/www/a.com-access_log" common
</virtualhost>

<virtualhost 172.18.18.30:80>
documentroot "/var/www/b.net"
customlog "/var/log/www/b.net-access_log" common
</virtualhost>

<virtualhost 172.18.18.40:80>
documentroot "/var/www/c.org"
customlog "/var/log/www/c.org-access_log" common
</virtualhost>

```

> 访问测试

```shell

[root@centos7 ~]#curl 172.18.18.20
www.a.com
[root@centos7 ~]#curl 172.18.18.30
www.b.net
[root@centos7 ~]#curl 172.18.18.40
www.c.org

```

> 我们可以看到能够通过不同的IP地址访问到不同的网站的首页

### 基于FQDN的虚拟主机设定

> 这里需先说明，设定的主机名需要能够通过DNS进行解析到，这里不再对DNS如何搭建进行过多的赘述，可以查看博客如何实现，[《DNS服务》](http://www.daysunshine.com/blog/2017/09/20/linux-server-DNS "DNS服务")

```shell

#编辑配置文件，这里同样建议使用额外的配置文件
[root@Centos6 ~]#vim /etc/httpd/conf.d/virtual.conf

namevirtualhost *:80	--> httpd2.4不需要此指令
<virtualhost *:80>
servername www.a.com
documentroot "/var/www/a.com"
customlog "/var/log/www/a.com-access_log" common
</virtualhost>

<virtualhost *:80>
servername www.b.net
documentroot "/var/www/b.net"
customlog "/var/log/www/b.net-access_log" common
</virtualhost>

<virtualhost *:80>
servername www.c.org
documentroot "/var/www/c.org"
customlog "/var/log/www/c.org-access_log" common
</virtualhost>

```

> 访问测试：
> 具体的DNS设置这里不再介绍
 
```shell

#在另一台客户端主机上进行访问测试

[root@centos6 ~]#curl www.a.com
www.a.com
[root@centos6 ~]#curl www.b.net
www.b.net
[root@centos6 ~]#curl www.c.org
www.c.org


```

> 我们可以看到能够成功访问

**在设置的过程中需要注意一下几点**

> - 1、在虚拟主机的设置还有许多功能，在设置时最少要包含servername及document两项

> - 2、使用虚拟主机后原来的主机名也要同时写入配置文件中

> - 3、在设定主机日志文件时，任何对主机名进行访问时都会在其对应的日志文件生成记录，所以一定要进行处理控制，防止文件过大导致系统崩溃



### 到此httpd的虚拟主机设定完毕，如有错误请批评指正!















































