---
layout: post
title:  二进制安装mariadb
date:   2017-09-25 08:00:00
categories: Linux 
tags:  Mariadb
---


#### 1、创建MySQL用户

```shell

#创建MySQL用户,-r 指定其为系统用户,id为306，-d 设定其家目录为/app/data,-s 设定登录shell为nologin
[root@centos6 ~]#useradd  -r -u 306 -s /sbin/nologin -m -d /app/data  mysql 

#查看是否创建成功
[root@centos6 ~]#getent passwd mysql
mysql:x:306:306::/app/data:/sbin/nologin

#查看mysql的家目录属组属主为mysql
[root@centos6 ~]#ll -d  /app/data
drwx------ 4 mysql mysql 4096 Sep 24 07:34 /app/data

```

#### 2、准备二进制程序

> &emsp;&emsp;二进制程序包已经内部设定好安装路径，我们最好解压到/usr/local/下，当然也可以安装到自己指定的地方，但需要将其配置文件当中的路径进行更改，否则会报错

```shell

#将二进制解压至/usr/local/下
[root@centos6 ~]#tar xvf mariadb-5.5.57-linux-x86_64 -C /usr/local/

#为解压后的目录创建连接，并命名为mysql(配置当中的要求)，不建议将其重命名为mysql
[root@centos6 ~]#cd /usr/local/
[root@centos6 /usr/local]#ln -s mariadb-5.5.57-linux-x86_64/ mysql

```

> 我们有必要了解一下解压后目录内的文件有哪些

```shell

[root@centos6 ~]#ll /usr/local/mariadb-5.5.57-linux-x86_64/
total 192
drwxr-xr-x  2 root root   4096 Sep 22 19:12 bin					      #二进制程序存放的位置
-rw-r--r--  1 yan  yan   17987 Jul 19 04:33 COPYING
drwxr-xr-x  3 root root   4096 Sep 22 19:12 data				      #数据存放的位置，但一般在实际中需要将其放置在逻辑卷中，本文以mysql的家目录为例，作为数据存放的位置
-rw-r--r--  1 yan  yan    8245 Jul 19 04:33 EXCEPTIONS-CLIENT
drwxr-xr-x  3 root root   4096 Sep 22 19:12 include
-rw-r--r--  1 yan  yan    8694 Jul 19 04:33 INSTALL-BINARY		#安装手册目录
drwxr-xr-x  3 root root   4096 Sep 22 19:11 lib					      #库文件
drwxr-xr-x  4 root root   4096 Sep 22 19:12 man
drwxr-xr-x 11 root root   4096 Sep 22 19:10 mysql-test
-rw-r--r--  1 yan  yan  108813 Jul 19 04:33 README
drwxr-xr-x  2 root root   4096 Sep 22 19:12 scripts				    #初始化脚本目录
drwxr-xr-x 27 root root   4096 Sep 22 19:12 share
drwxr-xr-x  4 root root   4096 Sep 22 19:12 sql-bench
drwxr-xr-x  3 root root   4096 Sep 22 19:12 support-files		  #配置文件模板

```

> &emsp;&emsp;我们看到属组属主为非mysql用户，所以将mysql下的所有文件更改为mysql所拥有
> &emsp;&emsp;这里说明在mysql初始化脚本完成后需要将mysql的属组更改回root所拥有，避免mysql被黑客攻破后能够直接利用mysql当中的数据

```shell

[root@centos6 /usr/local]#chown -R mysql.mysql mysql/*

```

#### 3、创建数据库文件

```shell

#切换至mysql目录，否则会报错
[root@centos6 ~]#cd /usr/local/mysql  

#执行初始化脚本命令
[root@centos6 /usr/local/mysql]#scripts/mysql_install_db --datadir=/app/data --user=mysql

#查看却已生成数据库库文件
[root@centos6 /usr/local/mysql]#ls /app/data/
aria_log.00000001  aria_log_control  mysql  performance_schema  test

#将mysql下的所有文件属主更改为root所拥有
chown -R root mysql/*

```

#### 4、准备配置文件

> &emsp;&emsp;二进制当中的配置文件查找次序为: ~/.my.cnf--> --default-extra-file=/PATH/TO/CONF_FILE --> /etc/mysql/my.cnf --> /etc/my.cnf

```shell

#创建配置文件的目录
mkdir  /etc/mysql/

#复制配置文件至设定目录下
cp support-files/my-large.cnf /etc/mysql/my.cnf

#并在[mysqld]选项中添加如下内容
datadir = /app/data					    #指定数据库的位置
innodb_file_per_table = on			
skip_name_resolve = on				  #禁止主机名解析

```

#### 5、设置服务脚本

> &emsp;&emsp;在二进制包当中已经有编写好的服务脚本在/mysql/support-files/mysql.server处，我们只需将其移动到特定的目录中即可

```shell

#复制到启动目录下
[root@centos6 /usr/local/mysql]#cp support-files/mysql.server  /etc/rc.d/init.d/mysqld

#添加至服务列表并启动
[root@centos6 /usr/local/mysql]#chkconfig  --add  mysqld

#启动mysqld服务
[root@centos6 /usr/local/mysql]#service  mysqld  start
Starting MySQL.170924 09:07:28 mysqld_safe Logging to '/var/log/mysqld.log'.
170924 09:07:28 mysqld_safe Starting mysqld daemon with databases from /app/data
/usr/local/mysql/bin/mysqld_safe_helper: Can't create/write to file '/var/log/mysqld.log' (Errcode: 13)
                                                           [FAILED]
提示失败，需要先创建日志文件

#创建日志文件
[root@centos6 /usr/local/mysql]#touch /var/log/mysqld.log

#因日志文件是由mysql这个系统用户访问的所以要给其访问的权限
#更改日志文件的属主
[root@centos6 /usr/local/mysql]#chown  mysql  /var/log/mysqld.log

#再次启动服务
[root@centos6 /usr/local/mysql]#service mysqld start
Starting MySQL.170924 09:11:56 mysqld_safe Logging to '/var/log/mysqld.log'.
170924 09:11:56 mysqld_safe Starting mysqld daemon with databases from /app/data
.........                                                  [  OK  ]

```

#### 6、设定环境变量

> &emsp;&emsp;二进制安装时环境变量找不到mysql的二进制执行命令，为了方便需要手动添加至环境变量里，而不用写其绝对路径

```shell

#添加环境变量路径
[root@centos6 /usr/local/mysql]#vim  /etc/profile.d/mysql.sh

export PATH=$PATH:/usr/local/mysql/bin

#重读配置文件
[root@centos6 /usr/local/mysql]#. /etc/profile.d/mysql.sh

```

> 至此mysql已经能够运行了，但是为了安全性考虑，需要进行安全初始化
> 运行命令脚本: /user/local/mysql/bin/mysql_secure_installation，来进行安全设定


#### **到此二进制安装Mariadb就结束了，如有错误之处请批评指正！**



























































































































































































































