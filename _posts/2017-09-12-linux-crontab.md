20170712

命令总结
awk
at
authconfig 
arp
arping
bc
bg
bzip2
bzcat
bunzip2
blkid
crontab
chpasswd
curl
cd
convert
chcon 
cdrecord
cp 
compress
cut -d: -f1-3
cat
chfn
chage
chmod
chgrp
chown
chattr
chsh usermod -s
clear =ctrl+l
du
dig
dumpe2fs 
df
dracut
e2fsck
e2label
dd
declare
dos2unix 
dstat
depmod
eject
expr
export
egrep
ethtools
edquota
env
exec 
exit
echo $SHELL $UID $PS1
gdisk
gpg
glances
gedit
getsebool
getent passwd bin
file-roller
fsck
ftp
fdisk
fuser
findmnt
findfs
find
file
fg
fgrep
finger
free  cat /proc/meminfo
grep
gzip
gunzip
grpck
getenforce
getfacl 
groupadd
gpasswd
grub-install
grub-md5-crypt
grub-crypt
grub2-mkconfig 
getent passwd|shadow|group|gshadow
hexdump
htop
hping epel
host
head
hostnamectl
hostname
id -u 
ip 
ifconfig 
ifup
ifdown
iostat
init 0 1 3 5 6
iconv -f gb2312 win.txt  -o linux.txt
jobs
ldd
last 
lftp
lftpget
links
lsmod
insmod
losf
let
locate
last
logout
lsattr
ls
less
logger
ln 
lsusb
losetup  loop <-->file
lsblk
lscpu  cat /proc/cpuinfo 
lvcreate
lvs
lvdisplay
lvremove
lvrename
lvextend
lvreduce
lvconvert 
kill
killall
md5sum
mknod 
modprobe
modinfo
mtr
mount -o loop,acl,ro,remount -B --bind 
mkswap
make
mkisofs
mv
more
mdadm -C -S -D -A -r -f -G --zero-superblock
mkdir
mail
mkinitrd
mkfs.xfs .ext4
mkfs
mke2fs  ext2 
nice
newgrp 
nano
netstat 
nmcli
nslookup
nmtui
nm-connection-editor
newusers
openssl rand 
ps
pkill 
pmap
ppgrep
pstree
pvcreate
pvs
pvdisplay
pvremove
pvmove 
pvscan
parted
partx
partprobe
pwunconv
pwconv
pwck
paste
ping 
pwd
quit
quotacheck -cug
quota
quotaon
quotaoff
repquota
renice
reboot
rmmod = modprobe -r
resize2fs
rmdir
rename
rz
restorecon
read
rpm 
rev
runleve
umount
uncompress
updatedb
unzip
uname -r
uuidgen
useradd
usermod
userdel
unix2dos
vmstat 
vgs
vgdisplay
vgcreate
vgremove
vgchange
vgrename
vgextend
vgreduce
vgimport
vgexport
sha1sum 224 512
source=.
setenforce 
setsebool
setup
sestatus
sysctl
semanage fcontext |port| boolean
sealert
ss
setquota
sha1sum
setfacl
sort
trap
tune2fs 
test
tcpdump
traceroute
tracepath
tail
top
tty
tr
tee
tac
tree
sync
swapon 
swapoff
su
scp
shred
stat
set 
touch 
watch 
who
wc 
wget
warnquota
who am i
whoami
umask
uptime
uniq 
vipw
vim
vi
vigr
xxd 
xz
xfs_grow
xzcat
zcat
zip




useradd usermod userdel
groupadd groupdel groupmod
id


重
轻

0
1
9 
A
B

F
10 

去
IOE
EMC


server/client
c/s

DOMAIN  controller
DC

lscpu
memory
mem

ram
rom 


bit 位   b
byte 字节 B 8位 2^8
00000000 0

11111111 255

2^0=1
2^1=2
2^2=4
8
16
32
64
128
256
512
1024

1K=2^10=1024
1K=1000
1M=2^20=1024K
G
1T=10^12
P 
E
Z
Y
B
N
D

raid

1Gb/s
1Gbps

iSCSI



10
11

000 0
001 1
010 2
..
111 7


N位

0-2^N-1

redhat enterprise linux



lib

sin()

UNIX
LINUX
POSIX


GNU is Not Unix


novell

netware

netscape 

firefox 

SICK


74391081d998963b21483113143eb172e7a4e5f4  CentOS-7-x86_64-Everything-1611.iso
09c68654986feee54dcc6cad72771b7fe4be9703  CentOS-6.9-x86_64-bin-DVD1.iso
1415937993b615956c8c4239544366e0674bf042  CentOS-6.9-x86_64-bin-DVD2.iso


第一个分区 分配 盘符C：
第一个分区 mount on  /

/dev/sda1 mount /boot
/dev/sda2 mount /
mount point
挂载点


/
第二个分区 分配盘符D：

/dev/

磁盘 分区设备名


centos6,7
/dev/sda,b,c

MBR 分区类型
GPT 分区

分区类型：
主分区：一块硬盘最多四个，只有一个激活，不能再划分 1-4
扩展分区：一块硬盘最多一个，不能直接存数据，划分小分区，主加扩展最多四个， 1-4
逻辑分区： 5-


20170714

/ 
/boot 
/usr c:\windows
/dev
/dev/sda
/home/mage
/root 
/etc

200G
1G
/dev/sda1 mount  /boot  1G    ext4 xfs  文件系统类型
/dev/sda2 mount  /      50G
/dev/sda3 mount  /app/  50G
/dev/sda4 		99G
/dev/sda5    		2G  

swap 虚拟内存 2G

FAT32 转换成NTFS分区
convert d:/fs:ntfs


vmware 
ctrl+alt+enter 全屏切换
ctrl+alt  移动鼠标


1k=1000  KB
1m=1000000  MB
1T TB

KiB 1024
MiB 

gedit /etc/gdm/custom.conf
AutomaticLoginEnable=true
AutomaticLogin=root



uid user id

root:0 
sys user:
centos6:1-499
centos7:1-999
wang:500,1000



echo $UID
壳shell

AIX unix

ksh bash


ctrl+l =clear 清屏
logout quit exit ctrl+D 注销


作业：
1 熟悉centos6,7的安装过程 
2 练习前面学习的所有命令


20170717

时区：
timedatectl list-timezones 
timedatectl  set-timezone Asia/Shanghai

centos7
rpm -ivh /run/media/root/CentOS\ 7\ x86_64/Packages/autofs-5.0.7-56.el7.x86_64.rpm 
centos6
rpm -ivh /misc/cd/Packages/screen-4.0.3-19.el6.x86_64.rpm 


echo {a-z}
echo {a..z}
echo {A..z}
echo {z..A}
echo {1..10}
echo {20..10}
echo {a,b,c}.{txt,log}
echo {1..100..3}
echo {0000..100..3}
echo {0000..100..3}.log
touch  /app/{0000..100..3}.log

录像
script -t 2> /app/time.log  -a /app/cmd.log
播放录像
scriptreplay  time.log  cmd.log 

echo '- - -' > /sys/class/scsi_host/host2/scan 



作业：
1、显示10天前是星期几
2、显示当前时间，格式：2016-06-18 10:20:30
3、设置当前日期为2019-08-07 06:05:10
4、在本机字符终端登录时，除显示原有信息外，再显示当前登录终端号，主机名和当前时间
5、今天18：30自动关机，并提示用户


20170719

/boot
/bin
/sbin
/etc
/var
/home
/root
/usr
/tmp
/lib
/lib64
/dev
/mnt
/media
/proc
/sys
/opt
/run
/misc/
/net
/lostandfound

ll ls -l 
d
- 
l 
b 块 	/dev/sda
c 字符 /dev/zero
p pipe
s 
 
作业：
1 显示指定目录下的所有目录，不要文件
2 只显示指定目录下的隐藏文件

glob 通配符

*.mp3


练习
1
ls /var/l*[[:digit:]]*[[:lower:]]  
2
ll /etc/[0-9]*[^0-9] 

3
ls  /etc/[^[:alpha:]][[:alpha:]]*  

4 
ls -d /etc/rc[0-6]*

5 
ls -ad /etc/*.d

6
ls -d /etc/[mnrp]*.conf

copy

7
alias baketc="cp -avr /etc/ /testdir/backup`date +%F`"

批量改文件名
rename  '.txt' '.txt.bak' *.txt
rename  '.bak' '' *.bak      



练习
1
mkdir -pv /app/testdir/dir1/{x,y}/{a,b}

2
mkdir -p /testdir/dir2/{x/{a,b},y}

3
mkdir -p /app/testdir/dir{3,4,5/dir{6,7}}  


4k


output  
input 
redirect


cmd1 | cmd2


20170721 

作业：
13
练习
?1、将/etc/issue文件中的内容转换为大写后保存至/tmp/issue.out文件中
?2、将当前系统登录用户的信息转换为大写后保存至/tmp/who.out文件中
?3、一个linux用户给root发邮件，要求邮件标题为”help”，邮件正文如下：
Hello, I am 用户名,The system version is here,pleasehelp me to check it ,thanks!
操作系统版本信息
?4、将/root/下文件列表，显示成一行，并文件名之间用空格隔开
?5、计算1+2+3+..+99+100的总和
?6、删除Windows文本文件中的‘^M’字符
?7、处理字符串“xt.,l 1 jr#!$mn2 c*/fe3 uz4”，只保留其中的数字和空格
?8、将PATH变量每个目录显示在独立的一行
?9、将指定文件中0-9分别替代成a-j
?10、将文件中每个单词（由字母组成）显示在独立的一行，并无空行


sid 

s--adfasdf-500 administartor
s343434sss-501 guest 


opts:tom ,jerry 
opts:file:写
devs:tom
devs:file：读

security context


20170724

文件
读权限：查看内容，类型
写权限：可以修改内容 
执行权限：是否可运行 对root也有效

目录：
读权限：可以看查看目录内文件列表
执行权限：可以cd进入目录，可以看目录内文件元数据
写权限：可以创建或删除目录内文件，但需要x权限


rwx rw- 
111 100
421 400
7

000 
001
...
111

001 x 1
010 w 2
100 r 4


rwx rw- r-- file
chmod 764 file


恢复家目录

方法1
 mkdir -m 700 wang
 cp -a /etc/skel/.[^.]* wang/
 chown -R wang.wang wang/

方法2
 cp -r /etc/skel /home/mage
 chown -R mage:mage /home/mage
 chmod 700 /home/mage

umask000 + default666 = file666/dir777

目录:
umask + default = dir777
文件：
666-umask
观察结果：有奇数对位加1，偶数不变


1、当用户xiaoming对/testdir 目录无执行权限时，意味着无法做哪些操作？
2、当用户xiaoqiang对/testdir 目录无读权限时，意味着无法做哪些操作？
3、当用户wangcai 对/testdir 目录无写权限时，该目录下的只读文件file1是否可修改和删除？
4、当用户wangcai 对/testdir 目录有写和执行权限时，该目录下的只读文件file1是否可修改和删除？
5、复制/etc/fstab文件到/var/tmp下，设置文件所有者为wangcai读写权限，所属组为sysadmins组有读写权限，其他人无权限
6、误删除了用户wangcai的家目录，请重建并恢复该用户家目录及相应的权限属性
7、在/testdir/dir里创建的新文件自动属于g1组，组g2的成员如：alice能对这些新文件有读写权限，组g3的成员如：tom只能对新文件有读权限，其它用户（不属于g1,g2,g3）不能访问这个文件夹。
8、备份/testdir/dir里所有文件的ACL权限到/root/acl.txt中，清除/testdir/dir中所有ACL权限，最后还原ACL权限
9、查看access_log日志文件中，远程主机IP连接数排名前10名的IP


SUID
作用：作用在可执行二进制程序上，将当前运行者身份切换成该程序所有者的身份

owner1 suid /bin/app    owner2 /testdir/f1.txt

mage  /bin/cat 保留身份mage      /testdir/f1.txt
mage  /bin/app 切换身份 owner1    /testdir/f1.txt


SGID
作用：
1作用在可执行二进制程序上，将当前运行者身份切换成该程序所属组的身份
2对目录：目录内的新文件将自动继承目录的所属组

STICKY 
作用：只对目录有效，粘滞位


ifconfig eth0|head -n2 |tail -n1 |tr -s " " : |cut -d : -f4   centos6
ifconfig eth0|head -n2 |tail -n1 |cut -d: -f2 |cut -d" " -f1
ifconfig ens33|head -n2 |tail -n1 |tr -s ' ' |cut -d" " -f3 centos7.


cut -d: -f1,3 /etc/passwd |sort -t: -k 2 -nr

netstat -nt|tr -s " " :  |cut -d: -f6|sort | uniq -c

grep -o  "\<[0-9]\+\>" /etc/passwd |sort -n|uniq -c
grep -o "\<[[:alpha:]]*\>"  /etc/init.d/functions  |sort |uniq -c|sort -n
grep -o "\<[a-zA-Z]*\>"  /etc/init.d/functions  |sort |uniq -c|sort -n

grep -o "\<[a-zA-Z]\>"  /etc/init.d/functions |sort |uniq -c |sort -n

ifconfig|grep -o "\<[[:digit:]]\{1,3\}\.[[:digit:]]\{1,3\}\.[[:digit:]]\{1,3\}\.[[:digit:]]\{1,3\}\>"
ifconfig | grep -o "\<\([[:digit:]]\{1,3\}\.\)\{3\}[[:digit:]]\{1,3\}\>" 


练习

1
grep -i "^s" /proc/meminfo 
grep  "^[sS]" /proc/meminfo 

2
#grep -v "/bin/bash$" /etc/passwd
3
grep "^rpc\>" /etc/passwd |cut -d: -f7
grep -w "^rpc" /etc/passwd |cut -d: -f7 
4 
grep -o "\<[[:digit:]]\{2,3\}\>" /etc/passwd
5
grep "^[[:space:]]\+[^[:space:]]" /etc/grub2.cfg

6 
netstat -tna|grep "LISTEN[[:space:]]*$"

7
centos7
cut -d: -f1,3 /etc/passwd |grep "\<[[:digit:]]\{1,3\}$" 
centos6
cut -d: -f1,3 /etc/passwd |grep "\<[1-4]\?[[:digit:]]\{1,2\}$" 

8
grep "^\([[:alnum:]]\+\>\).*\<\1$" /etc/passwd 
grep "^\([[:alnum:]]\+\>\).*/\1$" /etc/passwd 

9
df |grep "^/dev/sd" | grep -o "\<[[:digit:]]\+%" |sort -nr|tr -d "%"


练习
1
grep -E "^(root|mage|wang)\>"  /etc/passwd|cut -d : -f3,7

2
grep -Eo "^[[:alnum:]_]+\(\)" /etc/rc.d/init.d/functions

3
echo /etc/rc.d/init.d|egrep -o "[^/]+/?$"

4
echo /etc/rc.d/init.d |egrep -o "^/.*/\<"
#echo /etc/sysconfig/network-scripts/ |egrep -o "^/.*/\<"

5
last|grep "^root\>" |grep -oE "([0-9]{1,3}\.){3}[0-9]{1,3}"


作业：
1、利用扩展正则表达式分别表示0-9、10-99、100-199、200-249、250-255
2、显示ifconfig命令结果中所有IPv4地址
#ifconfig |grep -Eo "\<(([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\>"

3、将此字符串：welcome to magedu linux 中的每个字符去重并排序，重复次数多的排到前面
echo "welcome to magedu linux" |grep -o "." |sort |uniq -c |sort -nr

判断centos主版本号
cat /etc/centos-release |grep -o " [0-9]"  
 
 
ls *.rpm |grep -o "[^.]*\.rpm\>"|cut -d. -f1 |sort|uniq -c 
ls *.rpm | rev|cut -d. -f2|rev |sort |uniq -c



作业：
1、复制/etc/profile至/tmp/目录，用查找替换命令删除/tmp/profile文件中的行首的空白字符
2、复制/etc/rc.d/init.d/functions文件至/tmp目录，用查找替换命令为/tmp/functions的每行开头为空白字符的行的行首添加一个#号
3、在vim中设置tab缩进为4个字符
4、复制/etc/rc.d/init.d/functions文件至/tmp目录，替换/tmp/functions文件中的/etc/sysconfig/init为/var/log
5、删除/tmp/functions文件中所有以#开头，且#后面至少有一个空白字符的行的行首的#号
6、编写脚本/root/bin/systeminfo.sh,显示当前主机系统信息，包括主机名，IPv4地址，操作系统版本，内核版本，CPU型号，内存大小，硬盘大小。
7、编写脚本/root/bin/backup.sh，可实现每日将/etc/目录备份到/root/etcYYYY-mm-dd中
8、编写脚本/root/bin/disk.sh,显示当前硬盘分区中空间利用率最大的值
9、编写脚本/root/bin/links.sh,显示正连接本主机的每个远程主机的IPv4地址和连接数，并按连接数从大到小排序



find /etc  \( -path '/etc/sane.d' -o -path '/etc/fonts' \) -a -prune -o -name "*.conf"


20170731

cmd1 && cmd2  短路与
cmd1为真，将执行cmd2，cmd2为真，结果为真，cmd2为假，结果为假
cmd1为假，将不执行cmd2 ，并且结果为假

cmd1 || cmd2  短路或

cmd1为真，将不执行cmd2，结果为真
cmd1为假，将执行cmd2 ，cmd2为真，结果为真，cmd2为假，结果为假

变量互换
a=10;b=6;a=$[a^b];b=$[a^b];a=$[a^b];echo a=$a;echo b=$b


ver=`cat /etc/centos-release  | grep -o " [0-9]"|cut -d" " -f2`

判断数字
[[ "$num" =~ ^-?[0-9]+$  ]] && echo $num is  number || echo $num is not number 


[[ "$file" =~ ^.*\.sh$  ]] && echo script  || echo  not script

作业：
1、编写脚本/root/bin/sumspace.sh，传递两个文件路径作为参数给脚本，计算这两个文件中所有空白行之和
2、编写脚本/root/bin/sumfile.sh,统计/etc, /var, /usr目录中共有多少个一级子目录和文件
3、编写脚本/bin/per.sh,判断当前用户对指定的参数文件，是否不可读并且不可写
4、编写脚本/root/bin/excute.sh ，判断参数文件是否为sh后缀的普通文件，如果是，添加所有人可执行权限，否则提示用户非脚本文件
5、编写脚本/root/bin/nologin.sh和login.sh,实现禁止和充许普通用户登录系统
6、查找/var目录下属主为root，且属组为mail的所有文件
7、查找/var目录下不属于root、lp、gdm的所有文件
8、查找/var目录下最近一周内其内容修改过，同时属主不为root，也不是postfix的文件
9、查找当前系统上没有属主或属组，且最近一个周内曾被访问过的文件
10、查找/etc目录下大于1M且类型为普通文件的所有文件
11、查找/etc目录下所有用户都没有写权限的文件
12、查找/etc目录下至少有一类用户没有执行权限的文件
13、查找/etc/init.d目录下，所有用户都有执行权限，且其它用户有写权限的文件


20170802

rpm -ivh 文件名
rpm -e  包名
rpm -qa "*包名*"
rpm -ql 包名
rpm -qi 包名
rpm -qp 文件名
rpm -K 
rpm --import 
yum install remove list repolist grouplist groupinstall groupremove reinstall history clean all
yum -y -q 


作业：
1 安装新内核 ，两个内核 
2 rm -f /bin/rpm ,安装Rpm包恢复之 或 cpio 
3 /usr/bin/java 找出来自哪个包
4 删除/lib64/libc/lib64/libc.so.6 恢复之
5 reset.sh 


20170804

实验：实现多系统网络yum服务器


准备：
1 关闭防火墙
centos7
systemctl disable firewalld.service 
systemctl stop firewalld.service 
centos6 
chkconfig iptables off
service iptables stop

iptables -vnL

2 关闭SElinux
vim /etc/selinux/config
SELINUX=permissive

setenforce  0
getenforce 验证是否生效


1 安装ftp服务包
rpm -ivh /run/media/root/CentOS\ 7\ x86_64/Packages/vsftpd-3.0.2-21.el7.x86_64.rpm 

rpm -ql vsftpd
/usr/lib/systemd/system/vsftpd.service
/var/ftp
systemctl start vsftpd 启动ftp服务
ss -tnl  21端口打开
systemctl enable vsftpd 设为开机自动启动ftp服务

2 准备yum 系统安装包

mkdir -p /var/ftp/pub/centos/{6,7} 
cp -r /run/media/root/CentOS\ 7\ x86_64/* /var/ftp/pub/centos/7
cp -r /run/media/root/CentOS\ 6\ x86_64/* /var/ftp/pub/centos/6

3 客户端配置

vim /etc/yum.repos.d/base.repo 
[base]
name=centos 
#baseurl=file:///misc/cd/
baseurl=ftp://192.168.25.129/pub/centos/$releasever/
gpgkey=ftp://192.168.25.129/pub/centos/$releasever/RPM-GPG-KEY-CentOS-$releasever


实验:第三方的yum仓库

1 安装http服务包

yum -y install httpd
rpm -ql httpd
/usr/lib/systemd/system/httpd.service
/var/www/html
systemctl start httpd 启动httpd服务
ss -tnl  80端口打开
systemctl enable httpd 设为开机自动启动 httpd服务

vim /var/www/html/index.html 
<h1>welcome to Magedu.com </h1> 

2 准备第三方rpm相关包
mkdir /var/www/html/app
cp *.rpm /var/www/html/app
createrepo  /var/www/html/app

3客户端
[app]
name=app
baseurl=http://192.168.25.129/app
gpgcheck=0

实验:在centos6.9源码编译httpd-2.2.34

0 准备工作
查看相同的软件
rpm -qa "httpd*"
yum remove httpd

1 安装开发包组
yum groupinstall "Development tools"

2 下载源码并解包
tar xvf httpd-2.2.34.tar.bz2
cd /usr/local/src/httpd-2.2.34


3 看说明 
cat README
cat INSTALL 

4 生成Makefile
cd /usr/local/src/httpd-2.2.34
./configure --help
./configure --prefix=/app/httpd22/ --sysconfdir=/etc/httpd22/ --enable-ssl
echo $?
yum install openssl-devel
./configure --prefix=/app/httpd22/ --sysconfdir=/etc/httpd22/ --enable-ssl

5
make && make install 

6 软件配置
准备环境变量
echo 'export PATH=/app/httpd22/bin:$PATH' > /etc/profile.d/httpd22.sh  
. /etc/profile.d/httpd22.sh
echo $PATH
apachctl start
ss -ntl 80端口打开即成功
http://本机IP/
vim /app/httpd22/htdocs/index.html

vim /etc/man.config 
加一行
MANPATH /app/httpd22/man


作业：
1、查询命令java来自于哪个rpm包
2、yum的配置和使用,包括yum仓库的创建
3、编写系统初始化脚本reset.sh，包括别名，提示符颜色，yum仓库配置文件,安装tree,ftp,lftp,telnet等包
4、在CentOS6上编译安装apache 2.2源码包,并启动此服务
5、在CentOS7上编译安装apache 2.4源码包,并启动此服务
6、tar xvf httpd.tar.bz2 && cd httpd && ./install.sh 实现一键安装httpd


实验：备份MBR和恢复

20170807

blkid -U `grep /app /etc/fstab  |cut -d" " -f1|cut -d"=" -f2`  

作业：
1、创建一个2G的文件系统，块大小为2048byte，预留1%可用空间,文件系统ext4，卷标为TEST，要求此分区开机后自动挂载至/test目录，且默认有acl挂载选项
2、写一个脚本，完成如下功能：
(1) 列出当前系统识别到的所有磁盘设备
(2) 如磁盘数量为1，则显示其空间使用信息
否则，则显示最后一个磁盘上的空间使用信息
3、完善reset.sh脚本



实验：创建和删除swap分区
1 分区
MBR 82 GPT 8200
dd if=/dev/zero of=/swapfile  bs=1M count=2048
2 
mkswap /dev/sdb1

3
vim /etc/fstab
UUID=xxx   swap swap defaults,pri=N   0 0

4
swapon -a 

5删除
禁用
swapoff /dev/sdb1

6 vim /etc/fstab
删除对应行

7
fdisk /dev/sdb 删除对应分区

https://wiki.centos.org/TipsAndTricks/CDtoDVDMedia

取IP
centos6
ifconfig eth0|sed  -n -e '2s/^.*r://' -e '2s/ .*$//p' 
centos7
ifconfig ens33 |sed  -n  -e '2s/.*et //' -e '2s/ .*$//p' 
ifconfig ens33 |sed  '2!d;s/.*et //;s/ .*$//'

修改httpd.conf配置文件
sed   -e '/^#<VirtualHost/,/^#<\/VirtualHost>/s@#@@'  -e '/^#NameVirtualHost/s@#@@' /etc/httpd/conf/httpd.conf

练习
1 sed  -r 's/^[[:space:]]+//' /etc/grub2.cfg
2 sed -r 's/^#[[:space:]]+//' /etc/fstab 
3 sed 's/^/#/' install.log 
4 bug
echo  /etc/sysconfig/network-scripts/ |sed 's@/.*/\<@@'
echo  /etc/sysconfig/-network-scripts/ |sed -r 's@^(.*/)([^/]+/?)@\1@'  取目录
echo  /etc/sysconfig/-network-scripts/ |sed -r 's@^(.*/)([^/]+/?)@\2@'	取基名

迁移home到独立分区

1 新建分区
2 创建文件系统
3 临时挂载到临时目录 /mnt/home
cp　-a /home/*  /mnt/home
4 init 1
5 vim /etc/fstab
UUID=xxx   /home    ext4 defaults 0 0

6 备份home
rm -rf /home/*

7 mount -a 
8 init 3 


实现配额

1 vi /etc/fstab
UUID=xxx  /home  ext4 usrquota,grpquota 0 0
mount -o remount /home
2 quotacheck -cug /home
/home/两个数据库文件
3
setenforce 0
quotaon  /home
4
edquota  wang
/dev/sda6   xxxx  80000 100000  

5 测试
su - wang
dd 



作业：
12
练习
1、统计centos安装光盘中Package目录下的所有rpm文件的以.分隔倒数第二个字段的重复次数
2、统计/etc/init.d/functions文件中每个单词的出现次数，并排序（用grep和sed两种方法分别实现）
sed  's/[^[:alpha:]]/\n/g' /etc/init.d/functions |sed '/^$/d' |sort |uniq -c|sort -n
grep -oE '[[:alpha:]]+' /etc/init.d/functions |sort |uniq -c |sort -n

3、将文本文件的n和n+1行合并为一行，n为奇数行




实验：逻辑卷实现

1 pvcreate /dev/sda6 /dev/sdb
2 vgcreate vg0 /dev/sd{a6,b}
3 lvcreate -n lv0  -L 50%vg vg0
lvceate -n lv1  -L 10G vg0
4 mkfs.ext4 /dev/vg0/lv0
5 mount

实验：扩展

1 vgdisplay 空间有空余
无空间
pvcreate  /dev/sda7
vgextend vg0 /dev/sda7
2 lvextend -L 15G  /dev/vg0/lv0
3 xfs_growfs /mnt/lv0
resize2fs /dev/vg0/lv1  15G

或者2，3合并为
lvextend -r -L +500M /dev/vg0/lv1

实验：缩减逻辑卷

1 umount /mnt/lv1
2 fsck -f /dev/vg0/lv1
3 resize2fs  /dev/vg0/lv1 5G
4 lvreduce -L 5G /dev/vg0/lv1
5 mount -a


实验：迁移逻辑卷

1 umount /mnt/lv0
2 vgrename vg0 newvg0
3 lvrename /dev/newvg0/lv0 newlv0
4 vgchange -an newvg0
5 vgexport newvg0
6 拆除硬盘
插入目标主机中
7 pvscan 
8 vgimport  newvg0
9 vgchange -ay newvg0
10 mkdir /mnt/newlv0；mount /dev/newvg0/newlv0  /mnt/newlv0

实验：从逻辑卷中删除正在使用的物理分区（硬盘）

1 vgdisplay;pvdisplay
2 pvmove /dev/sda6
3 vgreduce  vg0 /dev/sda6
4 pvremove /dev/sda6


VPN
LANMP PHP PERL 
PYTHON GO


正常时间/总体时间(正常时间+异常时间)

99.9999%

BNC

OSI 开放系统互联
ISO  
IOS 

application
presention
session
transport
network
data link
physical



802.11a,b,g,n,ac1G,ad 4.6G

ppp pppoe



作业：
1：创建一个可用空间为1G的RAID1设备，文件系统为ext4，有一个空闲盘，开机可自动挂载至/backup目录
2：创建由三块硬盘组成的可用空间为2G的RAID5设备，要求其chunk大小为256k，文件系统为ext4，开机可自动挂载至/mydata目录
3、创建一个至少有两个PV组成的大小为20G的名为testvg的VG；要求PE大小为16MB, 而后在卷组中创建大小为5G的逻辑卷testlv；挂载至/users目录
4、新建用户archlinux，要求其家目录为/users/archlinux，而后su切换至archlinux用户，复制/etc/pam.d目录至自己的家目录
5、扩展testlv至7G，要求archlinux用户的文件不能丢失
6、收缩testlv至3G，要求archlinux用户的文件不能丢失
7、对testlv创建快照，并尝试基于快照备份数据，验正快照的功能

frame 
packet 
segment 

unicast
multicast
broadcast

ip ipx
accton

单工 
半双工
全双工


windows mobile CE

wifi
wapi
2003-

马斯洛



社交
安全
生理
wifi
power

8
+6+6+2+(46-1500)+

4

72-1526

half close

-X->
<--

tcp
0-65535


22 ssh
443 https
3389 remote desktop
1521 oracle
3306 mysql
1433 sql server
25 smtp
110 pop
 

IGMP

Dos

00000000
00000001


11111111

00000000 0
00000001 1
00000010 2
00000100 4
00001000 8
00010000 16
00100000 32
01000000 64
10000000 128

10101110
128+32+14=174

174-128=46-32=14-8

10101110

0-255

192.168.25.107

A：
0.0.0.0 未知地址 本机所有地址
127 
1-126.A.B.C
网络数126个，一个网络主机数2^24-2=16777214

前8位网络ID，后24位主机ID
0XXXXXXX.A.B.C
00000000.A.B.C 
01111111.A.B.C 

6.A.B.C


6.0.0.0 网络ID
6.255.255.255 6这网络广播

B:
前16位网络ID，后16位主机ID
10XXXXXX.XXXXXXXX.B.C
10000000 128
10111111 191
128-191.A.B.C

网络数：2^14=16384
一个B网络主机数：2^16-2=65534



C:
前24位网络ID，后8位主机ID
110XXXXX.XXXXXXXX.XXXXXXXX.C
11000000 192
11011111 223
192-223.A.B.C
C网络数：2^21=2097152
一个C网络主机数：2^8-2=254

D:多播
1110XXXX.A.B.C
11100000 224
11101111 239
224-239.A.B.C

E
11110XXX.A.B.C
240-254 

rpm -q vim &>/dev/null || yum install vim -y &> /dev/null

CIDR 无类域间路由，网络ID和主机ID不是固定不变


子网掩码：32位二进制，对应网络ID位为1，对应主机ID为0

00000000 0
10000000 128
11000000 192
11100000 224
11110000 240
11111000 248
11111100 252
11111110 254
11111111 255



172.16.0.0/16
172.16.0.0/12




172.00010000
   .11110000
   .16


公式：
1 计算网络中主机最大数量：２＾主机ＩＤ位－２=2^(32-网络ID位数)-2
2 网络数=2^可变网络ID位
3 网络ID：IP与网络子网掩码
4 CIDR表示法=IP或网络ID/网络ID位数

172.16.100.200
255.255.224.0

220.199.211.100
255.255.248.0

114.203.188.10/20

1 子网掩码：255.255.240.0
2 网络ID:114.203.176.0/20

114.203.188.10
255.255.240.0
114.203.176.0

1011xxxx
11110000

3 网络主机数有多少:2^12-2=4094


201.130.199.100/24 A
201.130.188.100/16 B




A 先判断是否是B和一个网络
在同一网络，ARP
不在同一网络，ARP，网关的MAC

网关入口MAC，MACA | IPA,IPB|
router
MACB,MACrouter出口| IPA,IPB


申请B类网络

160.200.X.Y/16 

划分子网：将一个大网络分割多个小网络，每个小网络主机ID变少，网络ID变多，原来的大网络网络ID位向主机ID位借位

160.200.00 000000.X 
160.200.0.0/18
160.200.01 000000.X
160.200.64.0/18
160.200.10 000000.X
160.200.128.0/18
160.200.11 000000.X
160.200.192.0/18



160.200.0 0000000.1 

160.200.0 0000000.00000001
160.200.0 1111111.11111110

160.200.0 1111111.11111111 
160.200.1 0000000.00000000


160.200.1 0000000.00000001
160.200.1 1111111.11111110

网络ID：160.200.0.0/17
160.200.1 1111111.254
网络ID：160.200.128.0/17





160.200.0.0/16 划分16个子网

16=2^4

对最大的子网划分32个子网

1 划分子网的netmask
255.255.240.0

2 每个子网主机数
2^(32-20)-2=4094

3 最小，最大的子网的网络ID
160.200.0.0/20
160.200.240.0/20

4 最大子网的IP范围
160.200.240.1/20-160.200.255.254/20


160.200.0000    0000.0
160.200.1111    0000.00000001 
160.200.1111    1111.11111110




公式：
1 计算网络中主机最大数量：２＾主机ＩＤ位－２=2^(32-网络ID位数)-2
2 网络数=2^可变网络ID位
3 网络ID：IP与网络子网掩码
4 CIDR表示法=IP或网络ID/网络ID位数
5 划分子网的数量=2^网络ID向主机ID借的位数
6 划分子网后损失的IP数：（划分子网的数量-1）*2

160.200.0.0/16 划分16个子网
对最大的子网160.200.240.0/20划分32个子网
1 划分子网的netmask
255.255.255.128
2 每个子网主机数
2^(32-25)-2=126
3 最小，最大的子网的网络ID
160.200.240.0/25
160.200.255.128/25
4 最大子网的IP范围
160.200.255.129/25--160.200.255.254/25

160.200.1111 0000.0 0000000
  
160.200.1111 1111.1 0000001   160.200.255.129/25--160.200.255.254/25
160.200.1111 1111.1 1111110   


划分子网：将一个大网络分割多个小网络，每个小网络主机ID变少，网络ID变多，原来的大网络网络ID位向主机ID位借位
划分超网：将多个小网络合并一个大网络，每个网络主机ID变小，主机ID变多，原来的主机ID位向网络ID位借位


路由表：由多条路由记录组成
每条记录组成：

目标网络ID  netmask：目标网络的标识
接口：到达目标网络本设备的出口
网关：目标网络和当前路由器直连，网关IP=接口IP，不直接，网关IP=下一个路由器的邻近的接口的IP

DHCP DISCOVER
DHCP OFFER
DHCP REQUEST
DHCP ACK(ip,mask，gateway,release)



IP管理
32
Netmask 
CIDR:IP/网络ID位数

公式
1 主机数=2^主机ID位数-2
2 子网数=2^可变网络ID位
划分子网：一个大网划分多个小网，网络ID向主机ID借位N，划分2^N个子网
3 划分子网后损失IP数：2*（2^N-1）


路由表每一条路由组成
网络ID/netmask 接口 网关


R1
route add -net 192.168.3.0/24 gw 192.168.2.2
route add -net 192.168.4.0/24 gw 192.168.2.2


route add -net 0.0.0.0/0 gw 192.168.2.2


R2
route add -net 192.168.1.0/24 gw 192.168.2.1
route add -net 192.168.4.0/24 gw 192.168.3.1

R3
route add -net 192.168.1.0/24 gw 192.168.3.2
route add -net 192.168.2.0/24 gw 192.168.3.2
route add default gw 192.168.3.2


172.18.N.6
172.18.N.7


/etc/sysconfig/network-scripts/ifcfg-eth0
DEVICE=eth0
HWADDR=XXXXXXXX
BOOTPROTO=static dhcp
IPADDR=1.1.1.1
NETMASK=255.255.0.0
PREFIX=16
GATEWAY=1.1.1.254
DNS1=
DNS2
DNS3

/etc/sysconfig/network
/etc/hostname
/boot/grub2/grub.cfg
linux16 net.ifnames=0


/etc/resolv.conf


ip a ifconfig 
route -n ip route netstat -r

ifcfg-eth0:1
DEVICE=eth0:1

ip addr add 2.2.2.2/24 dev eth0 label eth0:1
ifconfig eth0:2 33.3.3.3/24 

scanip.sh 
#!/bin/bash
netid=172.18.253
> /root/ip.log
for id in {1..254};do
        {
        if ping -c1 -W1 $netid.$id &> /dev/null ;then
                echo $netid.$id |tee -a /root/ip.log
        fi
        }&
done
wait


作业：
1、每天的2点和12点整，将/etc备份至/testdir/backup目录中，保存的文件名称格式为“etcbak-yyyy-mm-dd-HH.tar.xz”
2、每周2, 4, 7备份/var/log/messages文件至/logs目录中，文件名形如“messages-yyyymmdd”
3、每两小时取出当前系统/proc/meminfo文件中以S或M开头的信息追加至/tmp/meminfo.txt文件中
4、工作日时间，每10分钟执行一次磁盘空间检查，一旦发现任何分区利用率高于80%，就执行wall警报
5、编写脚本/root/bin/createuser.sh，实现如下功能：使用一个用户名做为参数，如果指定参数的用户存在，就显示其存在，否则添加之；显示添加的用户的id号等信息
6、编写脚本/root/bin/yesorno.sh，提示用户输入yes或no,并判断用户输入的是yes还是no,或是其它信息
7、编写脚本/root/bin/filetype.sh,判断用户输入文件路径，显示其文件类型（普通，目录，链接，其它文件类型）
8、编写脚本/root/bin/checkint.sh,判断用户输入的参数是否为正整数
12
用for实现
1、判断/var/目录下所有文件的类型
2、添加10个用户user1-user10，密码为8位随机字符
3、/etc/rc.d/rc3.d目录下分别有多个以K开头和以S开头的文件；分别读取每个文件，以K开头的输出为文件加stop，以S开头的输出为文件名加start，如K34filename stop S66filename start
4、编写脚本，提示输入正整数n的值，计算1+2+…+n的总和
5、计算100以内所有能被3整除的整数之和

seq -s +  3 3 100  |bc
echo {3..100..3}|tr ' ' +|bc
vim test1.sh 
#!/bin/bash
sum=0
for i in {1..100};do
        if [ $[i%3] -eq 0 ];then  
                let sum+=i
        fi
done
echo sum is $sum

vim test1.sh 
#!/bin/bash
sum=0
for i in {1..100};do
        if [ $((i%3)) -eq 0 ];then
                let sum+=i
        fi
done
echo sum is $sum
 
6、编写脚本，提示请输入网络地址，如192.168.0.0，判断输入的网段中主机在线状态
7、打印九九乘法表

./9x9.sh
#!/bin/bash

for i in {1..9}
do
        for j in `seq $i`
        do
                echo -en "$j*$i=$((i*j))\t"

        done
        echo
done


8、在/testdir目录下创建10个html文件,文件名格式为数字N（从1到10）加随机8个字母，如：1AbCdeFgH.html
9、打印等腰三角形


vim sum.sh

#!/bin/bash
sum=0
i=1
while [ $i -le 100 ];do
        let sum+=i
        let i++
done
echo sum=$sum



作业：用while实现
1、编写脚本，求100以内所有正奇数之和
2、编写脚本，提示请输入网络地址，如192.168.0.0，判断输入的网段中主机在线状态，并统计在线和离线主机各多少
3、编写脚本，打印九九乘法表
4、编写脚本，利用变量RANDOM生成10个随机数字，输出这个10数字，并显示其中的最大值和最小值
5、编写脚本，实现打印国际象棋棋盘
6、后续六个字符串：efbaf275cd、4be9c40b8b、44b2395c46、f8c8873ce0、b902c16c8b、ad865d2f63是通过对随机数变量RANDOM随机执行命令：
echo $RANDOM|md5sum|cut –c1-10
后的结果，请破解这些字符串对应的RANDOM值

用until实现
1、每隔3秒钟到系统上获取已经登录的用户的信息；如果发现用户hacker登录，则将登录时间和主机记录于日志/var/log/login.log中,并退出脚本
2、随机生成10以内的数字，实现猜字游戏，提示比较大或小，相等则退出
3、用文件名做为参数，统计所有参数文件的总行数
4、用二个以上的数字为参数，显示其中的最大值和最小值


while [ $# -gt 0 ] # or (( $# > 0 ))
do
        id $1 &>/dev/null && continue
        useradd $1 && echo $1 is created
        shift
done



for varname in 列表 ;do


done

for ((cmd1;cmd2;cmd3));do
	cmdN
done


cmd1
while cmd2;do
	cmdN
	cmd3
done

until cmd;do
	cmdN
done


while read varname ;do
	cmdN
done < /path/file

cmd | while read varname ;do
	cmdN
done 

continue
break 

select varname in list ;do

	case $varname in
	xxx)
		cmd1;
		;;
	yyy)
		cmd2
		break 
		;;
	*)
		cmdN
		;;
	esac

done

PS3
REPLY

trap 'cmd' 2




fact() { 
[$1 -eq 0 -o $1 -eq 1 ] && echo 1 || echo $[$1*`fact $[$1-1]`]
}



作业：
?编写函数，实现OS的版本判断
?编写函数，实现取出当前系统eth0的IP地址
?编写函数，实现打印绿色OK和红色FAILED
?编写函数，实现判断是否无位置参数，如无参数，提示错误


?编写服务脚本/root/bin/testsrv.sh，完成如下要求
(1) 脚本可接受参数：start, stop, restart, status
(2) 如果参数非此四者之一，提示使用格式后报错退出
(3) 如是start:则创建/var/lock/subsys/SCRIPT_NAME, 并显示“启动成功”
考虑：如果事先已经启动过一次，该如何处理？
(4) 如是stop:则删除/var/lock/subsys/SCRIPT_NAME, 并显示“停止完成”
考虑：如果事先已然停止过了，该如何处理？
(5) 如是restart，则先stop, 再start
考虑：如果本来没有start，如何处理？
(6) 如是status, 则如果/var/lock/subsys/SCRIPT_NAME文件存在，则显示“SCRIPT_NAMEis running...”
如果/var/lock/subsys/SCRIPT_NAME文件不存在，则显示“SCRIPT_NAME is stopped...”
其中：SCRIPT_NAME为当前脚本名


?编写脚本/root/bin/copycmd.sh
(1) 提示用户输入一个可执行命令名称
(2) 获取此命令所依赖到的所有库文件列表
(3) 复制命令至某目标目录(例如/mnt/sysroot)下的对应路径下；如：/bin/bash ==> /mnt/sysroot/bin/bash
/usr/bin/passwd==> /mnt/sysroot/usr/bin/passwd
(4) 复制此命令依赖到的所有库文件至目标目录下的对应路径下：如：/lib64/ld-linux-x86-64.so.2 ==> /mnt/sysroot/lib64/ld-linux-x86-64.so.2
(5)每次复制完成一个命令后，不要退出，而是提示用户键入新的要复制的命令，并重复完成上述功能；直到用户输入quit退出

作业：
1、输入若干个数值存入数组中，采用冒泡算法进行升序或降序排序
2、将下图所示，实现转置矩阵matrix.sh
1 2 3 		1 4 7
4 5 6 ===>  2 5 8
7 8 9 		3 6 9



实验：逻辑卷环境下，删除/etc/fstab  /boot

1 rescue mode
blkid 
fdisk -l 
lvscan 
vgchange -ay  激活逻辑卷

2
mkdir /mnt/tmp
mount /dev/vg0/root     /mnt/tmp
vim /mnt/tmp/etc/fatab
/dev/sda1  /boot ext4 defaults 0 0 
/dev/vg0/root  /  ext4 defaults 0 0 
/dev/vg0/home   /home  ext4 defaults  0 0 
/dev/vg0/swap swap swap  defaults 0 0 

exit
exit

3 rescue mode
chroot /mnt/sysimage 
mount /dev/sr0 /mnt
rpm -ivh /mnt/Packages/kernel.xxx.rpm --force

ls /boot

4
grub-install /dev/sda

5
vim /boot/grub/grub.conf

default=0
timeount=5
title linux
kernel /vmlinuz... root=/dev/vg0/root
initrd  /initramfs.xxx.img

exit
exit




作业
?1、破解root口令，并为grub设置保护功能
?2、破坏本机grub stage1，而后在救援模式下修复之
?3、删除vmlinuz和initramfs文件后无法启动,两种方法恢复之
?4、增加新硬盘，在其上制作能单独运行kernel和bash的系统
?5、在U盘上定制linux，使其可启动系统，并具有网络功能
?6、删除/etc/fstab和/boot目录的所有文件，并恢复之
?7、编译安装kernel，启用支持ntfs文件系统功能


?1、启用SELinux策略并安装httpd服务，改变网站的默认主目录为/website,添加SELinux文件标签规则，使网站可访问
?2、修改上述网站的http端口为9527，增加SELinux端口标签，使网站可访问
?3、启用相关的SELinux布尔值，使上述网站的用户student的家目录可通过http访问
?4、编写脚本selinux.sh，实现开启或禁用SELinux功能

df |awk -F% '$0 ~ "^/dev/sd"{print $1}'| awk  '$NF >=10{print $1,$NF}' 
echo {1..10}|awk '{i=1;while(i<=NF){ if($i%2==0)print $i,"oushu";else print $i,"jishu";i++}}'


[root@centos7 ~]#cat f1.txt
1 2 3 4 5 6 7 8 9 10
10 11 12 13 14 15 16 17 18 19 20
[root@centos7 ~]#awk '{i=1;sum=0;while(i<=NF){sum+=i;i++};print sum,i}' f1.txt         
55 11
66 12
[root@centos7 ~]#awk '{i=1;sum=0;while(i<=NF){sum+=$i;i++};print sum,i}' f1.txt  
55 11
165 12
[root@centos7 ~]#awk '{i=1;while(i<=NF){sum+=$i;i++}}END{print sum}' f1.txt                    
220
[root@centos7 ~]#awk '{i=1;while(i<=NF){sum+=$i;i++};print sum,i}END{print sum}' f1.txt 
55 11
220 12
220


time for ((sum=0,i=1;i<=1000000;i++));do let sum+=i;done;echo $sum
time awk 'BEGIN{sum=0;i=1;while(i<=1000000){sum+=i;i++};print sum}' 
time seq -s "+" 1 1000000 |bc

awk -F : '{sum+=$3}END{print sum}' /etc/passwd 
awk -F: '{if(NR%2!=0) next; print NR,$1,$3}' /etc/passwd 

awk -F : '{line[$7]++}END{for(i in line){ print i,line[i]}}' /etc/passwd

awk '/^tcp/{line[$NF]++}END{for(i in line){print i,line[i]}}' f1.log 

netstat -nt | awk -F '[[:space:]:]+'  '/^tcp/{ip[$6]++}END{for(i in ip){print i,ip[i]}}'
netstat -nt|awk -F '[[:space:]:]+'  '/^tcp/{ip[$6]++}END{for(i in ip){print i,ip[i]}}' |sort -nr -k2 |awk '{print $1}'|head -10

netstat -tn | awk '/^tcp\>/{split($5,ip,":");count[ip[1]]++}END{for(i in count){print i,count[i]}}'

awk '{total[$3]+=$2;num[$3]++}END{for(name in total){print name,total[name],total[name]/num[name]}}' score.txt  



[root@centos7 ~]#cat score.txt 
mage 100 male
wang 90 male
zhang 80 female
li    100 female

awk '{total[$3]+=$2;num[$3]++}END{for(name in total){print name,total[name],total[name]/num[name]}}' score.txt 
awk '{if($3 == "male"){msum+=$2;mnum++}else{fsum+=$2;fnum++}}END{printf "msum=%d  mavg=%.2f\nfsum=%d favg=%.2f\n",msum,msum/mnum,fsum,fsum/fnum}'  score.txt 

作业：
1、统计/etc/fstab文件中每个文件系统类型出现的次数
2、统计/etc/fstab文件中每个单词出现的次数
3、提取出字符串Yd$C@M05MB%9&Bdh7dq+YVixp3vpw中的所有数字
4、解决DOS攻击生产案例：根据web日志或者或者网络连接数，监控当某个IP并发连接数或者短时内PV达到100，即调用防火墙命令封掉对应的IP，监控频率每隔5分钟。防火墙命令为：iptables -A INPUT -s IP -j REJECT


作业：
1为编译安装的httpd服务，实现service unit文件
2破解centos7 口令
3修改默认的启动内核为新编译内核
4启动时临时禁用SELinux
5启动时进入emergency模式
6卸载编译安装的新内核
7删除/boot  恢复之




LOVEZ

ORYHC

算法公开 Key

https ssl

A--->B,C,D,E


data---加密key1(data)=data'----> 解密key2(data')=data

key1=key2  对称
1 key 传输困难 
2 

key1！=key2  非对称

A-->B

A Pa,Sa  
B Pb,Sb

数据加密，隐私
data---加密Pb(data)=data'----> 解密Sb(data')=data

数据来源确认,数字签名
data---加密Sa(data)=data'----> 解密Pa(data')=data

1G des 2G 4m 8m
1G rsa 1G 1m 64小时


hash
哈希 散列

hash(data)=digest 摘要

74391081d998963b21483113143eb172e7a4e5f4

王小云


Pb[Sa(data)]

Pb{data+Sa[hash(data)]}

key{data+Sa[hash(data)]}+Pb(key)

https
data+Sa[hash(data)]
[hash(data)=hash(data)


[ CA_default ]

dir             = /etc/pki/CA           # Where everything is kept
certs           = $dir/certs            # Where the issued certs are kept
crl_dir         = $dir/crl              # Where the issued crl are kept
database        = $dir/index.txt        # database index file.
#unique_subject = no                    # Set to 'no' to allow creation of
                                        # several ctificates with same subject.
new_certs_dir   = $dir/newcerts         # default place for new certs.

certificate     = $dir/cacert.pem       # The CA certificate
serial          = $dir/serial           # The current serial number
crlnumber       = $dir/crlnumber        # the current crl number
                                        # must be commented out to leave a V1 CRL
crl             = $dir/crl.pem          # The current CRL
private_key     = $dir/private/cakey.pem# The private key



复制本地文件到远程
pscp.pssh  -h ip.txt /app/f1.sh /app
复制远程文件到本地
pslurp  -h ip.txt -L /app /etc/passwd  pass

作业：
1 建立CA，申请证书，颁发证书
2 利用expect脚本， 实现自动化 将公钥复制到多个远程的主机
3 ssh端口转发
curl  --socks5 127.0.0.1  http://192.168.25.107   
awk '/Failed/{ip[$(NF-3)]++}END{for(i in ip){if(ip[i]>10){system("iptables -A INPUT -s "i" -j REJECT")}}}' /var/log/secure 
sshd_config

ssh -L 1080:smtpserver:25 sshserver

ssh -R 1080:smtpserver:25 sshserver
data--->localhost:1080--->localhost:sshclientport---->sshserver:22 ---->sshserver:smtpclientport ------> smtpserver:25
data--->sshserver:1080--->sshserver:sshserverport--ssh-->localhost:sshclientport ---->localhost:smtpclientport ---smtp---> smtpserver:25

作业
1、限制centos用户只能够在工作时间通过ssh远程连接本机
2、限制只有admins组内的用户可ssh到本机

mkisofs -R -J -T -v --no-emul-boot --boot-load-size 4 --boot-info-table -V "CentOS 6.9 x86_64 boot" -b isolinux/isolinux.bin -c isolinux/boot.cat -o /root/centos6_boot.iso /app/myiso/
/etc/pam.d/program
/lib64/security 

启动菜单ISO
boot:linux ks=http://websrv/ks.cfg
ks.cfg
url --url=ftp://websrv2/pub/

全功能ISO

1 准备相关文件，yum源
cp -r /misc/cd  /app/fulliso
rm -rf /app/fulliso/repodata/*
cp /misc/cd/repodata/43d8fd068164b0f042845474d6a22262798b9f0d1f49ad1bf9f95b953089777d-c6-x86_64-comps.xml /app/fulliso/repodata/
cd /app/fulliso
createrepo -g repodata/43d8fd068164b0f042845474d6a22262798b9f0d1f49ad1bf9f95b953089777d-c6-x86_64-comps.xml   ./

find /app/fulliso  -name TRANS.TBL -exec rm {} \;

2 准备ks文件
vim  /app/fulliso/ks6.cfg 
# Kickstart file automatically generated by anaconda.

#version=DEVEL
install
cdrom
lang en_US.UTF-8
keyboard us
network --onboot yes --device eth0 --bootproto dhcp --noipv6
rootpw  --iscrypted $6$hfb25YOYZDU3YZTl$VxTkHGGJGGBbr59OPnY5kTJzvJ9hb9NRwrh5FMHLIAlXh9VQ74PYoK7QzPWYN0zaJrm3mv/IP0fDkHxFglNi6/
firewall --service=ssh
authconfig --enableshadow --passalgo=sha512
 selinux --disabled
timezone Asia/Shanghai
bootloader --location=mbr --driveorder=sda --append="crashkernel=auto rhgb quiet"
# The following is the partition information you requested
# Note that any partitions you deleted are not expressed
# here so unless you clear all partitions first, this is
# not guaranteed to work
 zerombr
 clearpart --all
 text
 reboot
 part /boot --fstype=ext4 --size=1000
 part / --fstype=ext4 --size=50000
 part /app --fstype=ext4 --size=40000

 part swap --size=2048


 #repo --name="CentOS"  --baseurl=cdrom:sr0 --cost=100

%packages
@base
@core
@debugging
@basic-desktop
@desktop-debugging
@desktop-platform
@directory-client
@fonts
@general-desktop
@graphical-admin-tools
@input-methods
@internet-applications
@internet-browser
@java-platform
@kde-desktop
@legacy-x
@network-file-system-client
@office-suite
@print-client
@remote-desktop-clients
@server-platform
@server-policy
@workstation-policy
@x11
mtools
pax
python-dmidecode
oddjob
wodim
sgpio
genisoimage
device-mapper-persistent-data
abrt-gui
qt-mysql
samba-winbind
certmonger
pam_krb5
krb5-workstation
xterm
xorg-x11-xdm
libXmu
rdesktop
%end
 %post
 rm -rf /etc/yum.repos.d/*
 cat > /etc/yum.repos.d/base.repo <<eof
 [base]
 name=base
 baseurl=file:///misc/cd
 gpgcheck=0
 eof
 %end

3 定制启动菜单
vim  /app/fulliso/isolinux/isolinux.cfg
default vesamenu.c32
#prompt 1
timeout 600

display boot.msg

menu background splash.jpg
menu title Welcome to CentOS Automatic Install!
menu color border 0 #ffffffff #00000000
menu color sel 7 #ffffffff #ff000000
menu color title 0 #ffffffff #00000000
menu color tabmsg 0 #ffffffff #00000000
menu color unsel 0 #ffffffff #00000000
menu color hotsel 0 #ff000000 #ffffffff
menu color hotkey 7 #ffffffff #ff000000
menu color scrollbar 0 #ffffffff #00000000

label desktop
  menu label Install an ^desktop system
  kernel vmlinuz
  append initrd=initrd.img ks=cdrom:/ks6.cfg
label mini
  menu label Install an m^ini system
  kernel vmlinuz
  append initrd=initrd.img ks=cdrom:/ks6-mini.cfg
label manual
  menu label ^Manual install an  system
  kernel vmlinuz
  append initrd=initrd.img 
label local
  menu label Boot from ^local drive
  menu default
  localboot 0xffff

4 制作ISO文件
 mkisofs -R -J -T -v --no-emul-boot --boot-load-size 4 --boot-info-table -V "CentOS 6.9 autoinstall" -b isolinux/isolinux.bin -c isolinux/boot.cat -o /root/centos6.iso /app/fulliso/


 
tftp

ftp
ftp://192.168.25.107/
-->/var/ftp

pxe:
dhcp tftp  ks.cfg http/ftp/nfs/



实验：基于网络PXE自动化安装CentOS 7

1安装前准备：关闭防火墙和SELINUX，DHCP服务器静态IP

2安装相关软件包
yum install httpd tftp-server dhcp syslinux 

3 配置文件共享服务和yum源
systemctl enable httpd
systemctl start httpd
mkdir /var/www/html/centos/7
mount /dev/sr0 /var/www/html/centos/7

4 准备kickstart文件
cp /root/anaconda-ks.cfg   /var/www/html/ksdir/ks7-1.cfg
chmod +r /var/www/html/ksdir/ks7-1.cfg
vim /var/www/html/ksdir/ks7-1.cfg 
cat ks7-1.cfg 
#version=DEVEL
# System authorization information
auth --enableshadow --passalgo=sha512
# Use CDROM installation media
 url --url=http://192.168.25.107/centos/7
# Use graphical install
 text
# Run the Setup Agent on first boot
firstboot --enable
ignoredisk --only-use=sda
# Keyboard layouts
keyboard --vckeymap=us --xlayouts='us'
# System language
lang en_US.UTF-8

# Network information
 network  --bootproto=dhcp --device=ens33 --onboot=on --ipv6=auto --activate
network  --hostname=centos7.magedu.com

# Root password
rootpw --iscrypted $6$Z7LBEUpwj3iQdYZ3$olYQ.Lj1xV2VAGS1UiNflKF0oMGip3b6tU9QFcp0i2JBjwKlY/Yaexul57NHpIJc.Y2V1hWAOueaqwjuWDGMk0
# System services
services --disabled="chronyd"
# System timezone
timezone Asia/Shanghai --isUtc --nontp
user --name=wang --password=$6$v.VphW/puRblcrFB$uaSrdEhGAwMXap27WIKTn5lyOOfoFyB/SNxyyL3og6s9/VQoAKoL2KQjKmeYFmoYTuYkSNL7BBxgbJzeryKr9. --iscrypted --gecos="wang"
# X Window System configuration information
xconfig  --startxonboot
# System bootloader configuration
bootloader --append=" crashkernel=auto" --location=mbr --boot-drive=sda
# Partition clearing information
 zerombr
 clearpart --all
# Disk partitioning information
part swap --fstype="swap" --ondisk=sda --size=2048
part /app --fstype="xfs" --ondisk=sda --size=51200
part / --fstype="xfs" --ondisk=sda --size=51200
part /boot --fstype="xfs" --ondisk=sda --size=1024
 reboot
%packages
@^graphical-server-environment
@base
@core
@desktop-debugging
@dial-up
@fonts
@gnome-desktop
@guest-agents
@guest-desktop-agents
@hardware-monitoring
@input-methods
@internet-browser
@multimedia
@print-client
@x11
kexec-tools
 autofs
%end

%addon com_redhat_kdump --enable --reserve-mb='auto'

%end

%anaconda
pwpolicy root --minlen=6 --minquality=50 --notstrict --nochanges --notempty
pwpolicy user --minlen=6 --minquality=50 --notstrict --nochanges --notempty
pwpolicy luks --minlen=6 --minquality=50 --notstrict --nochanges --notempty
%end
%post
systemctl enable autofs
rm -rf /etc/yum.repos.d/*
cat > /etc/yum.repos.d/base.repo <<eof
[base]
name=base
baseurl=file:///misc/cd
gpgcheck=0
eof
%end

5配置tftp服务
systemctl enable tftp.socket
systemctl start tftp.socket

6配置DHCP服务
vim /etc/dhcp/dhcpd.conf
option domain-name "example.com";
default-lease-time 600;
max-lease-time 7200;
subnet 192.168.25.0 netmask 255.255.255.0 {
        range 192.168.25.50 192.168.25.100;
        next-server 192.168.25.107;
        filename "pxelinux.0";
}
systemctl enable dhcpd
systemctl start dhcpd


7 准备PXE相关文件
mkdir/var/lib/tftpboot/pxelinux.cfg/
cp/usr/share/syslinux/{pxelinux.0,menu.c32} /var/lib/tftpboot/
cp/misc/cd/isolinux/{vmlinuz,initrd.img} /var/lib/tftpboot/
cp/misc/cd/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default


8 制作启动菜单文件
vim /var/lib/tftpboot/pxelinux.cfg/default
default menu.c32
timeout 600

menu title CentOS Linux 7 PXE Install

label desktop
  menu label Auto Install CentOS Linux 7  ^Desktop
  kernel vmlinuz
  append initrd=initrd.img ks=http://192.168.25.107/ksdir/ks7-1.cfg

label mini
  menu label Auto Install CentOS Linux 7  M^ini
  kernel vmlinuz
  append initrd=initrd.img ks=http://192.168.25.107/ksdir/ks7-2.cfg

label manual
  menu label ^Manual Install CentOS Linux 7 
  kernel vmlinuz
  append initrd=initrd.img inst.repo=http://192.168.25.107/centos/7

label local
  menu default
  menu label Boot from ^local drive
  localboot 0xffff
menu end

文件列表如下：
/var/lib/tftpboot/
├──initrd.img
├──menu.c32
├──pxelinux.0
├──pxelinux.cfg
│└──default
└──vmlinuz



实验：在centos6实现PXE安装

准备：iptables 和selinux 关闭
1 安装包并启动服务
yum install vsftpd tftp-server dhcp syslinux-nonlinux
chkconfig dhcpd on
chkconfig tftp on
service xinetd restart
chkconfig vsftpd on
service vsftpd start
ss -nutlp

2 准备yum源
mkdir /var/ftp/pub/centos/6 -pv
mount /dev/sr0 /var/ftp/pub/centos/6

3 准备ks文件
cp /root/anaconda-ks.cfg /var/ftp/pub/ksdir/ks6-1.cfg
vim /var/ftp/pub/ksdir/ks6-1.cfg
yum install system-config-kickstart
ksvalidator  /var/ftp/pub/ksdir/ks6-1.cfg
chmod +r /var/ftp/pub/ksdir/*.cfg 

4 配置DHCP服务
cp /usr/share/doc/dhcp-4.1.1/dhcpd.conf.sample /etc/dhcp/dhcpd.conf 
vim /etc/dhcp/dhcpd.conf
subnet 192.168.25.0 netmask 255.255.255.0 {
        range 192.168.25.10 192.168.25.100;
        next-server 192.168.25.106;
        filename "pxelinux.0";
}
service dhcpd restart

5 准备PXE相关文件

cp /usr/share/syslinux/{pxelinux.0,menu.c32} /var/lib/tftpboot/
cp /misc/cd/images/pxeboot/{initrd.img,vmlinuz} /var/lib/tftpboot/
mkdir pxelinux.cfg
cp /misc/cd/isolinux/isolinux.cfg  /var/lib/tftpboot/pxelinux.cfg/default

6 准备启动菜单
vim /var/lib/tftpboot/pxelinux.cfg/default
default menu.c32
#prompt 1
timeout 600

menu title Welcome to CentOS 6.9!

label desktop
  menu label Automatic Install an ^desktop system
  kernel vmlinuz
  append initrd=initrd.img ks=ftp://192.168.25.106/pub/ksdir/ks6-1.cfg
label mini
  menu label Automatic Install a m^ini system
  kernel vmlinuz
  append initrd=initrd.img  ks=ftp://192.168.25.106/pub/ksdir/ks6-2.cfg
label manual
  menu label ^Manual Install a system
  kernel vmlinuz
  append initrd=initrd.img inst.repo=ftp://192.168.25.106/pub/centos/6
label local
  menu default
  menu label Boot from ^local drive
  localboot 0xffff


实验：在centos7实现PXE安装centos6和centos7

1 分别准备centos7和centos6的yum 源
/var/www.html/centos/{6,7}
mount /dev/sr1 /var/www/html/centos/6

2 准备centos6,7的各个ks文件
ls /var/www/html/ksdir
ks6-1.cfg  ks7-1.cfg  ks7-2.cfg

3 准备centos6,7的各自的内核文件
mkdir /var/lib/tftpboot/centos{6,7}
cp /var/www/html/centos/7/isolinux/{initrd.img,vmlinuz}  /var/lib/tftpboot/centos7
cp /var/www/html/centos/6/isolinux/{initrd.img,vmlinuz}  /var/lib/tftpboot/centos6

tree /var/lib/tftpboot
/var/lib/tftpboot
├── centos6
│?? ├── initrd.img
│?? └── vmlinuz
├── centos7
│?? ├── initrd.img
│?? └── vmlinuz
├── menu.c32
├── pxelinux.0
└── pxelinux.cfg
    └── default


4 准备启动菜单

vim /var/lib/tftpboot/pxelinux.cfg/default
default menu.c32
timeout 600
menu title CentOS Linux  PXE Install

label centos7
  menu label Auto Install CentOS Linux ^7
  kernel centos7/vmlinuz
  append initrd=centos7/initrd.img ks=http://192.168.25.107/ksdir/ks7-1.cfg

label centos6
  menu label Auto Install CentOS Linux ^6
  kernel centos6/vmlinuz
  append initrd=centos6/initrd.img ks=http://192.168.25.107/ksdir/ks6-1.cfg

label manual7
  menu label ^Manual Install CentOS Linux 7
  kernel centos7/vmlinuz
  append initrd=centos7/initrd.img inst.repo=http://192.168.25.107/centos/7

label manual6
  menu label Manual ^Install CentOS Linux 6
  kernel centos6/vmlinuz
  append initrd=centos6/initrd.img inst.repo=http://192.168.25.107/centos/6
label local
  menu default
  menu label Boot from ^local drive
  localboot 0xffff
menu end



实验：cobbler安装系统

1 安装包
yum install cobbler  dhcp
systemctl  enable  cobblerd
systemctl  start  cobblerd
systemctl enable  tftp
systemctl start  tftp
systemctl enable httpd
systemctl start httpd


2 根据cobbler check 提示
1) vim /etc/cobbler/settings 
default_password_crypted: "$1$8ckh4FrM$ayLsgQi85bi8Nt5Gj4Drj/"    #openssl passwd -1 生成口令
next_server: 192.168.25.107
manage_dhcp: 1  
server: 192.168.25.107

systemctl restart cobblerd
cobbler sync

2）生成dhcp模版文件
vi /etc/cobbler/dhcp.template
subnet 192.168.25.0 netmask 255.255.255.0 {
   range dynamic-bootp        192.168.25.100 192.168.25.254;

cobbler sync 

3) 准备启动文件和和菜单风格文件
连接internet
cobbler get-loaders
不连internet
cp /var/lib/tftpboot/{menu.c32,pxelinux.0} /var/lib/cobbler/loaders

3 导入yum源
cobbler import --path=/misc/cd --name=centos7.3 --arch=x86_64
cobbler import --path=/misc/cd --name=centos6.9 --arch=x86_64
cobbler distro list
cobbler profile list
4 生成ks
cp  centos6.cfg centos7.cfg /var/lib/cobbler/kickstarts/
cobbler profile remove --name=centos6.9-x86_64
cobbler profile remove --name=centos7.3-x86_64
cobbler profile add --name=centos6.9_desktop --distro=centos6.9-x86_64  --kickstart=/var/lib/cobbler/kickstarts/centos6.cfg
cobbler profile add --name=centos7.3_mini --distro=centos7.3-x86_64  --kickstart=/var/lib/cobbler/kickstarts/centos7.cfg
cobbler sync


https://cobblerserver/cobbler_web


名字解析服务
名字--->IP

ping www.qq.com


实验：正向解析数据库magedu.com

1 yum install bind
systemctl start named
systemctl enable named

2 vim /etc/named.conf
 listen-on port 53 { localhost; };
  allow-query     { any; };

3 vim /etc/named.rfc1912.zones
zone "magedu.com" IN {
        type master;
        file "magedu.com.zone";
};

4 vim /var/named/magedu.com.zone
$TTL 1D
@  2D   IN SOA  dns1  admin.magedu.com.   (
                                        2017091901      ; serial
                                        86400   ; refresh
                                        1H      ; retry
                                        1W      ; expire
                                        3H )    ; minimum
        NS      dns1
dns1 2D A       192.168.25.107
websrv  A       192.168.25.106
www     CNAME   websrv

chgrp named /var/named/magedu.com.zone

5
named-checkconf
named-checkzone magedu.com /var/named/magedu.com.zone
rndc reload

6 测试


实验：实现HTTPS HSTS
环境：三台主机

一台CA和DNS，一台client,一台httpd

1 DNS

www.a.com IP

2 httpd mod_ssl

3 CA

4 httpd 向CA申请，CA颁发证书

5 httpd

四个文件 httpd.key httpd.csr httpd.crt cacert.pem 
/etc/httpd/conf.d/ssl/

6 vim /etc/httpd/conf.d/ssl.conf
SSLCertificateFile /etc/httpd/conf.d/ssl/httpd.crt
SSLCertificateKeyFile /etc/httpd/conf.d/ssl/httpd.key
SSLCACertificateFile /etc/httpd/conf.d/ssl/cacert.pem

7 client 
CA证书导入


vim /etc/httpd/conf/httpd.conf
Header always set Strict-Transport-Security "max-age=15768000"
RewriteEngineon
RewriteRule^(/.*)$ https://%{HTTP_HOST}$1 [redirect=301]


1、建立httpd服务器，要求提供两个基于名称的虚拟主机:
(1)www.X.com，页面文件目录为/web/vhosts/x；错误日志为/var/log/httpd/x.err，访问日志为/var/log/httpd/x.access
(2)www.Y.com，页面文件目录为/web/vhosts/y；错误日志为/var/log/httpd/www2.err，访问日志为/var/log/httpd/y.access
(3)为两个虚拟主机建立各自的主页文件index.html，内容分别为其对应的主机名
(4)通过www.X.com/server-status输出httpd工作状态相关信息
2、为上面的第2个虚拟主机提供https服务，使得用户可以通过https安全的访问此web站点
(1)要求使用证书认证，证书中要求使用的国家(CN)、州(Beijing)、城市(Beijing)和组织(MageEdu)
(2)设置部门为Ops，主机名为www.Y.com，邮件为admin@Y.com


vim /etc/httpd/conf.d/vhosts.conf 
<virtualhost *:80 >
servername www.a.com
documentroot "/app/a.com/"
ProxyPass "/" "http://www.a.com:8080/"
ProxyPassReverse "/" "http://www.a.com:8080/"
<directory /app/a.com>
Require all granted
</directory>
</virtualhost>
listen 8080
<virtualhost *:8080 >
servername www.b.com
documentroot "/app/b.com/"
<directory /app/b.com>
Require all granted
</directory>
</virtualhost>

<virtualhost *:80 >
servername www.c.com

<directory /app/c.com>
Require all granted
</directory>
documentroot "/app/c.com/"
</virtualhost>


实验：centos7源码编译httpd2.4

1 yum groupinstall "development tools"
yum install apr-devel apr-util-devel openssl-devel pcre-devel 

2 tar xvf httpd-2.4.27.tar.bz2 -C /usr/local/src

3 cd  /usr/local/src/httpd-2.4.27/
./configure --prefix=/app/httpd24 --enable-so --enable-ssl --enable-cgi --enable-rewrite --with-zlib --with-pcre --with-apr=/app/apr/ --with-apr-util=/app/apr-util/ --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork

make -j 4 && make install

4 useradd -r -d /app/httpd2/htdoc  -s /sbin/nologin apache 
vim /app/httpd24/conf/httpd.conf
User apache
Group apache
 
5 vim /etc/profile.d/httpd24.sh
PATH=/app/httpd24/bin:$PATH
. /etc/profile.d/httpd24.sh

6 vim /etc/rc.d/rc.local
/app/httpd24/bin/apachectl start
chmod +x   /etc/rc.d/rc.local

7 apachectl start
测试访问


实验：centos6源码编译httpd2.4

环境：centos6.9 apr-1.6.2.tar.gz apr-util-1.6.0.tar.gz   httpd-2.4.27.tar.bz2

1 yum groupinstall "Development tools"
 yum install openssl-devel pcre-devel expat-devel 
 
2 tar xvf apr-1.6.2.tar.gz apr-util-1.6.0.tar.gz   httpd-2.4.27.tar.bz2

3
cd apr-1.6.2
./configure --prefix=/app/apr

4 ./configure --prefix=/app/apr-util --with-apr=/app/apr
 make & make install

5 useradd -r -d  /app/website -s /sbin/nologin apache  

6 cd httpd-2.4.27
 ./configure --prefix=/app/httpd24 --enable-so --enable-ssl --enable-cgi --enable-rewrite --with-zlib --with-pcre --with-apr=/app/apr/ --with-apr-util=/app/apr-util/ --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork
 make & make install

7 vim  /app/httpd24/conf/httpd.conf
user apache
group apache
Documentroot /app/website
<directory /app/website>

8 vim /etc/profile.d/httpd24.sh
PATH=/app/httpd24/bin:$PATH

9 vim /etc/init.d/httpd24
apachectl=/app/httpd24/bin/apachectl
httpd=${HTTPD-/app/httpd24/bin/httpd}
pidfile=${PIDFILE-/app/httpd24/logs/httpd.pid}
lockfile=${LOCKFILE-/var/lock/subsys/httpd24}

chkconfig --add httpd24
chkconfig httpd24 on
service httpd24 start

10 mkdir /app/website
echo /app/website/index.html > index.html

11 测试

实验：centos6编译安装httpd2.4方法2

cp -r apr-1.6.2 httpd-2.4.27/srclib/apr
cp -r apr-util-1.6.0 httpd-2.4.27/srclib/apr-util
./configure --prefix=/usr/local/httpd24 --enable-so --enable-ssl --enable-cgi --enable-rewrite --with-zlib --with-pcre --with-included-apr --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork


使用httpd-2.4实现
1、建立httpd服务，要求：
(1) 提供两个基于名称的虚拟主机：
www.a.com
页面文件目录为/web/vhosts/www1
错误日志为/var/log/httpd/www1/error_log
访问日志为/var/log/httpd/www1/access_log
www.b.com
页面文件目录为/web/vhosts/www2
错误日志为/var/log/httpd/www2/error_log
访问日志为/var/log/httpd/www2/access_log
(2) 通过www.a.com/server-status输出其状态信息，且要求只允许提供账号的用户访问
(3) www.a.com不允许192.168.1.0/24网络中的主机访问

2、为上面的第2个虚拟主机提供https服务，使得用户可以通过https安全的访问此web站点
(1) 要求使用证书认证，证书中要求使用国家（CN），州（Beijing），城市（Beijing），组织为(MageEdu)
(2) 设置部门为Ops, 主机名为www.b.com


实验：实现LAMP，编写Index.php，测试php及数据库

实验：phpmyadmin 
一个主机


实验:centos7.3实现lamp应用wordpress
环境
host1 httpd,php
host2 mariadb 
1 yum install httpd php php-mysql 
yum install mariadb-server
systemctl start httpd
systemctl start mariadb

2 创建数据库及用户
mysql> create database wpdb;
mysql> grant all  on wpdb.* to wpuser@'%' identified by "wppass";

3 下载源
tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /var/www/html/
cd /var/www/html
ln -s wordpress/ blog
setfacl -R -m u:apache:rwx wordpress/

4 http://websrv/blog/


实验：centos7.3实现基于源码编译安装LAMP的wordpress应用

软件环境：
apr-1.6.2.tar.gz       httpd-2.4.27.tar.bz2                php-7.1.10.tar.xz
apr-util-1.6.0.tar.gz  mariadb-10.2.8-linux-x86_64.tar.gz  wordpress-4.8.1-zh_CN.tar.gz

两台主机：一台实现LAP ，一台实现M

1 源码编译安装Httpd2.4
yum groupinstall "development tools"
yum install openssl-devel expat-devel pcre-devel 

tar xvf apr-1.6.2.tar.gz 
tar xvf apr-util-1.6.0.tar.gz 
tar xvf httpd-2.4.27.tar.bz2 
cp -r apr-1.6.2 httpd-2.4.27/srclib/apr
cp -r apr-util-1.6.0 httpd-2.4.27/srclib/apr-util
cd httpd-2.4.27/
./configure --prefix=/app/httpd24 --sysconfdir=/etc/httpd24 --enable-so --enable-ssl --enable-rewrite --with-zlib --with-pcre --with-included-apr --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork
make -j 4 && make install

vim /etc/profile.d/lamp.sh
PATH=/app/httpd24/bin/:$PATH
. /etc/profile.d/lamp.sh
apachectl 
ss -tnl

2 二进制安装mariadb 
tar xvf mariadb-10.2.8-linux-x86_64.tar.gz  -C /usr/local/
cd /usr/local
ln -s mariadb-10.2.8-linux-x86_64/ mysql
useradd -r -m -d /app/mysqldb -s /sbin/nologin mysql 
cd mysql/
scripts/mysql_install_db --datadir=/app/mysqldb --user=mysql
mkdir /etc/mysql
cp support-files/my-large.cnf   /etc/mysql/my.cnf

vim /etc/mysql/my.cnf
[mysqld]
datadir = /app/mysqldb
innodb_file_per_table = ON
skip_name_resolve = ON

cp support-files/mysql.server /etc/init.d/mysqld
chkconfig --add mysqld
chkconfig --list 
service mysqld start

mkdir /var/log/mariadb
chown mysql /var/log/mariadb/
service mysqld start

vi /etc/profile.d/lamp.sh 
PATH=/app/httpd24/bin/:/usr/local/mysql/bin/:$PATH
. /etc/profile.d/lamp.sh

mysql_secure_installation

mysql -uroot -pcentos
create datebase wpdb;
grant all on wpdb.* to wpuser@'192.168.25.%' identified by 'centos';
grant all on wpdb.* to wpuser@'127.%' identified by 'centos';
grant all on wpdb.* to wpuser@'localhost' identified by 'centos';


3 源码编译安装Php
yum install libxml2-devel bzip2-devel libmcrypt-devel 
tar xvf php-7.1.10.tar.xz 
cd php-7.1.10/

./configure \
--prefix=/app/php \
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

cp php.ini-production /etc/php.ini

vim /etc/httpd24/httpd.conf
在文件尾部加两行
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
修改下面行
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>
apachectl stop
apachectl 

4 测试php和mariadb连接

vim /app/httpd24/htdocs/index.php 
<html><body><h1> LAMP</h1></body></html>
<?php
$mysqli=new mysqli("localhost","root","centos");
if(mysqli_connect_errno()){
echo "连接数据库失败!";
$mysqli=null;
exit;
}
echo "连接数据库成功!";
$mysqli->close();
phpinfo();
?>


5 配置wordpress

tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /app/httpd24/htdocs
cd /app/httpd24/htdocs
mv wordpress/ blog/

cd /app/httpd24/htdocs/blog/
cp wp-config-sample.php  wp-config.php
vim wp-config.php
define('DB_NAME', 'wpdb');

/** MySQL数据库用户名 */
define('DB_USER', 'wpuser');

/** MySQL数据库密码 */
define('DB_PASSWORD', 'centos');

/** MySQL主机 */
define('DB_HOST', 'localhost');


6 登录测试
http://websrv/blog 
测试性能
ab -c 10 -n 100 http://websrv/blog/


实验：centos6.9实现基于源码编译安装LAMP(php模块方式)的wordpress应用

软件版本：
apr-1.6.2.tar.gz       httpd-2.4.27.tar.bz2                php-5.6.31.tar.xz             xcache-3.2.0.tar.bz2
apr-util-1.6.0.tar.gz  mariadb-5.5.57-linux-x86_64.tar.gz  wordpress-4.8.1-zh_CN.tar.gz

1 编译httpd2.4
yum groupinstall "development tools"
yum install openssl-devel pcre-devel expat-devel
tar xvf apr-1.6.2.tar.gz 
tar xvf apr-util-1.6.0.tar.gz 
tar xvf httpd-2.4.27.tar.bz2 
cp -r apr-1.6.2 httpd-2.4.27/srclib/apr
cp -r apr-util-1.6.0 httpd-2.4.27/srclib/apr-util

cd httpd-2.4.27/
./configure --prefix=/app/httpd24 --enable-so --enable-ssl --enable-rewrite --with-zlib --with-pcre --with-included-apr --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork
make -j 4 && make install

vim /etc/profile.d/lamp.sh
PATH=/app/httpd24/bin/:$PATH
. /etc/profile.d/lamp.sh

cp  /etc/init.d/httpd  /etc/init.d/httpd24

vim /etc/init.d/httpd24
apachectl=/app/httpd24/bin/apachectl
httpd=${HTTPD-/app/httpd24/bin/httpd}
prog=httpd
pidfile=${PIDFILE-/app/httpd24/logs/httpd.pid}
lockfile=${LOCKFILE-/var/lock/subsys/httpd24}

chkconfig --add httpd24
chkconfig --list httpd24
service httpd24 start


2 二进制安装mariadb
tar xvf mariadb-5.5.57-linux-x86_64.tar.gz  -C /usr/local/
cd /usr/local/
ln -s mariadb-5.5.57-linux-x86_64/ mysql

useradd -r -m -d /app/mysqldb -s /sbin/nologin mysql 
cd mysql/
scripts/mysql_install_db --datadir=/app/mysqldb --user=mysql
mkdir /etc/mysql
cp support-files/my-large.cnf   /etc/mysql/my.cnf

vim /etc/mysql/my.cnf
[mysqld]
datadir = /app/mysqldb
innodb_file_per_table = ON
skip_name_resolve = ON

cp support-files/mysql.server /etc/init.d/mysqld
chkconfig --add mysqld
chkconfig --list 
service mysqld start

touch /var/log/mysqld.log
chown mysql /var/log/mysqld.log
service mysqld start

vi /etc/profile.d/lamp.sh 
PATH=/app/httpd24/bin/:/usr/local/mysql/bin/:$PATH
. /etc/profile.d/lamp.sh

mysql_secure_installation

mysql -uroot -pcentos
create datebase wpdb;
grant all on wpdb.* to wpuser@'192.168.25.%' identified by 'centos';
grant all on wpdb.* to wpuser@'127.%' identified by 'centos';
grant all on wpdb.* to wpuser@'localhost' identified by 'centos';

3 源码编译php
yum install libxml2-devel bzip2-devel libmcrypt-devel

tar xvf php-5.6.31.tar.xz 
cd php-5.6.31
./configure --prefix=/app/php \
--with-mysql=/usr/local/mysql \
--with-openssl \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--enable-mbstring \
--with-png-dir \
--with-jpeg-dir \
--with-freetype-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--enable-sockets \
--with-apxs2=/app/httpd24/bin/apxs \
--with-mcrypt \
--with-config-file-path=/etc \
--with-config-file-scan-dir=/etc/php.d \
--with-bz2

make -j 4 && make install


vi /etc/profile.d/lamp.sh 
PATH=/app/php/bin:/app/httpd24/bin/:/usr/local/mysql/bin/:$PATH
.  /etc/profile.d/lamp.sh 

cp php.ini-production /etc/php.ini

vim /etc/httpd24/httpd.conf
在文件尾部加两行
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
修改下面行
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

service httpd24 restart

4 测试
vim /app/httpd24/htdocs/index.php
<html><body><h1>It works!</h1></body></html>
<?php
$mysqli=new mysqli("localhost","root","centos");
if(mysqli_connect_errno()){
echo "连接数据库失败!";
$mysqli=null;
exit;
}
echo "连接数据库成功!";
$mysqli->close();


$conn = mysql_connect('localhost','root','centos');
if ($conn)
echo "OK";
else
echo "Failure";
mysql_close();

phpinfo();

?>


5 配置wordpress

tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /app/httpd24/htdocs
cd /app/httpd24/htdocs
mv wordpress/ blog/

cd /app/httpd24/htdocs/blog/
cp wp-config-sample.php  wp-config.php
vim wp-config.php
define('DB_NAME', 'wpdb');

/** MySQL数据库用户名 */
define('DB_USER', 'wpuser');

/** MySQL数据库密码 */
define('DB_PASSWORD', 'centos');

/** MySQL主机 */
define('DB_HOST', 'localhost');


6 登录测试
http://websrv/blog 
测试性能
ab -c 10 -n 100 http://websrv/blog/

7 编译xcache 实现Php加速
tar xvf xcache-3.2.0.tar.bz2 
cd xcache-3.2.0



phpize 
./configure  --enable-xcache --with-php-config=/app/php/bin/php-config 
make && make install

mkdir /etc/php.d/
cp xcache.ini  /etc/php.d/
vim /etc/php.d/xcache.ini 
extension = /app/php/lib/php/extensions/no-debug-non-zts-20131226/xcache.so

service httpd24 restart

8 测试
ab -c 10 -n 100 http://websrv/blog/



实验：centos6.9实现基于源码编译安装LAMP(FPM模块方式)的wordpress应用

软件版本：
apr-1.6.2.tar.gz       httpd-2.4.27.tar.bz2                php-5.6.31.tar.xz             xcache-3.2.0.tar.bz2
apr-util-1.6.0.tar.gz  mariadb-5.5.57-linux-x86_64.tar.gz  wordpress-4.8.1-zh_CN.tar.gz

1 编译httpd2.4
yum groupinstall "development tools"
yum install openssl-devel pcre-devel expat-devel
tar xvf apr-1.6.2.tar.gz 
tar xvf apr-util-1.6.0.tar.gz 
tar xvf httpd-2.4.27.tar.bz2 
cp -r apr-1.6.2 httpd-2.4.27/srclib/apr
cp -r apr-util-1.6.0 httpd-2.4.27/srclib/apr-util

cd httpd-2.4.27/
./configure --prefix=/app/httpd24 --enable-so --enable-ssl --enable-rewrite --with-zlib --with-pcre --with-included-apr --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork
make -j 4 && make install

vim /etc/profile.d/lamp.sh
PATH=/app/httpd24/bin/:$PATH
. /etc/profile.d/lamp.sh

cp  /etc/init.d/httpd  /etc/init.d/httpd24

vim /etc/init.d/httpd24
apachectl=/app/httpd24/bin/apachectl
httpd=${HTTPD-/app/httpd24/bin/httpd}
prog=httpd
pidfile=${PIDFILE-/app/httpd24/logs/httpd.pid}
lockfile=${LOCKFILE-/var/lock/subsys/httpd24}

chkconfig --add httpd24
chkconfig --list httpd24
service httpd24 start


2 二进制安装mariadb
tar xvf mariadb-5.5.57-linux-x86_64.tar.gz  -C /usr/local/
cd /usr/local/
ln -s mariadb-5.5.57-linux-x86_64/ mysql

useradd -r -m -d /app/mysqldb -s /sbin/nologin mysql 
cd mysql/
scripts/mysql_install_db --datadir=/app/mysqldb --user=mysql
mkdir /etc/mysql
cp support-files/my-large.cnf   /etc/mysql/my.cnf

vim /etc/mysql/my.cnf
[mysqld]
datadir = /app/mysqldb
innodb_file_per_table = ON
skip_name_resolve = ON

cp support-files/mysql.server /etc/init.d/mysqld
chkconfig --add mysqld
chkconfig --list 
service mysqld start

touch /var/log/mysqld.log
chown mysql /var/log/mysqld.log
service mysqld start

vi /etc/profile.d/lamp.sh 
PATH=/app/httpd24/bin/:/usr/local/mysql/bin/:$PATH
. /etc/profile.d/lamp.sh

mysql_secure_installation

mysql -uroot -pcentos
create datebase wpdb;
grant all on wpdb.* to wpuser@'192.168.25.%' identified by 'centos';
grant all on wpdb.* to wpuser@'127.%' identified by 'centos';
grant all on wpdb.* to wpuser@'localhost' identified by 'centos';

3 源码编译php
yum install libxml2-devel bzip2-devel libmcrypt-devel
tar xvf php-5.6.31.tar.xz 
cd php-5.6.31

./configure \
--prefix=/app/php5 \
--with-mysql=/usr/local/mysql \
--with-openssl \
--with-mysqli=/usr/local/mysql/bin/mysql_config \
--enable-mbstring \
--with-freetype-dir  \
--with-jpeg-dir \
--with-png-dir \
--with-zlib \
--with-libxml-dir=/usr \
--enable-xml \
--enable-sockets \
--enable-fpm \
--with-mcrypt \
--with-config-file-path=/etc/php5 \
--with-config-file-scan-dir=/etc/php5.d \
--with-bz2

make -j 4 && make install

vi /etc/profile.d/lamp.sh 
PATH=/app/php5/bin:/app/httpd24/bin/:/usr/local/mysql/bin/:$PATH
.  /etc/profile.d/lamp.sh 

mkdir /etc/php5/
cp php.ini-production /etc/php5/php.ini
cp   sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm
chkconfig --add php-fpm
chkconfig --list  php-fpm

cd /app/php5/etc
cp php-fpm.conf.default php-fpm.conf

vim /etc/httpd24/httpd.conf
取消两行的注释
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so

在文件尾部加四行
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
ProxyRequests Off 关闭正向代理
ProxyPassMatch  ^/(.*\.php)$ fcgi://127.0.0.1:9000/app/httpd24/htdocs/$1

修改下面行
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

service httpd24 restart

4 测试
vim /app/httpd24/htdocs/index.php
<html><body><h1>It works!</h1></body></html>
<?php
$mysqli=new mysqli("localhost","root","centos");
if(mysqli_connect_errno()){
echo "连接数据库失败!";
$mysqli=null;
exit;
}
echo "连接数据库成功!";
$mysqli->close();


$conn = mysql_connect('localhost','root','centos');
if ($conn)
echo "OK";
else
echo "Failure";
mysql_close();

phpinfo();

?>


5 配置wordpress

tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /app/httpd24/htdocs
cd /app/httpd24/htdocs
mv wordpress/ blog/

cd /app/httpd24/htdocs/blog/
cp wp-config-sample.php  wp-config.php
vim wp-config.php
define('DB_NAME', 'wpdb');

/** MySQL数据库用户名 */
define('DB_USER', 'wpuser');

/** MySQL数据库密码 */
define('DB_PASSWORD', 'centos');

/** MySQL主机 */
define('DB_HOST', 'localhost');


6 登录测试
http://websrv/blog 
测试性能
ab -c 10 -n 100 http://websrv/blog/

7 编译xcache 实现Php加速
tar xvf xcache-3.2.0.tar.bz2 
cd xcache-3.2.0

phpize 
./configure  --enable-xcache --with-php-config=/app/php5/bin/php-config 
make && make install

mkdir /etc/php.d/
cp xcache.ini  /etc/php5.d/
vim /etc/php5.d/xcache.ini 
extension = /app/php5/lib/php/extensions/no-debug-non-zts-20131226/xcache.so

service php-fpm restart

8 测试
ab -c 10 -n 100 http://websrv/blog/


实验：基于TCP或UDP远程日志

两台主机

1 在日志服务器上

vim /etc/rsyslog.conf
$ModLoad imudp
$UDPServerRun 514

# Provides TCP syslog reception
$ModLoad imtcp
$InputTCPServerRun 514

2 在应用服务器
vim /etc/rsyslog.conf
local2.*                                        @@192.168.25.107

实验：实现loganalyzer

三台主机

