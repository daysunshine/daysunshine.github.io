---
layout: post
title:  yum及源码编译httpd
date:   2017-08-08 08:00:00
categories: Linux 
tags:  yum  源码编译httpd
---

> yum本身相比于rpm来说，能够将有依赖的包文件一次性的安装完成，是相当的方便的。
> yum的服务器支持的几种格式：
> -        http、https、ftp、file

### 1、yum基础命令
#### 1、yum命令

```shell

     yum [options] [command] [package ...]	
		 [options]: 基本不用
		 [command]: 
		         repolist[all | enable | disable]: 显示仓库列表[所有|启用|禁用] 
		         list[all | glob]: 显示程序包[所有 | 通配符]
		         -y : 在安装时自动回答yes
```
```shell
     	安装程序包: yum install package  安装 
               	 yum reinstall package  重新安装
	升级程序包: yum update package 升级
	             yum downgrade package 降级
	卸载程序包: yum remove | erase  package 
	清除本地yum缓存
	当更换光盘时，往往在新建yum时会导致不能读到光盘，这时我们就需要进行清除yum的缓存
		 yum clean all 
```
#### 2、查看yum历史(基于yum历史可以实现撤销操作)

> 命令格式：yum history [info | list | ... ]

```shell
	eg: [root@Centos6 ~]#yum history 
             Loaded plugins: fastestmirror, refresh-packagekit, security
            ID | Login user               | Date and time    | Action(s)      | Altered 
             -------------------------------------------------------------------------------
             3 | root <root>              | 2017-08-05 02:13 | Install        |    1   
             2 | root <root>              | 2017-08-04 12:32 | Install        |   46  <
             1 | System <unset>           | 2017-07-14 10:38 | Install        | 1232 > 
             history list	
        eg: yum history redo 3  会将root安装的历史步骤重做相当于卸载
            yum history undo 3  会将root安装历史步骤撤销
```
#### 3、包组管理的相关命令
         yum groupinstall group1 安装包组
         yum groupupdate group1  升级包组
		 yum grouplist group1    列出包组清单
		 yum groupremove group1  移除包组
		 yum groupinfo group1    包组详细信息
### 2、yum仓库的创建
#### (1)本地yum源

```
    1、yum的配置文件
        /etc/yum.conf  为所有的yum仓库提供公共配置
        /etc/yum.repo.d/*.repo  为仓库的指向提供配置
        yum的repo配置文件中可用变量
	    $releasever 当前OS发行版的主版本号
	    $arch  平台 x86_64 、i386 、i486等
	    $basearch
    2、仓库配置文件格式
	   [base]  仓库名称
       name=CentOS-$releasever - Base  定义一个应用名称(可省) 
       baseurl=file:///misc/cd  yum源的寻找路径(将本地光盘作为yum源)
       gpgcheck=1  需要检查key
	           =0  不需要检查key
       gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6   key的寻找路径
	   enabled=1 仓库启用
	          =0 仓库禁用
	 到此本地yum源完成
```
#### (2)基于ftp网络yum源
```
 1、搭建ftp服务
    (1)首先要找到基于网络上的元数据(repodata)，我们要记住repodata的父目录接下来我们要用到
    (2)通过rpm命令安装vsftpd安装包,并查看本机服务地址及所开的端口号是否打开 netstat -ntl  /  ss -ntl 
    (3)开启vsftpd服务
        centos 6 为systemctl start vsftpd 
        centos 7 为service vsftpd start 
       开机自启vsftpd服务
         centos 6 : chkconfig vsftpd on 
         centos 7 : systemctl enable vsftpd 
	(4)关闭防火墙服务
	     centos 6 : service iptanles stop  
		 centos 7 : systemctl stop firewall
		开机自动关闭
		 centos 6 : chkconfig iptables off
		 centos 7 : systemctl disable firewall
    (5)关闭selinux
	     setenforce=0
	   开机自动生效
	     vim /etc/selinux/config
		    更改SELINUX=permissive
	(6)将光盘里的文件复制到需要通过网络服务共享的文件夹里 /var/ftp/pub下
	(7)创建yum配置文件
	    这里与本地yum仓库的配置文件类似，这里不在说明
```
#### (3)源码编译httpd

```
   在生产中，我们安装工具时会遇到很多命令或工具会有依赖关系，很是麻烦，并且还不能定制特性
 1、下载源码并解压
     eg : tar xf httpd-2.4.25.tar.gz
	 需要注意的是
 2、安装开发工具包   
       因源码是通过C语言来编译的，所以在编译时需要gcc等工具来对源码进行操作
     eg : yum install "Development Tools"
 3、进入解压后的目录
    cd httpd-2.4.25
	阅读INSTALL README
 4、运行configure脚本
     (1)通过选项指定参数，指定启用特性，安装路径等；执行时会参考用户的makefile.in文件生成makefile
	 (2)检查依赖的外部环境，如依赖的软件包
     ./configure --prefix=/path/to/somewhere  --sysconfdir=/path/to/somewhere  为其指定安装位置及配置文件安装位置
	 在其安装过程中会提示错误，缺少安装包，只需要将缺的安装包对应安装即可
 5、make 及make install
    make : 根据生成的makefile文件构建应用程序
	makeinstall : 复制文件到相应路径
   到此还没有彻底完成，还有后续的配置
 6、访问测试
    iptables  -vnL  查看防火墙状态
	若防火墙打开，则将其关闭
	iptables -F 关闭防火墙
 7、启用服务
     httpd的启动程序在指定的安装目录下的/bin/apachectl 
	 将其加入PATH变量中
	 echo "export PATH=/path/to/somewhere/bin/apachectl:$PATH" > /etc/profile.d/xxx.sh
	 而后 ./apachectl start 即启动服务
```
### 3、附上自己源码编译简单脚本:

```
# 查询httpd命令之前是否安装过，如果安装过则移除
rpm -qa "httpd*" &> /dev/null && yum remove httpd   
# 判断系统版本号
centos=`cat /etc/centos-release | grep -o " [0-9]"|cut -d" " -f2`  
# 解压相对应的httpd包
if [ $centos = 6 ] ;then
          tar xf httpd-2.2.34.tar.bz2 &> /dev/null && echo "Uncompress Complete" || echo "failed"
elif
   [ $centos = 7 ];then
          tar xf httpd-2.4.27.tar.bz2 &> /dev/null && echo "Uncompress Complete" || echo "failed"    
fi  
#进入解压后的目录
cd   httpd-*/  
# 安装developmrnt tools包组
    yum groupinstall "development tools" -y &>/dev/null && echo "Development install successed!" || echo " Devement installfailed "
sleep 3      
# 运行configure脚本
    ./configure --prefix=/app/httpd25/ --sysconfdir=/app/httpd25/etc && echo "Configure successed!" || echo "Configure filed!"
sleep 3    
    make && make install && echo "Make install successed"  
sleep 3
# 启动httpd服务
/app/httpd25/bin/apachectl  start   
# 判断80端口是否打开
dk=`ss -ntl | grep -o ":::80"`     
if [ $dk == ":::80" ] ;then
    echo "80-port is open" 
else 
    echo "80-port is not open please restart"
     exit 10
fi
# 导入man帮助
if [ $centos = 6 ] ;then  
     echo "MANPATH /app/httpd25/man" >> /etc/man.config
else 
     echo "MANPATH /app/httpd25/man" >> /etc/man_db.conf
fi 

```
