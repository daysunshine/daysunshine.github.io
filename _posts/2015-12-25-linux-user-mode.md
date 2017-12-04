---
layout: post
title:  用户、组、文件权限属性
date:   2015-12-25 08:00:00
categories: Linux 
tags:  用户、权限
---

###1、用户和组的配置文件

> 在Linux中用户和组的配置文件主要为：
  - /etc/passwd  :存放用户及其属性的信息（名称、GID、UID）
  - /etc/group   :存放组及其属性信息
  - /etc/shadow  :存放用户密码及其相关属性
  - /etc/gshadow :存放组密码及其相关属性
  
####(1)、/etc/passwd的格式

```
root:x:0:0:root:/root:/bin/bash 
分别表示为：用户名 ：密码 : UID : GID : 描述信息 : 家目录 : shell类型
```

####(2)、/etc/shadow的格式

```
root::17361:0:99999:7:::
分别表示为：用户名 : 密码 : 距1970年天数 : 密码最短有效期 : 最长有效期 : 提前通知天数 : 过期天数 : 账户有效期
其中密码部分为:
$6$1C6dXZDTp.Kccbji$8K.gFoDfNtFONEoWV5I9s87Fun6kw2NSlQqIEsrNblzrfSydSXQ9We3MW/6Ow1KPuI4xtZjPDffOY1j3Ccg9x.
"6":表示其算法为sha512
"1C6dXZDTp.Kccbji"表示系统随机生成的随机数用于密码中增加密码的安全性
最后一部分为：加密生成的最终的密码	 
 
在密码最前面如有"!"则代表账户被锁定，不能登录
eg：tcpdump:!!:17361::::::

####(3)、/etc/groupd的格式

```
root:x:0:
分别表示为：用户名 : 组密码(X表示) : GID : 附加组
```
###2、用户和组的相关命令

####(1)useradd 添加用户

```
useradd  [options]  username
     -u UID ：指定用户的UID
	 -g GID ：指定用户的GID
	 -o     ：检查ID的唯一性
	 -c     ：用户的注释信息
	 -s     ：shell类型
	 -G group：为用户添加附加组（组需事先存在）
	 -r     ：添加系统组
	                    CentOS 6  系统ID：0-499 ，其他用户ID：500-60000
						CentOS 7  系统ID：0-999 ，其他用户ID：1000-60000
创建用户时的默认设置文件：/etc/default/useradd	
当用户被创建时用户的家目录会自动生成与/etc/skel目录下相同的文件
/etc/login.defs目录下为用户登录时的属性默认设置，可通过更改文件设定来改变包括用户有效期、UID、GID以及加密算法等在内的配置信息
```

####(2)usermod 用户属性修改

```
usermod [options] username 
     -u UID :修改新的UID
	 -g group :新的基本组
	 -aG group :增加新的附加组至用户
	 -md home :创建新的家目录，并移动原家目录数据至新家目录
	 -s shell :更改shell
	 -c "comment" :修改注释信息
	 -l login_name:更改登录名称
	 -L username :锁定指定用户，在/etc/shadow 密码列增加"!"
	 -U username :解锁指定用户
	 -e YY-MM-DD :指定用户账户过期时间
```

####(3)userdel 删除指定用户

```
userdel [options] username
     -r :将指定用户信息全部删除
	 -f :强制删除
```

####批量创建用户及更改口令

```
1、批量创建用户
     需要事先创建和/etc/passwd格式一样的文件，如user.txt
     命令newusers  user.txt 可以批量创建user.txt内的用户，但创建后用户家目录下会缺少一些文件，可以
     去/etc/skel目录下复制过来使用。
2、批量更改口令
     先创建文本passwd.txt内容格式如：username:passwd 
	 执行命令cat passwd | chpasswd 将文本中的用户与密码设定成功 
```

####(4)groupadd、groupmod、groupdel 创建组、修改组、删除组

```
group [options] groupname
     -g GID :设定组ID
	 -r     :创建系统组
	                   CentOS 6  系统ID：0-499 ，其他用户ID：500-60000
					   CentOS 7  系统ID：0-999 ，其他用户ID：1000-60000
groupmod [options] group 
     -n groupname :更改新的组名
     -g GID       :新的GID
groupdel :删除组，若删除的组为其他组的主组则不能删除。
```

####(5)passwd 修改指定用户的密码

```
passwd [options] username 
     -l :锁定指定用户
	 -u :解锁指定用户
	 -e :用户下次登录强制修改密码
	 --stdin :接受标准输入
```

####(6)gpasswd 更改组密码

```
gpasswd [options] group 
     -a username:将用户添加到指定的组
	 -d username:从指定的组中将用户删除
	 -A username:设置有管理权限的列表
```

####(7)groupmems 

```
groupmems [options] 
     -g group :指定要操作的组
	 -a username :指定用户加入组
	 -d username :从组中删除用户
	 -p :清空所有成员
	 -l :显示组列表
```

###3、文件权限属性
    再打开文件和目录时显示其详细信息如下：
```
####(1)文件属性信息
-rw-r--r--. 1 root mage 1738 Jul 19 19:18 /etc/passwd
其中：
第一位是文件的类型：
                     - :普通文件
					 -d:目录
					 -c:字符设备
					 -b:块设备
					 -l:连接文件
					 -s:套接字
					 -p:管道文件
rw-r--r--为文件的权限：r(读)、w(写)、x(执行)、-(无)
                     前三位：为文件所属主的权限		
					 中三位：为文件所属组的权限
					 后三位：为文件其他人的权限
 1 root mage         
                     1:为其链接数
                     root:为其属主
					 mage:为其属组
1738 Jul 19 19:18	
                     1738：为其文件的大小，单位默认为K
                     Jul 19 19:18 :为其访问时间
/etc/passwd          :为其文件路径
```

####(2)属性更改          					 

```
chown 设置文件的属主、属组
     chown [OPTION]... [OWNER][:[GROUP]] FILE...
     chown user1:group1 file  更改文件file的属主以及属组
     chown user1:       file  只更改文件file的属主
     chown      :group1 file  只更改文件的属组
     chown -R                 递归更改
chmod 设置文件的权限属性
     chmod [WHO] [OPTION]... MODE[,MODE]... FILE...
     [who]   : user、group、other
	 [option]: + 增加 
	           - 去除
			   = 赋值为
			   a 所有(all)
	 [mode]  : r、w、x、X(作用在目录上，配合-R使用会将目录下的除文件以外的目录加上x执行权限)
eg : chmod u+rw  file 将file文件加上rw权限
*注意：Linux系统对于目录中的文件来说，文件能否被用户删除与文件本身的权限无关，
       而与文件的父目录有关，当用户访问父目录具有w(写)权限时才能够将文件删除	   
```

####(3)特殊权限 suid、sgid、sticky

```
suid (作用在可执行的二进制文件上): 访问者会继承文件所有者的权限
     (用s表示)
sgid (作用在可执行的二进制文件上): 访问者会继承文件所属组的权限
     (用s表示)
     (作用在目录上)              : 目录下新建的文件的所属组会继承目录的所属组(同属一个组的成员可以新建、互相更改及删除文件) 
sticky (作用在目录上)            : 在一个公共的目录上每个人都可以创建、删除自己文件，但不能删除别人的文件，只有所有者能够删除
       (用t表示)
eg : 
[root@Centos6 /app]#chmod u+s passwd
-rwSr-xr-x. 1 root root  1664 Jul 19 02:57 passwd  为passwd文件加上suid权限  这里说明:若文件权限最初无x(执行权限)则为S，反之为s
[root@Centos6 /app]#chmod g+s passwd
-rw-r-sr-x. 1 root root  1664 Jul 19 02:57 passwd  为passwd文件加上sgid权限
[root@Centos6 /app]#chmod o+t passwd
-rw-r-xr-t. 1 root root  1664 Jul 19 02:57 passwd  为passwd文件加上sticky权限
```

####(4)访问控制列表(ACL)

```
    在现实的生产中我们通过更改权限的命令只能设定群体的用户，但是我们却想要特定的文件只能某个用户访问或者拒绝其访问
这时就需要ACL来实现除了所有者、所属组和其他人，可以对更多的特定用户设置权限了
setfacl (set file acl) 设定文件ACL权限
     -m 设定权限
	 -x 取消权限
	 -R 递归操作
	 -k 清空默认acl
	 -b 彻底删除acl权限，包括mask
eg: 
     setfacl -m u:user:r  file  设定文件file对user只有r(读)权限
	 setfacl -m u:user:rx dir   设定目录对于user来说，在目录下新建文件时默认具有rx权限
     setfacl -m g:group:w dir   设定目录dir对group有写权限	 
[root@Centos6 /app]#setfacl -m u:ymd:rx passwd
-rw-r-xr-x+ 1 root root  1664 Jul 19 02:57 passwd  设定文件ACL权限后有"+" 为ymd用户对passwd文件设定rx权限
[root@Centos6 /app]#getfacl passwd                 getfacl 查看设定的权限
# file: passwd
# owner: root
# group: root
user::rw-        所属主
user:ymd:r-x     自定义用户
group::r-x       所属、其表现出来的权限实际为mask的权限
mask::r-x        限制最高权限
other::r-x       其他人

备份与恢复ACL
getfacl -R /file > backup.txt  将文件file备份到backup.txt
setfacl --restore backup.txt   通过backup.txt恢复acl
```


