---
layout: post
title:  LAMP
date:   2017-10-11 08:00:00
categories: Linux 
tags:  centos7.3编译实现LAMP PHP配置
---

## 介绍

> &emsp;&emsp;LAMP指的Linux(操作系统)、ApacheHTTP 服务器，MySQL(有时也指MariaDB，数据库软件) 和PHP(有时也是指Perl或Python) 的第一个字母，一般用来建立web应用平台。
> &emsp;&emsp;LAMP(Linux- Apache-MySQL-PHP)网站架构是目前国际流行的Web框架，该框架包括:Linux操作系统，Apache网络服务器，MySQL数据 库，Perl、PHP或者Python编程语言，所有组成产品均是开源软件，是国际上成熟的架构框架，很多流行的商业应用都是采取这个架构，和 Java/J2EE架构相比，LAMP具有Web资源丰富、轻量、快速开发等特点，微软的.NET架构相比，LAMP具有通用、跨平台、高性能、低价格的 优势，因此LAMP无论是性能、质量还是价格都是企业搭建网站的首选平台。
> &emsp;&emsp;对于大流量、大并发量的网站系统架构来说，除了硬件上使用高 性能的服务器、负载均衡、CDN等之外，在软件架构上需要重点关注下面几个环节:使用高性能的操作系统(OS)、高性能的网页服务器(Web Server)、高性能的数据库(Database)、高效率的编程语言等。

### PHP配置

> PHP是一种脚本解释性语言，这里仅是进行简单的介绍其应用到的配置文件
> 在进行安装后配置文件也相应生成

> 配置文件：/etc/php.ini, /etc/php.d/*.ini
> 配置文件在php解释器启动时被读取
> 对配置文件的修改生效方法
>> Modules：重启httpd服务
>> FastCGI：重启php-fpm服务

> 查看PHP的配置文件的格式

```shell

说明：
注释符：较新的版本中，已经完全使用;进行注释
#：纯粹的注释信息
;：用于注释可启用的directive

选项信息：

max_execution_time=30 		最长执行时间30s
memory_limit 128M 			生产不够，可调大
display_errors off 			调试使用，不要打开，否则可能暴露重要信息
display_startup_errors off 	建议关闭
post_max_size 8M 			最大上传数据大小，生产可能临时要调大，比下面项要大
upload_max_filesize 2M 		最大上传文件，生产可能要调大
max_file_uploads=20 		同时上传最多文件数
date.timezone=Asia/Shanghai 指定时区
short_open_tag=on 			开启短标签,如<? phpinfo();?>

```

> PHP的语言格式

```shell

<?php
	...phpcode...  代码段
?>

示例

<?php
	echo "<h1>Hello world!</h1>"
?>

```

> 使用mysql扩展连接数据库的测试代码

```shell

<?php
$conn = mysql_connect(‘mysqlserver','username','password');
if ($conn)
	echo "OK";
else
	echo "Failure";
mysql_close();
?>

```

> Php使用mysqli扩展连接数据库的测试代码

```shell

<?php
$mysqli=new mysqli("mysqlserver",“username",“password");
if(mysqli_connect_errno()){
	echo "连接数据库失败!";
	$mysqli=null;
	exit;
}
echo "连接数据库成功!";
$mysqli->close();
?>

```

#### 布署phpMyadmin

> PhpMyAdmin是一个以PHP为基础，以Web-Base方式架构在网站主机上的MySQL的数据库管理工具，让管理者可用Web接口管理MySQL数据库

> 实现步骤

```shell

yum -y install httpd mariadb-server php php-mysql
service  httpd start
service mariadb start
#进行安全设置
mysql_secure_installation
phpMyAdmin下载地址：https://www.phpmyadmin.net/downloads/
#解压
tar xvf phpMyAdmin-4.0.10.20-all-languages.tar.xz cd /var/www/html
ln -s phpMyAdmin-4.0.10.20-all-languages mad
cd mad/
cp config.sample.inc.php config.inc.php
yum -y install php-mbstring
service httpd reload

```

#### 布署wordpress

> WordPress是一种使用PHP语言开发的博客平台，用户可以在支持PHP和MySQL数据库的服务器上架设属于自己的网站。也可把WordPress当作一个内容管理系统（CMS）来使用

> 实现步骤

```shell

下载地址：
官网：https://cn.wordpress.org/
解压缩WordPress博客程序到网页站点目录下
	unzip wordpress-4.3.1-zh_CN.zip
新建wpdb库和wpuser用户
	mysql> create database wpdb;
	mysql> grant all privileges on wpdb.* to wpuser@'%' identified by "wppass";
打开http://webserver/wordpress进行页面安装
注意wordpress目录权限
	Setfacl –R –m u:apache:rwx wordpress
	
```

#### 编译Php-xcache加速访问

```shell

yum -y install php-devel
下载并解压缩xcache-3.2.0.tar.bz2    #目前PHP5.5以上的还不能被xcache所支持，这里要注意
phpize生成编译环境
cd xcache-3.2.0
./configure --enable-xcache --with-php-config=/usr/bin/php-config
make && make install
cp xcache-3.2.0/xcache.ini /etc/php.d/
systemctl restart httpd.service

```

### 编译实现LAMP并实现WordPress

> 为保证编译成功，请确保系统是干净的，没有同类软件正在进行服务
> 这里进行说明编译用到的包的版本信息

```shell

mairadb：通用二进制格式，mariadb-10.2.8
apr-1.6.2：httpd安装的依赖包
apr-util-1.6.0：httpd安装的依赖包
httpd：编译安装，httpd-2.4.27
php5：编译安装，php-7.1.10
WordPress：安装wordpress-4.8.1-zh_CN

```

**注意**
> 因php依赖于mariadb-devel包：所以mariadb要先安装，PHP最后安装，这里指定安装次序为：mariadb-->httpd-->php
> centos7实现apache、PHP
> centos6实现Mariadb

#### 1、源码编译Httpd2.4

> 在安装httpd之前请确保已经将开发包组安装完毕，如未安装请执行命令
```shell
	yum groupinstall "development tools"
```

> 当然在编译安装过程当中还需要一些devel包需要安装，这里先进行提前的安装，不同的环境可能所需devel包不同，如在进行编译安装的时候提示还需要安装其他的devel包请自行按照提示进行安装
```shell
	yum install openssl-devel expat-devel pcre-devel
```

> 编译步骤

```shell

#下载源码并解压，将源码下载至/usr/local/src/下
tar xvf apr-1.6.2.tar.gz 
tar xvf apr-util-1.6.0.tar.gz 
tar xvf httpd-2.4.27.tar.bz2 

#将apr的依赖目录复制到httpd目录下的/srclib并重命名成apr、及apr-util
cp -r apr-1.6.2  httpd-2.4.27/srclib/apr
cp -r apr-util-1.6.0  httpd-2.4.27/srclib/apr-util

#进行编译安装
cd httpd-2.4.27/
./configure --prefix=/app/httpd24 --sysconfdir=/etc/httpd24 --enable-so --enable-ssl --enable-rewrite --with-zlib --with-pcre --with-included-apr --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork
make -j 4 && make install   #使用4个CPU来使其编译速度更快

#设定环境变量
#创建文件/etc/profile.d/lamp.sh并添加如下内容
PATH=/app/httpd24/bin/:$PATH

#启动httpd服务
apachectl  start

> #如在centos6进行编译安装也可通过以下方法来实现启动服务
	cp  /etc/init.d/httpd  /etc/init.d/httpd24

> #编辑文件/etc/init.d/httpd24，并更改为如下内容
	apachectl=/app/httpd24/bin/apachectl
	httpd=${HTTPD-/app/httpd24/bin/httpd}
	prog=httpd
	pidfile=${PIDFILE-/app/httpd24/logs/httpd.pid}
	lockfile=${LOCKFILE-/var/lock/subsys/httpd24}


#查看验证服务是否启动
[root@centos7 /etc/httpd24]#ss -ntl | grep 80
LISTEN     0      128         :::80               :::*   #成功启动

```

> #### 到此源码编译httpd已经完成！

#### 2、二进制安装Mariadb

> 编译步骤
> 因之前的文档已经详细介绍二进制安装Mariadb，这里只做简单的介绍，详细请参考[《二进制安装Mariadb》](http://www.daysunshine.com/blog/2017/09/25/linux-mariadb-binary-installtion)

```shell

#解压至指定目录
tar xvf mariadb-10.2.8-linux-x86_64.tar.gz  -C /usr/local/

#为解压后的目录设定软连接，并命名为mysql
cd /usr/local
ln -s mariadb-10.2.8-linux-x86_64/ mysql

#创建系统mysql用户并指定家目录用于放置数据的地方
useradd -r -m -d /app/mysqldb -s /sbin/nologin mysql 

#执行生成数据库的脚本
cd  mysql/
scripts/mysql_install_db --datadir=/app/mysqldb --user=mysql  #指定数据库存放位置及用户为mysql

#设定数据库配置文件
mkdir /etc/mysql
cp /usr/local/mysql/support-files/my-large.cnf   /etc/mysql/my.cnf

#编辑配置文件
vim /etc/mysql/my.cnf
#在[mysqld]内添加如下内容
datadir = /app/mysqldb
innodb_file_per_table = ON
skip_name_resolve = ON

#设定Mariadb的服务脚本
cp /usr/local/mysql/support-files/mysql.server  /etc/init.d/mysqld

#添加服务脚本并启动
chkconfig --add mysqld
service mysqld start

[root@centos7 /usr/local/mysql]#service mysqld start
Starting mysqld (via systemctl):  Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.
                                                           [FAILED]

#根据提示进行日志的设定，让mysqld服务对日志文件有权限即可
mkdir /var/log/mariadb
chown mysql /var/log/mariadb/

#查看是否启动成功
[root@centos7 /usr/local/mysql]#service mysqld start
Starting mysqld (via systemctl):                           [  OK  ]

#设定环境变量
#编辑文件/etc/profile.d/lamp.sh，并更改内容为
PATH=/app/httpd24/bin/:/usr/local/mysql/bin/:$PATH

#对数据库进行安全设定,根据提示进行填写
mysql_secure_installation

#登录数据库并进行数据库及用户的创建、授权
mysql -uroot -pcentos
create database wpdb;
grant all on wpdb.* to test@'172.18.18.%' identified by 'centos';
grant all on wpdb.* to test@'127.%' identified by 'centos';
grant all on wpdb.* to test@'localhost' identified by 'centos';

```

> #### 二进制编译安装Mariadb完成！

#### 3、PHP

> 在编译PHP的时候也会依赖一些devel包，我们提前先进行安装,如在编译的过程中还提示错误请根据提示进行相应的安装

```shell
	yum install libxml2-devel bzip2-devel libmcrypt-devel
```

> 编译步骤

```shell

#进行解压
tar xvf php-7.1.10.tar.xz

#进行编译安装
cd  php-7.1.10/

./configure \
--prefix=/app/php \			#指定安装的路径
--enable-mysqlnd \
--with-mysqli=mysqlnd \
--with-openssl \
--with-pdo-mysql=mysqlnd \
--enable-mbstring \
--with-freetype-dir \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--enable-sockets \
--with-apxs2=/app/httpd24/bin/apxs \
--with-mcrypt \
--with-config-file-path=/etc \
--with-config-file-scan-dir=/etc/php.d \
--enable-maintainer-zts \
--disable-fileinfo

make -j 4 && make install

#设定配置文件
cp  php-7.1.10/php.ini-production  /etc/php.ini  #复制并重命名即可

#设定httpd的配置文件，添加让httpd能够识别php的程序
vim /etc/httpd24/httpd.conf
在文件尾部加两行
	AddType application/x-httpd-php .php
	AddType application/x-httpd-php-source .phps
修改下面行，更改为如下内容
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

#重启httpd服务
apachectl restart

```

#### 4、测试PHP与Mariadb连接

```shell

#编辑测试PHP文件
vim /app/httpd24/htdocs/index.php
#添加如下内容
<?php
	$mysqli=new mysqli("172.18.18.10","test","centos");
	if(mysqli_connect_errno())
{
	echo "连接数据库失败!";
	$mysqli=null;
	exit;
}
	echo "连接数据库成功!";
	$mysqli->close();
	echo date("Y/m/d");
	phpinfo();
?>

```
> 在浏览器当中进行访问测试，查看是否成功

![PHP与Mariadb连接测试](/assets/pictures/php-mariadb-test.png)



#### 5、配置WordPress

> 

```shell

#解压缩WordPress博客程序到网页站点目录下
tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /app/httpd24/htdocs

#对解压后的文件进行重命名
cd /app/httpd24/htdocs
ln -s wordpress/ blog

#设定WordPress具有对wordpress/目录的控制权限
setfacl -R -m u:apache:rwx wordpress

```

#### 登录测试

> 通过浏览器登录http://172.18.18.10/blog
> 填写必要的信息如图所示

![填写信息](/assets/pictures/localdenglu.png)

> 账户密码的设定

![登录设置](/assets/pictures/denglusetting.png)

> 设定成功提示

![设定成功](/assets/pictures/success.png)

> 通过ab命令来进行压力测试
ab -c 10 -n 100 http://172.18.18.10/blog/

```shell
...
部分省略
Total transferred:      5228000 bytes
HTML transferred:       5201900 bytes
Requests per second:    12.17 [#/sec] (mean)   #每秒处理请求为12.17个
Time per request:       821.716 [ms] (mean)
Time per request:       82.172 [ms] (mean, across all concurrent requests)
Transfer rate:          621.32 [Kbytes/sec] received
部分省略
...

```

#### 至此centos7.3编译安装LAMP并实现WordPress完成，如有错误还望批评指正，敬请谅解！
