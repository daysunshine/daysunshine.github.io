20170712

�����ܽ�
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


��
��

0
1
9 
A
B

F
10 

ȥ
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


bit λ   b
byte �ֽ� B 8λ 2^8
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


Nλ

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


��һ������ ���� �̷�C��
��һ������ mount on  /

/dev/sda1 mount /boot
/dev/sda2 mount /
mount point
���ص�


/
�ڶ������� �����̷�D��

/dev/

���� �����豸��


centos6,7
/dev/sda,b,c

MBR ��������
GPT ����

�������ͣ�
��������һ��Ӳ������ĸ���ֻ��һ����������ٻ��� 1-4
��չ������һ��Ӳ�����һ��������ֱ�Ӵ����ݣ�����С������������չ����ĸ��� 1-4
�߼������� 5-


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
/dev/sda1 mount  /boot  1G    ext4 xfs  �ļ�ϵͳ����
/dev/sda2 mount  /      50G
/dev/sda3 mount  /app/  50G
/dev/sda4 		99G
/dev/sda5    		2G  

swap �����ڴ� 2G

FAT32 ת����NTFS����
convert d:/fs:ntfs


vmware 
ctrl+alt+enter ȫ���л�
ctrl+alt  �ƶ����


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
��shell

AIX unix

ksh bash


ctrl+l =clear ����
logout quit exit ctrl+D ע��


��ҵ��
1 ��Ϥcentos6,7�İ�װ���� 
2 ��ϰǰ��ѧϰ����������


20170717

ʱ����
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

¼��
script -t 2> /app/time.log  -a /app/cmd.log
����¼��
scriptreplay  time.log  cmd.log 

echo '- - -' > /sys/class/scsi_host/host2/scan 



��ҵ��
1����ʾ10��ǰ�����ڼ�
2����ʾ��ǰʱ�䣬��ʽ��2016-06-18 10:20:30
3�����õ�ǰ����Ϊ2019-08-07 06:05:10
4���ڱ����ַ��ն˵�¼ʱ������ʾԭ����Ϣ�⣬����ʾ��ǰ��¼�ն˺ţ��������͵�ǰʱ��
5������18��30�Զ��ػ�������ʾ�û�


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
b �� 	/dev/sda
c �ַ� /dev/zero
p pipe
s 
 
��ҵ��
1 ��ʾָ��Ŀ¼�µ�����Ŀ¼����Ҫ�ļ�
2 ֻ��ʾָ��Ŀ¼�µ������ļ�

glob ͨ���

*.mp3


��ϰ
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

�������ļ���
rename  '.txt' '.txt.bak' *.txt
rename  '.bak' '' *.bak      



��ϰ
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

��ҵ��
13
��ϰ
?1����/etc/issue�ļ��е�����ת��Ϊ��д�󱣴���/tmp/issue.out�ļ���
?2������ǰϵͳ��¼�û�����Ϣת��Ϊ��д�󱣴���/tmp/who.out�ļ���
?3��һ��linux�û���root���ʼ���Ҫ���ʼ�����Ϊ��help�����ʼ��������£�
Hello, I am �û���,The system version is here,pleasehelp me to check it ,thanks!
����ϵͳ�汾��Ϣ
?4����/root/���ļ��б���ʾ��һ�У����ļ���֮���ÿո����
?5������1+2+3+..+99+100���ܺ�
?6��ɾ��Windows�ı��ļ��еġ�^M���ַ�
?7�������ַ�����xt.,l 1 jr#!$mn2 c*/fe3 uz4����ֻ�������е����ֺͿո�
?8����PATH����ÿ��Ŀ¼��ʾ�ڶ�����һ��
?9����ָ���ļ���0-9�ֱ������a-j
?10�����ļ���ÿ�����ʣ�����ĸ��ɣ���ʾ�ڶ�����һ�У����޿���


sid 

s--adfasdf-500 administartor
s343434sss-501 guest 


opts:tom ,jerry 
opts:file:д
devs:tom
devs:file����

security context


20170724

�ļ�
��Ȩ�ޣ��鿴���ݣ�����
дȨ�ޣ������޸����� 
ִ��Ȩ�ޣ��Ƿ������ ��rootҲ��Ч

Ŀ¼��
��Ȩ�ޣ����Կ��鿴Ŀ¼���ļ��б�
ִ��Ȩ�ޣ�����cd����Ŀ¼�����Կ�Ŀ¼���ļ�Ԫ����
дȨ�ޣ����Դ�����ɾ��Ŀ¼���ļ�������ҪxȨ��


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


�ָ���Ŀ¼

����1
 mkdir -m 700 wang
 cp -a /etc/skel/.[^.]* wang/
 chown -R wang.wang wang/

����2
 cp -r /etc/skel /home/mage
 chown -R mage:mage /home/mage
 chmod 700 /home/mage

umask000 + default666 = file666/dir777

Ŀ¼:
umask + default = dir777
�ļ���
666-umask
�۲�������������λ��1��ż������


1�����û�xiaoming��/testdir Ŀ¼��ִ��Ȩ��ʱ����ζ���޷�����Щ������
2�����û�xiaoqiang��/testdir Ŀ¼�޶�Ȩ��ʱ����ζ���޷�����Щ������
3�����û�wangcai ��/testdir Ŀ¼��дȨ��ʱ����Ŀ¼�µ�ֻ���ļ�file1�Ƿ���޸ĺ�ɾ����
4�����û�wangcai ��/testdir Ŀ¼��д��ִ��Ȩ��ʱ����Ŀ¼�µ�ֻ���ļ�file1�Ƿ���޸ĺ�ɾ����
5������/etc/fstab�ļ���/var/tmp�£������ļ�������Ϊwangcai��дȨ�ޣ�������Ϊsysadmins���ж�дȨ�ޣ���������Ȩ��
6����ɾ�����û�wangcai�ļ�Ŀ¼�����ؽ����ָ����û���Ŀ¼����Ӧ��Ȩ������
7����/testdir/dir�ﴴ�������ļ��Զ�����g1�飬��g2�ĳ�Ա�磺alice�ܶ���Щ���ļ��ж�дȨ�ޣ���g3�ĳ�Ա�磺tomֻ�ܶ����ļ��ж�Ȩ�ޣ������û���������g1,g2,g3�����ܷ�������ļ��С�
8������/testdir/dir�������ļ���ACLȨ�޵�/root/acl.txt�У����/testdir/dir������ACLȨ�ޣ����ԭACLȨ��
9���鿴access_log��־�ļ��У�Զ������IP����������ǰ10����IP


SUID
���ã������ڿ�ִ�ж����Ƴ����ϣ�����ǰ����������л��ɸó��������ߵ����

owner1 suid /bin/app    owner2 /testdir/f1.txt

mage  /bin/cat �������mage      /testdir/f1.txt
mage  /bin/app �л���� owner1    /testdir/f1.txt


SGID
���ã�
1�����ڿ�ִ�ж����Ƴ����ϣ�����ǰ����������л��ɸó�������������
2��Ŀ¼��Ŀ¼�ڵ����ļ����Զ��̳�Ŀ¼��������

STICKY 
���ã�ֻ��Ŀ¼��Ч��ճ��λ


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


��ϰ

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


��ϰ
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


��ҵ��
1��������չ������ʽ�ֱ��ʾ0-9��10-99��100-199��200-249��250-255
2����ʾifconfig������������IPv4��ַ
#ifconfig |grep -Eo "\<(([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\.){3}([0-9]|[1-9][0-9]|1[0-9]{2}|2[0-4][0-9]|25[0-5])\>"

3�������ַ�����welcome to magedu linux �е�ÿ���ַ�ȥ�ز������ظ���������ŵ�ǰ��
echo "welcome to magedu linux" |grep -o "." |sort |uniq -c |sort -nr

�ж�centos���汾��
cat /etc/centos-release |grep -o " [0-9]"  
 
 
ls *.rpm |grep -o "[^.]*\.rpm\>"|cut -d. -f1 |sort|uniq -c 
ls *.rpm | rev|cut -d. -f2|rev |sort |uniq -c



��ҵ��
1������/etc/profile��/tmp/Ŀ¼���ò����滻����ɾ��/tmp/profile�ļ��е����׵Ŀհ��ַ�
2������/etc/rc.d/init.d/functions�ļ���/tmpĿ¼���ò����滻����Ϊ/tmp/functions��ÿ�п�ͷΪ�հ��ַ����е��������һ��#��
3����vim������tab����Ϊ4���ַ�
4������/etc/rc.d/init.d/functions�ļ���/tmpĿ¼���滻/tmp/functions�ļ��е�/etc/sysconfig/initΪ/var/log
5��ɾ��/tmp/functions�ļ���������#��ͷ����#����������һ���հ��ַ����е����׵�#��
6����д�ű�/root/bin/systeminfo.sh,��ʾ��ǰ����ϵͳ��Ϣ��������������IPv4��ַ������ϵͳ�汾���ں˰汾��CPU�ͺţ��ڴ��С��Ӳ�̴�С��
7����д�ű�/root/bin/backup.sh����ʵ��ÿ�ս�/etc/Ŀ¼���ݵ�/root/etcYYYY-mm-dd��
8����д�ű�/root/bin/disk.sh,��ʾ��ǰӲ�̷����пռ�����������ֵ
9����д�ű�/root/bin/links.sh,��ʾ�����ӱ�������ÿ��Զ��������IPv4��ַ���������������������Ӵ�С����



find /etc  \( -path '/etc/sane.d' -o -path '/etc/fonts' \) -a -prune -o -name "*.conf"


20170731

cmd1 && cmd2  ��·��
cmd1Ϊ�棬��ִ��cmd2��cmd2Ϊ�棬���Ϊ�棬cmd2Ϊ�٣����Ϊ��
cmd1Ϊ�٣�����ִ��cmd2 �����ҽ��Ϊ��

cmd1 || cmd2  ��·��

cmd1Ϊ�棬����ִ��cmd2�����Ϊ��
cmd1Ϊ�٣���ִ��cmd2 ��cmd2Ϊ�棬���Ϊ�棬cmd2Ϊ�٣����Ϊ��

��������
a=10;b=6;a=$[a^b];b=$[a^b];a=$[a^b];echo a=$a;echo b=$b


ver=`cat /etc/centos-release  | grep -o " [0-9]"|cut -d" " -f2`

�ж�����
[[ "$num" =~ ^-?[0-9]+$  ]] && echo $num is  number || echo $num is not number 


[[ "$file" =~ ^.*\.sh$  ]] && echo script  || echo  not script

��ҵ��
1����д�ű�/root/bin/sumspace.sh�����������ļ�·����Ϊ�������ű��������������ļ������пհ���֮��
2����д�ű�/root/bin/sumfile.sh,ͳ��/etc, /var, /usrĿ¼�й��ж��ٸ�һ����Ŀ¼���ļ�
3����д�ű�/bin/per.sh,�жϵ�ǰ�û���ָ���Ĳ����ļ����Ƿ񲻿ɶ����Ҳ���д
4����д�ű�/root/bin/excute.sh ���жϲ����ļ��Ƿ�Ϊsh��׺����ͨ�ļ�������ǣ���������˿�ִ��Ȩ�ޣ�������ʾ�û��ǽű��ļ�
5����д�ű�/root/bin/nologin.sh��login.sh,ʵ�ֽ�ֹ�ͳ�����ͨ�û���¼ϵͳ
6������/varĿ¼������Ϊroot��������Ϊmail�������ļ�
7������/varĿ¼�²�����root��lp��gdm�������ļ�
8������/varĿ¼�����һ�����������޸Ĺ���ͬʱ������Ϊroot��Ҳ����postfix���ļ�
9�����ҵ�ǰϵͳ��û�����������飬�����һ�������������ʹ����ļ�
10������/etcĿ¼�´���1M������Ϊ��ͨ�ļ��������ļ�
11������/etcĿ¼�������û���û��дȨ�޵��ļ�
12������/etcĿ¼��������һ���û�û��ִ��Ȩ�޵��ļ�
13������/etc/init.dĿ¼�£������û�����ִ��Ȩ�ޣ��������û���дȨ�޵��ļ�


20170802

rpm -ivh �ļ���
rpm -e  ����
rpm -qa "*����*"
rpm -ql ����
rpm -qi ����
rpm -qp �ļ���
rpm -K 
rpm --import 
yum install remove list repolist grouplist groupinstall groupremove reinstall history clean all
yum -y -q 


��ҵ��
1 ��װ���ں� �������ں� 
2 rm -f /bin/rpm ,��װRpm���ָ�֮ �� cpio 
3 /usr/bin/java �ҳ������ĸ���
4 ɾ��/lib64/libc/lib64/libc.so.6 �ָ�֮
5 reset.sh 


20170804

ʵ�飺ʵ�ֶ�ϵͳ����yum������


׼����
1 �رշ���ǽ
centos7
systemctl disable firewalld.service 
systemctl stop firewalld.service 
centos6 
chkconfig iptables off
service iptables stop

iptables -vnL

2 �ر�SElinux
vim /etc/selinux/config
SELINUX=permissive

setenforce  0
getenforce ��֤�Ƿ���Ч


1 ��װftp�����
rpm -ivh /run/media/root/CentOS\ 7\ x86_64/Packages/vsftpd-3.0.2-21.el7.x86_64.rpm 

rpm -ql vsftpd
/usr/lib/systemd/system/vsftpd.service
/var/ftp
systemctl start vsftpd ����ftp����
ss -tnl  21�˿ڴ�
systemctl enable vsftpd ��Ϊ�����Զ�����ftp����

2 ׼��yum ϵͳ��װ��

mkdir -p /var/ftp/pub/centos/{6,7} 
cp -r /run/media/root/CentOS\ 7\ x86_64/* /var/ftp/pub/centos/7
cp -r /run/media/root/CentOS\ 6\ x86_64/* /var/ftp/pub/centos/6

3 �ͻ�������

vim /etc/yum.repos.d/base.repo 
[base]
name=centos 
#baseurl=file:///misc/cd/
baseurl=ftp://192.168.25.129/pub/centos/$releasever/
gpgkey=ftp://192.168.25.129/pub/centos/$releasever/RPM-GPG-KEY-CentOS-$releasever


ʵ��:��������yum�ֿ�

1 ��װhttp�����

yum -y install httpd
rpm -ql httpd
/usr/lib/systemd/system/httpd.service
/var/www/html
systemctl start httpd ����httpd����
ss -tnl  80�˿ڴ�
systemctl enable httpd ��Ϊ�����Զ����� httpd����

vim /var/www/html/index.html 
<h1>welcome to Magedu.com </h1> 

2 ׼��������rpm��ذ�
mkdir /var/www/html/app
cp *.rpm /var/www/html/app
createrepo  /var/www/html/app

3�ͻ���
[app]
name=app
baseurl=http://192.168.25.129/app
gpgcheck=0

ʵ��:��centos6.9Դ�����httpd-2.2.34

0 ׼������
�鿴��ͬ�����
rpm -qa "httpd*"
yum remove httpd

1 ��װ��������
yum groupinstall "Development tools"

2 ����Դ�벢���
tar xvf httpd-2.2.34.tar.bz2
cd /usr/local/src/httpd-2.2.34


3 ��˵�� 
cat README
cat INSTALL 

4 ����Makefile
cd /usr/local/src/httpd-2.2.34
./configure --help
./configure --prefix=/app/httpd22/ --sysconfdir=/etc/httpd22/ --enable-ssl
echo $?
yum install openssl-devel
./configure --prefix=/app/httpd22/ --sysconfdir=/etc/httpd22/ --enable-ssl

5
make && make install 

6 �������
׼����������
echo 'export PATH=/app/httpd22/bin:$PATH' > /etc/profile.d/httpd22.sh  
. /etc/profile.d/httpd22.sh
echo $PATH
apachctl start
ss -ntl 80�˿ڴ򿪼��ɹ�
http://����IP/
vim /app/httpd22/htdocs/index.html

vim /etc/man.config 
��һ��
MANPATH /app/httpd22/man


��ҵ��
1����ѯ����java�������ĸ�rpm��
2��yum�����ú�ʹ��,����yum�ֿ�Ĵ���
3����дϵͳ��ʼ���ű�reset.sh��������������ʾ����ɫ��yum�ֿ������ļ�,��װtree,ftp,lftp,telnet�Ȱ�
4����CentOS6�ϱ��밲װapache 2.2Դ���,�������˷���
5����CentOS7�ϱ��밲װapache 2.4Դ���,�������˷���
6��tar xvf httpd.tar.bz2 && cd httpd && ./install.sh ʵ��һ����װhttpd


ʵ�飺����MBR�ͻָ�

20170807

blkid -U `grep /app /etc/fstab  |cut -d" " -f1|cut -d"=" -f2`  

��ҵ��
1������һ��2G���ļ�ϵͳ�����СΪ2048byte��Ԥ��1%���ÿռ�,�ļ�ϵͳext4�����ΪTEST��Ҫ��˷����������Զ�������/testĿ¼����Ĭ����acl����ѡ��
2��дһ���ű���������¹��ܣ�
(1) �г���ǰϵͳʶ�𵽵����д����豸
(2) ���������Ϊ1������ʾ��ռ�ʹ����Ϣ
��������ʾ���һ�������ϵĿռ�ʹ����Ϣ
3������reset.sh�ű�



ʵ�飺������ɾ��swap����
1 ����
MBR 82 GPT 8200
dd if=/dev/zero of=/swapfile  bs=1M count=2048
2 
mkswap /dev/sdb1

3
vim /etc/fstab
UUID=xxx   swap swap defaults,pri=N   0 0

4
swapon -a 

5ɾ��
����
swapoff /dev/sdb1

6 vim /etc/fstab
ɾ����Ӧ��

7
fdisk /dev/sdb ɾ����Ӧ����

https://wiki.centos.org/TipsAndTricks/CDtoDVDMedia

ȡIP
centos6
ifconfig eth0|sed  -n -e '2s/^.*r://' -e '2s/ .*$//p' 
centos7
ifconfig ens33 |sed  -n  -e '2s/.*et //' -e '2s/ .*$//p' 
ifconfig ens33 |sed  '2!d;s/.*et //;s/ .*$//'

�޸�httpd.conf�����ļ�
sed   -e '/^#<VirtualHost/,/^#<\/VirtualHost>/s@#@@'  -e '/^#NameVirtualHost/s@#@@' /etc/httpd/conf/httpd.conf

��ϰ
1 sed  -r 's/^[[:space:]]+//' /etc/grub2.cfg
2 sed -r 's/^#[[:space:]]+//' /etc/fstab 
3 sed 's/^/#/' install.log 
4 bug
echo  /etc/sysconfig/network-scripts/ |sed 's@/.*/\<@@'
echo  /etc/sysconfig/-network-scripts/ |sed -r 's@^(.*/)([^/]+/?)@\1@'  ȡĿ¼
echo  /etc/sysconfig/-network-scripts/ |sed -r 's@^(.*/)([^/]+/?)@\2@'	ȡ����

Ǩ��home����������

1 �½�����
2 �����ļ�ϵͳ
3 ��ʱ���ص���ʱĿ¼ /mnt/home
cp��-a /home/*  /mnt/home
4 init 1
5 vim /etc/fstab
UUID=xxx   /home    ext4 defaults 0 0

6 ����home
rm -rf /home/*

7 mount -a 
8 init 3 


ʵ�����

1 vi /etc/fstab
UUID=xxx  /home  ext4 usrquota,grpquota 0 0
mount -o remount /home
2 quotacheck -cug /home
/home/�������ݿ��ļ�
3
setenforce 0
quotaon  /home
4
edquota  wang
/dev/sda6   xxxx  80000 100000  

5 ����
su - wang
dd 



��ҵ��
12
��ϰ
1��ͳ��centos��װ������PackageĿ¼�µ�����rpm�ļ�����.�ָ������ڶ����ֶε��ظ�����
2��ͳ��/etc/init.d/functions�ļ���ÿ�����ʵĳ��ִ�������������grep��sed���ַ����ֱ�ʵ�֣�
sed  's/[^[:alpha:]]/\n/g' /etc/init.d/functions |sed '/^$/d' |sort |uniq -c|sort -n
grep -oE '[[:alpha:]]+' /etc/init.d/functions |sort |uniq -c |sort -n

3�����ı��ļ���n��n+1�кϲ�Ϊһ�У�nΪ������




ʵ�飺�߼���ʵ��

1 pvcreate /dev/sda6 /dev/sdb
2 vgcreate vg0 /dev/sd{a6,b}
3 lvcreate -n lv0  -L 50%vg vg0
lvceate -n lv1  -L 10G vg0
4 mkfs.ext4 /dev/vg0/lv0
5 mount

ʵ�飺��չ

1 vgdisplay �ռ��п���
�޿ռ�
pvcreate  /dev/sda7
vgextend vg0 /dev/sda7
2 lvextend -L 15G  /dev/vg0/lv0
3 xfs_growfs /mnt/lv0
resize2fs /dev/vg0/lv1  15G

����2��3�ϲ�Ϊ
lvextend -r -L +500M /dev/vg0/lv1

ʵ�飺�����߼���

1 umount /mnt/lv1
2 fsck -f /dev/vg0/lv1
3 resize2fs  /dev/vg0/lv1 5G
4 lvreduce -L 5G /dev/vg0/lv1
5 mount -a


ʵ�飺Ǩ���߼���

1 umount /mnt/lv0
2 vgrename vg0 newvg0
3 lvrename /dev/newvg0/lv0 newlv0
4 vgchange -an newvg0
5 vgexport newvg0
6 ���Ӳ��
����Ŀ��������
7 pvscan 
8 vgimport  newvg0
9 vgchange -ay newvg0
10 mkdir /mnt/newlv0��mount /dev/newvg0/newlv0  /mnt/newlv0

ʵ�飺���߼�����ɾ������ʹ�õ����������Ӳ�̣�

1 vgdisplay;pvdisplay
2 pvmove /dev/sda6
3 vgreduce  vg0 /dev/sda6
4 pvremove /dev/sda6


VPN
LANMP PHP PERL 
PYTHON GO


����ʱ��/����ʱ��(����ʱ��+�쳣ʱ��)

99.9999%

BNC

OSI ����ϵͳ����
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



��ҵ��
1������һ�����ÿռ�Ϊ1G��RAID1�豸���ļ�ϵͳΪext4����һ�������̣��������Զ�������/backupĿ¼
2������������Ӳ����ɵĿ��ÿռ�Ϊ2G��RAID5�豸��Ҫ����chunk��СΪ256k���ļ�ϵͳΪext4���������Զ�������/mydataĿ¼
3������һ������������PV��ɵĴ�СΪ20G����Ϊtestvg��VG��Ҫ��PE��СΪ16MB, �����ھ����д�����СΪ5G���߼���testlv��������/usersĿ¼
4���½��û�archlinux��Ҫ�����Ŀ¼Ϊ/users/archlinux������su�л���archlinux�û�������/etc/pam.dĿ¼���Լ��ļ�Ŀ¼
5����չtestlv��7G��Ҫ��archlinux�û����ļ����ܶ�ʧ
6������testlv��3G��Ҫ��archlinux�û����ļ����ܶ�ʧ
7����testlv�������գ������Ի��ڿ��ձ������ݣ��������յĹ���

frame 
packet 
segment 

unicast
multicast
broadcast

ip ipx
accton

���� 
��˫��
ȫ˫��


windows mobile CE

wifi
wapi
2003-

��˹��



�罻
��ȫ
����
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

A��
0.0.0.0 δ֪��ַ �������е�ַ
127 
1-126.A.B.C
������126����һ������������2^24-2=16777214

ǰ8λ����ID����24λ����ID
0XXXXXXX.A.B.C
00000000.A.B.C 
01111111.A.B.C 

6.A.B.C


6.0.0.0 ����ID
6.255.255.255 6������㲥

B:
ǰ16λ����ID����16λ����ID
10XXXXXX.XXXXXXXX.B.C
10000000 128
10111111 191
128-191.A.B.C

��������2^14=16384
һ��B������������2^16-2=65534



C:
ǰ24λ����ID����8λ����ID
110XXXXX.XXXXXXXX.XXXXXXXX.C
11000000 192
11011111 223
192-223.A.B.C
C��������2^21=2097152
һ��C������������2^8-2=254

D:�ಥ
1110XXXX.A.B.C
11100000 224
11101111 239
224-239.A.B.C

E
11110XXX.A.B.C
240-254 

rpm -q vim &>/dev/null || yum install vim -y &> /dev/null

CIDR �������·�ɣ�����ID������ID���ǹ̶�����


�������룺32λ�����ƣ���Ӧ����IDλΪ1����Ӧ����IDΪ0

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


��ʽ��
1 ��������������������������������ɣ�λ����=2^(32-����IDλ��)-2
2 ������=2^�ɱ�����IDλ
3 ����ID��IP��������������
4 CIDR��ʾ��=IP������ID/����IDλ��

172.16.100.200
255.255.224.0

220.199.211.100
255.255.248.0

114.203.188.10/20

1 �������룺255.255.240.0
2 ����ID:114.203.176.0/20

114.203.188.10
255.255.240.0
114.203.176.0

1011xxxx
11110000

3 �����������ж���:2^12-2=4094


201.130.199.100/24 A
201.130.188.100/16 B




A ���ж��Ƿ���B��һ������
��ͬһ���磬ARP
����ͬһ���磬ARP�����ص�MAC

�������MAC��MACA | IPA,IPB|
router
MACB,MACrouter����| IPA,IPB


����B������

160.200.X.Y/16 

������������һ��������ָ���С���磬ÿ��С��������ID���٣�����ID��࣬ԭ���Ĵ���������IDλ������IDλ��λ

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

����ID��160.200.0.0/17
160.200.1 1111111.254
����ID��160.200.128.0/17





160.200.0.0/16 ����16������

16=2^4

��������������32������

1 ����������netmask
255.255.240.0

2 ÿ������������
2^(32-20)-2=4094

3 ��С����������������ID
160.200.0.0/20
160.200.240.0/20

4 ���������IP��Χ
160.200.240.1/20-160.200.255.254/20


160.200.0000    0000.0
160.200.1111    0000.00000001 
160.200.1111    1111.11111110




��ʽ��
1 ��������������������������������ɣ�λ����=2^(32-����IDλ��)-2
2 ������=2^�ɱ�����IDλ
3 ����ID��IP��������������
4 CIDR��ʾ��=IP������ID/����IDλ��
5 ��������������=2^����ID������ID���λ��
6 ������������ʧ��IP��������������������-1��*2

160.200.0.0/16 ����16������
����������160.200.240.0/20����32������
1 ����������netmask
255.255.255.128
2 ÿ������������
2^(32-25)-2=126
3 ��С����������������ID
160.200.240.0/25
160.200.255.128/25
4 ���������IP��Χ
160.200.255.129/25--160.200.255.254/25

160.200.1111 0000.0 0000000
  
160.200.1111 1111.1 0000001   160.200.255.129/25--160.200.255.254/25
160.200.1111 1111.1 1111110   


������������һ��������ָ���С���磬ÿ��С��������ID���٣�����ID��࣬ԭ���Ĵ���������IDλ������IDλ��λ
���ֳ����������С����ϲ�һ�������磬ÿ����������ID��С������ID��࣬ԭ��������IDλ������IDλ��λ


·�ɱ��ɶ���·�ɼ�¼���
ÿ����¼��ɣ�

Ŀ������ID  netmask��Ŀ������ı�ʶ
�ӿڣ�����Ŀ�����籾�豸�ĳ���
���أ�Ŀ������͵�ǰ·����ֱ��������IP=�ӿ�IP����ֱ�ӣ�����IP=��һ��·�������ڽ��Ľӿڵ�IP

DHCP DISCOVER
DHCP OFFER
DHCP REQUEST
DHCP ACK(ip,mask��gateway,release)



IP����
32
Netmask 
CIDR:IP/����IDλ��

��ʽ
1 ������=2^����IDλ��-2
2 ������=2^�ɱ�����IDλ
����������һ���������ֶ��С��������ID������ID��λN������2^N������
3 ������������ʧIP����2*��2^N-1��


·�ɱ�ÿһ��·�����
����ID/netmask �ӿ� ����


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


��ҵ��
1��ÿ���2���12��������/etc������/testdir/backupĿ¼�У�������ļ����Ƹ�ʽΪ��etcbak-yyyy-mm-dd-HH.tar.xz��
2��ÿ��2, 4, 7����/var/log/messages�ļ���/logsĿ¼�У��ļ������硰messages-yyyymmdd��
3��ÿ��Сʱȡ����ǰϵͳ/proc/meminfo�ļ�����S��M��ͷ����Ϣ׷����/tmp/meminfo.txt�ļ���
4��������ʱ�䣬ÿ10����ִ��һ�δ��̿ռ��飬һ�������κη��������ʸ���80%����ִ��wall����
5����д�ű�/root/bin/createuser.sh��ʵ�����¹��ܣ�ʹ��һ���û�����Ϊ���������ָ���������û����ڣ�����ʾ����ڣ��������֮����ʾ��ӵ��û���id�ŵ���Ϣ
6����д�ű�/root/bin/yesorno.sh����ʾ�û�����yes��no,���ж��û��������yes����no,����������Ϣ
7����д�ű�/root/bin/filetype.sh,�ж��û������ļ�·������ʾ���ļ����ͣ���ͨ��Ŀ¼�����ӣ������ļ����ͣ�
8����д�ű�/root/bin/checkint.sh,�ж��û�����Ĳ����Ƿ�Ϊ������
12
��forʵ��
1���ж�/var/Ŀ¼�������ļ�������
2�����10���û�user1-user10������Ϊ8λ����ַ�
3��/etc/rc.d/rc3.dĿ¼�·ֱ��ж����K��ͷ����S��ͷ���ļ����ֱ��ȡÿ���ļ�����K��ͷ�����Ϊ�ļ���stop����S��ͷ�����Ϊ�ļ�����start����K34filename stop S66filename start
4����д�ű�����ʾ����������n��ֵ������1+2+��+n���ܺ�
5������100���������ܱ�3����������֮��

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
 
6����д�ű�����ʾ�����������ַ����192.168.0.0���ж��������������������״̬
7����ӡ�žų˷���

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


8����/testdirĿ¼�´���10��html�ļ�,�ļ�����ʽΪ����N����1��10�������8����ĸ���磺1AbCdeFgH.html
9����ӡ����������


vim sum.sh

#!/bin/bash
sum=0
i=1
while [ $i -le 100 ];do
        let sum+=i
        let i++
done
echo sum=$sum



��ҵ����whileʵ��
1����д�ű�����100��������������֮��
2����д�ű�����ʾ�����������ַ����192.168.0.0���ж��������������������״̬����ͳ�����ߺ���������������
3����д�ű�����ӡ�žų˷���
4����д�ű������ñ���RANDOM����10��������֣�������10���֣�����ʾ���е����ֵ����Сֵ
5����д�ű���ʵ�ִ�ӡ������������
6�����������ַ�����efbaf275cd��4be9c40b8b��44b2395c46��f8c8873ce0��b902c16c8b��ad865d2f63��ͨ�������������RANDOM���ִ�����
echo $RANDOM|md5sum|cut �Cc1-10
��Ľ�������ƽ���Щ�ַ�����Ӧ��RANDOMֵ

��untilʵ��
1��ÿ��3���ӵ�ϵͳ�ϻ�ȡ�Ѿ���¼���û�����Ϣ����������û�hacker��¼���򽫵�¼ʱ���������¼����־/var/log/login.log��,���˳��ű�
2���������10���ڵ����֣�ʵ�ֲ�����Ϸ����ʾ�Ƚϴ��С��������˳�
3�����ļ�����Ϊ������ͳ�����в����ļ���������
4���ö������ϵ�����Ϊ��������ʾ���е����ֵ����Сֵ


while [ $# -gt 0 ] # or (( $# > 0 ))
do
        id $1 &>/dev/null && continue
        useradd $1 && echo $1 is created
        shift
done



for varname in �б� ;do


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



��ҵ��
?��д������ʵ��OS�İ汾�ж�
?��д������ʵ��ȡ����ǰϵͳeth0��IP��ַ
?��д������ʵ�ִ�ӡ��ɫOK�ͺ�ɫFAILED
?��д������ʵ���ж��Ƿ���λ�ò��������޲�������ʾ����


?��д����ű�/root/bin/testsrv.sh���������Ҫ��
(1) �ű��ɽ��ܲ�����start, stop, restart, status
(2) ��������Ǵ�����֮һ����ʾʹ�ø�ʽ�󱨴��˳�
(3) ����start:�򴴽�/var/lock/subsys/SCRIPT_NAME, ����ʾ�������ɹ���
���ǣ���������Ѿ�������һ�Σ�����δ���
(4) ����stop:��ɾ��/var/lock/subsys/SCRIPT_NAME, ����ʾ��ֹͣ��ɡ�
���ǣ����������Ȼֹͣ���ˣ�����δ���
(5) ����restart������stop, ��start
���ǣ��������û��start����δ���
(6) ����status, �����/var/lock/subsys/SCRIPT_NAME�ļ����ڣ�����ʾ��SCRIPT_NAMEis running...��
���/var/lock/subsys/SCRIPT_NAME�ļ������ڣ�����ʾ��SCRIPT_NAME is stopped...��
���У�SCRIPT_NAMEΪ��ǰ�ű���


?��д�ű�/root/bin/copycmd.sh
(1) ��ʾ�û�����һ����ִ����������
(2) ��ȡ�������������������п��ļ��б�
(3) ����������ĳĿ��Ŀ¼(����/mnt/sysroot)�µĶ�Ӧ·���£��磺/bin/bash ==> /mnt/sysroot/bin/bash
/usr/bin/passwd==> /mnt/sysroot/usr/bin/passwd
(4) ���ƴ����������������п��ļ���Ŀ��Ŀ¼�µĶ�Ӧ·���£��磺/lib64/ld-linux-x86-64.so.2 ==> /mnt/sysroot/lib64/ld-linux-x86-64.so.2
(5)ÿ�θ������һ������󣬲�Ҫ�˳���������ʾ�û������µ�Ҫ���Ƶ�������ظ�����������ܣ�ֱ���û�����quit�˳�

��ҵ��
1���������ɸ���ֵ���������У�����ð���㷨���������������
2������ͼ��ʾ��ʵ��ת�þ���matrix.sh
1 2 3 		1 4 7
4 5 6 ===>  2 5 8
7 8 9 		3 6 9



ʵ�飺�߼������£�ɾ��/etc/fstab  /boot

1 rescue mode
blkid 
fdisk -l 
lvscan 
vgchange -ay  �����߼���

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




��ҵ
?1���ƽ�root�����Ϊgrub���ñ�������
?2���ƻ�����grub stage1�������ھ�Ԯģʽ���޸�֮
?3��ɾ��vmlinuz��initramfs�ļ����޷�����,���ַ����ָ�֮
?4��������Ӳ�̣������������ܵ�������kernel��bash��ϵͳ
?5����U���϶���linux��ʹ�������ϵͳ�����������繦��
?6��ɾ��/etc/fstab��/bootĿ¼�������ļ������ָ�֮
?7�����밲װkernel������֧��ntfs�ļ�ϵͳ����


?1������SELinux���Բ���װhttpd���񣬸ı���վ��Ĭ����Ŀ¼Ϊ/website,���SELinux�ļ���ǩ����ʹ��վ�ɷ���
?2���޸�������վ��http�˿�Ϊ9527������SELinux�˿ڱ�ǩ��ʹ��վ�ɷ���
?3��������ص�SELinux����ֵ��ʹ������վ���û�student�ļ�Ŀ¼��ͨ��http����
?4����д�ű�selinux.sh��ʵ�ֿ��������SELinux����

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

��ҵ��
1��ͳ��/etc/fstab�ļ���ÿ���ļ�ϵͳ���ͳ��ֵĴ���
2��ͳ��/etc/fstab�ļ���ÿ�����ʳ��ֵĴ���
3����ȡ���ַ���Yd$C@M05MB%9&Bdh7dq+YVixp3vpw�е���������
4�����DOS������������������web��־���߻�����������������ص�ĳ��IP�������������߶�ʱ��PV�ﵽ100�������÷���ǽ��������Ӧ��IP�����Ƶ��ÿ��5���ӡ�����ǽ����Ϊ��iptables -A INPUT -s IP -j REJECT


��ҵ��
1Ϊ���밲װ��httpd����ʵ��service unit�ļ�
2�ƽ�centos7 ����
3�޸�Ĭ�ϵ������ں�Ϊ�±����ں�
4����ʱ��ʱ����SELinux
5����ʱ����emergencyģʽ
6ж�ر��밲װ�����ں�
7ɾ��/boot  �ָ�֮




LOVEZ

ORYHC

�㷨���� Key

https ssl

A--->B,C,D,E


data---����key1(data)=data'----> ����key2(data')=data

key1=key2  �Գ�
1 key �������� 
2 

key1��=key2  �ǶԳ�

A-->B

A Pa,Sa  
B Pb,Sb

���ݼ��ܣ���˽
data---����Pb(data)=data'----> ����Sb(data')=data

������Դȷ��,����ǩ��
data---����Sa(data)=data'----> ����Pa(data')=data

1G des 2G 4m 8m
1G rsa 1G 1m 64Сʱ


hash
��ϣ ɢ��

hash(data)=digest ժҪ

74391081d998963b21483113143eb172e7a4e5f4

��С��


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



���Ʊ����ļ���Զ��
pscp.pssh  -h ip.txt /app/f1.sh /app
����Զ���ļ�������
pslurp  -h ip.txt -L /app /etc/passwd  pass

��ҵ��
1 ����CA������֤�飬�䷢֤��
2 ����expect�ű��� ʵ���Զ��� ����Կ���Ƶ����Զ�̵�����
3 ssh�˿�ת��
curl  --socks5 127.0.0.1  http://192.168.25.107   
awk '/Failed/{ip[$(NF-3)]++}END{for(i in ip){if(ip[i]>10){system("iptables -A INPUT -s "i" -j REJECT")}}}' /var/log/secure 
sshd_config

ssh -L 1080:smtpserver:25 sshserver

ssh -R 1080:smtpserver:25 sshserver
data--->localhost:1080--->localhost:sshclientport---->sshserver:22 ---->sshserver:smtpclientport ------> smtpserver:25
data--->sshserver:1080--->sshserver:sshserverport--ssh-->localhost:sshclientport ---->localhost:smtpclientport ---smtp---> smtpserver:25

��ҵ
1������centos�û�ֻ�ܹ��ڹ���ʱ��ͨ��sshԶ�����ӱ���
2������ֻ��admins���ڵ��û���ssh������

mkisofs -R -J -T -v --no-emul-boot --boot-load-size 4 --boot-info-table -V "CentOS 6.9 x86_64 boot" -b isolinux/isolinux.bin -c isolinux/boot.cat -o /root/centos6_boot.iso /app/myiso/
/etc/pam.d/program
/lib64/security 

�����˵�ISO
boot:linux ks=http://websrv/ks.cfg
ks.cfg
url --url=ftp://websrv2/pub/

ȫ����ISO

1 ׼������ļ���yumԴ
cp -r /misc/cd  /app/fulliso
rm -rf /app/fulliso/repodata/*
cp /misc/cd/repodata/43d8fd068164b0f042845474d6a22262798b9f0d1f49ad1bf9f95b953089777d-c6-x86_64-comps.xml /app/fulliso/repodata/
cd /app/fulliso
createrepo -g repodata/43d8fd068164b0f042845474d6a22262798b9f0d1f49ad1bf9f95b953089777d-c6-x86_64-comps.xml   ./

find /app/fulliso  -name TRANS.TBL -exec rm {} \;

2 ׼��ks�ļ�
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

3 ���������˵�
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

4 ����ISO�ļ�
 mkisofs -R -J -T -v --no-emul-boot --boot-load-size 4 --boot-info-table -V "CentOS 6.9 autoinstall" -b isolinux/isolinux.bin -c isolinux/boot.cat -o /root/centos6.iso /app/fulliso/


 
tftp

ftp
ftp://192.168.25.107/
-->/var/ftp

pxe:
dhcp tftp  ks.cfg http/ftp/nfs/



ʵ�飺��������PXE�Զ�����װCentOS 7

1��װǰ׼�����رշ���ǽ��SELINUX��DHCP��������̬IP

2��װ��������
yum install httpd tftp-server dhcp syslinux 

3 �����ļ���������yumԴ
systemctl enable httpd
systemctl start httpd
mkdir /var/www/html/centos/7
mount /dev/sr0 /var/www/html/centos/7

4 ׼��kickstart�ļ�
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

5����tftp����
systemctl enable tftp.socket
systemctl start tftp.socket

6����DHCP����
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


7 ׼��PXE����ļ�
mkdir/var/lib/tftpboot/pxelinux.cfg/
cp/usr/share/syslinux/{pxelinux.0,menu.c32} /var/lib/tftpboot/
cp/misc/cd/isolinux/{vmlinuz,initrd.img} /var/lib/tftpboot/
cp/misc/cd/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default


8 ���������˵��ļ�
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

�ļ��б����£�
/var/lib/tftpboot/
������initrd.img
������menu.c32
������pxelinux.0
������pxelinux.cfg
��������default
������vmlinuz



ʵ�飺��centos6ʵ��PXE��װ

׼����iptables ��selinux �ر�
1 ��װ������������
yum install vsftpd tftp-server dhcp syslinux-nonlinux
chkconfig dhcpd on
chkconfig tftp on
service xinetd restart
chkconfig vsftpd on
service vsftpd start
ss -nutlp

2 ׼��yumԴ
mkdir /var/ftp/pub/centos/6 -pv
mount /dev/sr0 /var/ftp/pub/centos/6

3 ׼��ks�ļ�
cp /root/anaconda-ks.cfg /var/ftp/pub/ksdir/ks6-1.cfg
vim /var/ftp/pub/ksdir/ks6-1.cfg
yum install system-config-kickstart
ksvalidator  /var/ftp/pub/ksdir/ks6-1.cfg
chmod +r /var/ftp/pub/ksdir/*.cfg 

4 ����DHCP����
cp /usr/share/doc/dhcp-4.1.1/dhcpd.conf.sample /etc/dhcp/dhcpd.conf 
vim /etc/dhcp/dhcpd.conf
subnet 192.168.25.0 netmask 255.255.255.0 {
        range 192.168.25.10 192.168.25.100;
        next-server 192.168.25.106;
        filename "pxelinux.0";
}
service dhcpd restart

5 ׼��PXE����ļ�

cp /usr/share/syslinux/{pxelinux.0,menu.c32} /var/lib/tftpboot/
cp /misc/cd/images/pxeboot/{initrd.img,vmlinuz} /var/lib/tftpboot/
mkdir pxelinux.cfg
cp /misc/cd/isolinux/isolinux.cfg  /var/lib/tftpboot/pxelinux.cfg/default

6 ׼�������˵�
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


ʵ�飺��centos7ʵ��PXE��װcentos6��centos7

1 �ֱ�׼��centos7��centos6��yum Դ
/var/www.html/centos/{6,7}
mount /dev/sr1 /var/www/html/centos/6

2 ׼��centos6,7�ĸ���ks�ļ�
ls /var/www/html/ksdir
ks6-1.cfg  ks7-1.cfg  ks7-2.cfg

3 ׼��centos6,7�ĸ��Ե��ں��ļ�
mkdir /var/lib/tftpboot/centos{6,7}
cp /var/www/html/centos/7/isolinux/{initrd.img,vmlinuz}  /var/lib/tftpboot/centos7
cp /var/www/html/centos/6/isolinux/{initrd.img,vmlinuz}  /var/lib/tftpboot/centos6

tree /var/lib/tftpboot
/var/lib/tftpboot
������ centos6
��?? ������ initrd.img
��?? ������ vmlinuz
������ centos7
��?? ������ initrd.img
��?? ������ vmlinuz
������ menu.c32
������ pxelinux.0
������ pxelinux.cfg
    ������ default


4 ׼�������˵�

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



ʵ�飺cobbler��װϵͳ

1 ��װ��
yum install cobbler  dhcp
systemctl  enable  cobblerd
systemctl  start  cobblerd
systemctl enable  tftp
systemctl start  tftp
systemctl enable httpd
systemctl start httpd


2 ����cobbler check ��ʾ
1) vim /etc/cobbler/settings 
default_password_crypted: "$1$8ckh4FrM$ayLsgQi85bi8Nt5Gj4Drj/"    #openssl passwd -1 ���ɿ���
next_server: 192.168.25.107
manage_dhcp: 1  
server: 192.168.25.107

systemctl restart cobblerd
cobbler sync

2������dhcpģ���ļ�
vi /etc/cobbler/dhcp.template
subnet 192.168.25.0 netmask 255.255.255.0 {
   range dynamic-bootp        192.168.25.100 192.168.25.254;

cobbler sync 

3) ׼�������ļ��ͺͲ˵�����ļ�
����internet
cobbler get-loaders
����internet
cp /var/lib/tftpboot/{menu.c32,pxelinux.0} /var/lib/cobbler/loaders

3 ����yumԴ
cobbler import --path=/misc/cd --name=centos7.3 --arch=x86_64
cobbler import --path=/misc/cd --name=centos6.9 --arch=x86_64
cobbler distro list
cobbler profile list
4 ����ks
cp  centos6.cfg centos7.cfg /var/lib/cobbler/kickstarts/
cobbler profile remove --name=centos6.9-x86_64
cobbler profile remove --name=centos7.3-x86_64
cobbler profile add --name=centos6.9_desktop --distro=centos6.9-x86_64  --kickstart=/var/lib/cobbler/kickstarts/centos6.cfg
cobbler profile add --name=centos7.3_mini --distro=centos7.3-x86_64  --kickstart=/var/lib/cobbler/kickstarts/centos7.cfg
cobbler sync


https://cobblerserver/cobbler_web


���ֽ�������
����--->IP

ping www.qq.com


ʵ�飺����������ݿ�magedu.com

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

6 ����


ʵ�飺ʵ��HTTPS HSTS
��������̨����

һ̨CA��DNS��һ̨client,һ̨httpd

1 DNS

www.a.com IP

2 httpd mod_ssl

3 CA

4 httpd ��CA���룬CA�䷢֤��

5 httpd

�ĸ��ļ� httpd.key httpd.csr httpd.crt cacert.pem 
/etc/httpd/conf.d/ssl/

6 vim /etc/httpd/conf.d/ssl.conf
SSLCertificateFile /etc/httpd/conf.d/ssl/httpd.crt
SSLCertificateKeyFile /etc/httpd/conf.d/ssl/httpd.key
SSLCACertificateFile /etc/httpd/conf.d/ssl/cacert.pem

7 client 
CA֤�鵼��


vim /etc/httpd/conf/httpd.conf
Header always set Strict-Transport-Security "max-age=15768000"
RewriteEngineon
RewriteRule^(/.*)$ https://%{HTTP_HOST}$1 [redirect=301]


1������httpd��������Ҫ���ṩ�����������Ƶ���������:
(1)www.X.com��ҳ���ļ�Ŀ¼Ϊ/web/vhosts/x��������־Ϊ/var/log/httpd/x.err��������־Ϊ/var/log/httpd/x.access
(2)www.Y.com��ҳ���ļ�Ŀ¼Ϊ/web/vhosts/y��������־Ϊ/var/log/httpd/www2.err��������־Ϊ/var/log/httpd/y.access
(3)Ϊ�������������������Ե���ҳ�ļ�index.html�����ݷֱ�Ϊ���Ӧ��������
(4)ͨ��www.X.com/server-status���httpd����״̬�����Ϣ
2��Ϊ����ĵ�2�����������ṩhttps����ʹ���û�����ͨ��https��ȫ�ķ��ʴ�webվ��
(1)Ҫ��ʹ��֤����֤��֤����Ҫ��ʹ�õĹ���(CN)����(Beijing)������(Beijing)����֯(MageEdu)
(2)���ò���ΪOps��������Ϊwww.Y.com���ʼ�Ϊadmin@Y.com


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


ʵ�飺centos7Դ�����httpd2.4

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
���Է���


ʵ�飺centos6Դ�����httpd2.4

������centos6.9 apr-1.6.2.tar.gz apr-util-1.6.0.tar.gz   httpd-2.4.27.tar.bz2

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

11 ����

ʵ�飺centos6���밲װhttpd2.4����2

cp -r apr-1.6.2 httpd-2.4.27/srclib/apr
cp -r apr-util-1.6.0 httpd-2.4.27/srclib/apr-util
./configure --prefix=/usr/local/httpd24 --enable-so --enable-ssl --enable-cgi --enable-rewrite --with-zlib --with-pcre --with-included-apr --enable-modules=most --enable-mpms-shared=all --with-mpm=prefork


ʹ��httpd-2.4ʵ��
1������httpd����Ҫ��
(1) �ṩ�����������Ƶ�����������
www.a.com
ҳ���ļ�Ŀ¼Ϊ/web/vhosts/www1
������־Ϊ/var/log/httpd/www1/error_log
������־Ϊ/var/log/httpd/www1/access_log
www.b.com
ҳ���ļ�Ŀ¼Ϊ/web/vhosts/www2
������־Ϊ/var/log/httpd/www2/error_log
������־Ϊ/var/log/httpd/www2/access_log
(2) ͨ��www.a.com/server-status�����״̬��Ϣ����Ҫ��ֻ�����ṩ�˺ŵ��û�����
(3) www.a.com������192.168.1.0/24�����е���������

2��Ϊ����ĵ�2�����������ṩhttps����ʹ���û�����ͨ��https��ȫ�ķ��ʴ�webվ��
(1) Ҫ��ʹ��֤����֤��֤����Ҫ��ʹ�ù��ң�CN�����ݣ�Beijing�������У�Beijing������֯Ϊ(MageEdu)
(2) ���ò���ΪOps, ������Ϊwww.b.com


ʵ�飺ʵ��LAMP����дIndex.php������php�����ݿ�

ʵ�飺phpmyadmin 
һ������


ʵ��:centos7.3ʵ��lampӦ��wordpress
����
host1 httpd,php
host2 mariadb 
1 yum install httpd php php-mysql 
yum install mariadb-server
systemctl start httpd
systemctl start mariadb

2 �������ݿ⼰�û�
mysql> create database wpdb;
mysql> grant all  on wpdb.* to wpuser@'%' identified by "wppass";

3 ����Դ
tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /var/www/html/
cd /var/www/html
ln -s wordpress/ blog
setfacl -R -m u:apache:rwx wordpress/

4 http://websrv/blog/


ʵ�飺centos7.3ʵ�ֻ���Դ����밲װLAMP��wordpressӦ��

���������
apr-1.6.2.tar.gz       httpd-2.4.27.tar.bz2                php-7.1.10.tar.xz
apr-util-1.6.0.tar.gz  mariadb-10.2.8-linux-x86_64.tar.gz  wordpress-4.8.1-zh_CN.tar.gz

��̨������һ̨ʵ��LAP ��һ̨ʵ��M

1 Դ����밲װHttpd2.4
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

2 �����ư�װmariadb 
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


3 Դ����밲װPhp
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
���ļ�β��������
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
�޸�������
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>
apachectl stop
apachectl 

4 ����php��mariadb����

vim /app/httpd24/htdocs/index.php 
<html><body><h1> LAMP</h1></body></html>
<?php
$mysqli=new mysqli("localhost","root","centos");
if(mysqli_connect_errno()){
echo "�������ݿ�ʧ��!";
$mysqli=null;
exit;
}
echo "�������ݿ�ɹ�!";
$mysqli->close();
phpinfo();
?>


5 ����wordpress

tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /app/httpd24/htdocs
cd /app/httpd24/htdocs
mv wordpress/ blog/

cd /app/httpd24/htdocs/blog/
cp wp-config-sample.php  wp-config.php
vim wp-config.php
define('DB_NAME', 'wpdb');

/** MySQL���ݿ��û��� */
define('DB_USER', 'wpuser');

/** MySQL���ݿ����� */
define('DB_PASSWORD', 'centos');

/** MySQL���� */
define('DB_HOST', 'localhost');


6 ��¼����
http://websrv/blog 
��������
ab -c 10 -n 100 http://websrv/blog/


ʵ�飺centos6.9ʵ�ֻ���Դ����밲װLAMP(phpģ�鷽ʽ)��wordpressӦ��

����汾��
apr-1.6.2.tar.gz       httpd-2.4.27.tar.bz2                php-5.6.31.tar.xz             xcache-3.2.0.tar.bz2
apr-util-1.6.0.tar.gz  mariadb-5.5.57-linux-x86_64.tar.gz  wordpress-4.8.1-zh_CN.tar.gz

1 ����httpd2.4
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


2 �����ư�װmariadb
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

3 Դ�����php
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
���ļ�β��������
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
�޸�������
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

service httpd24 restart

4 ����
vim /app/httpd24/htdocs/index.php
<html><body><h1>It works!</h1></body></html>
<?php
$mysqli=new mysqli("localhost","root","centos");
if(mysqli_connect_errno()){
echo "�������ݿ�ʧ��!";
$mysqli=null;
exit;
}
echo "�������ݿ�ɹ�!";
$mysqli->close();


$conn = mysql_connect('localhost','root','centos');
if ($conn)
echo "OK";
else
echo "Failure";
mysql_close();

phpinfo();

?>


5 ����wordpress

tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /app/httpd24/htdocs
cd /app/httpd24/htdocs
mv wordpress/ blog/

cd /app/httpd24/htdocs/blog/
cp wp-config-sample.php  wp-config.php
vim wp-config.php
define('DB_NAME', 'wpdb');

/** MySQL���ݿ��û��� */
define('DB_USER', 'wpuser');

/** MySQL���ݿ����� */
define('DB_PASSWORD', 'centos');

/** MySQL���� */
define('DB_HOST', 'localhost');


6 ��¼����
http://websrv/blog 
��������
ab -c 10 -n 100 http://websrv/blog/

7 ����xcache ʵ��Php����
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

8 ����
ab -c 10 -n 100 http://websrv/blog/



ʵ�飺centos6.9ʵ�ֻ���Դ����밲װLAMP(FPMģ�鷽ʽ)��wordpressӦ��

����汾��
apr-1.6.2.tar.gz       httpd-2.4.27.tar.bz2                php-5.6.31.tar.xz             xcache-3.2.0.tar.bz2
apr-util-1.6.0.tar.gz  mariadb-5.5.57-linux-x86_64.tar.gz  wordpress-4.8.1-zh_CN.tar.gz

1 ����httpd2.4
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


2 �����ư�װmariadb
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

3 Դ�����php
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
ȡ�����е�ע��
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so

���ļ�β��������
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
ProxyRequests Off �ر��������
ProxyPassMatch  ^/(.*\.php)$ fcgi://127.0.0.1:9000/app/httpd24/htdocs/$1

�޸�������
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

service httpd24 restart

4 ����
vim /app/httpd24/htdocs/index.php
<html><body><h1>It works!</h1></body></html>
<?php
$mysqli=new mysqli("localhost","root","centos");
if(mysqli_connect_errno()){
echo "�������ݿ�ʧ��!";
$mysqli=null;
exit;
}
echo "�������ݿ�ɹ�!";
$mysqli->close();


$conn = mysql_connect('localhost','root','centos');
if ($conn)
echo "OK";
else
echo "Failure";
mysql_close();

phpinfo();

?>


5 ����wordpress

tar xvf wordpress-4.8.1-zh_CN.tar.gz  -C /app/httpd24/htdocs
cd /app/httpd24/htdocs
mv wordpress/ blog/

cd /app/httpd24/htdocs/blog/
cp wp-config-sample.php  wp-config.php
vim wp-config.php
define('DB_NAME', 'wpdb');

/** MySQL���ݿ��û��� */
define('DB_USER', 'wpuser');

/** MySQL���ݿ����� */
define('DB_PASSWORD', 'centos');

/** MySQL���� */
define('DB_HOST', 'localhost');


6 ��¼����
http://websrv/blog 
��������
ab -c 10 -n 100 http://websrv/blog/

7 ����xcache ʵ��Php����
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

8 ����
ab -c 10 -n 100 http://websrv/blog/


ʵ�飺����TCP��UDPԶ����־

��̨����

1 ����־��������

vim /etc/rsyslog.conf
$ModLoad imudp
$UDPServerRun 514

# Provides TCP syslog reception
$ModLoad imtcp
$InputTCPServerRun 514

2 ��Ӧ�÷�����
vim /etc/rsyslog.conf
local2.*                                        @@192.168.25.107

ʵ�飺ʵ��loganalyzer

��̨����

