---
layout: post
title:  自动化系统安装
date:   2017-09-11 12:00:00
categories: Linux 
tags:  自动化安装，制作U盘启动
---


#### 自动化安装centos6系统

> 我们先看一下，与启动有关的文件

> 在光盘的isolinux文件夹中

```shell

[root@Centos6 /misc/cd/isolinux]#ll

total 45306
-r--r--r--. 1 root root     2048 Mar 29 02:24 boot.cat 		#MBR
-r--r--r--. 2 root root       84 Mar 29 02:19 boot.msg		
-r--r--r--. 2 root root      321 Mar 29 02:19 grub.conf		
-r--r--r--. 4 root root 41587792 Mar 29 02:19 initrd.img	
-r--r--r--. 2 root root    24576 Mar 29 02:19 isolinux.bin	#启动stage2阶段文件
-r--r--r--. 2 root root      923 Mar 29 02:19 isolinux.cfg	#启动菜单选项文件
-r--r--r--. 2 root root   183012 Mar 29 02:19 memtest		
-r--r--r--. 2 root root   151230 Mar 29 02:19 splash.jpg
-r--r--r--. 1 root root     2215 Mar 29 02:24 TRANS.TBL
-r--r--r--. 2 root root   163728 Mar 29 02:19 vesamenu.c32
-r-xr-xr-x. 4 root root  4274992 Mar 29 02:19 vmlinuz		#内核文件

```

> 我们看一下，自动安装的过程中，重要的文件isolinux.cfg

```

[root@Centos6 /misc/cd/isolinux]#cat isolinux.cfg 

default vesamenu.c32
...
部分省略
...
#默认的选项，安装或升级系统
label linux 
  menu label ^Install or upgrade an existing system
  menu default
  kernel vmlinuz
  append initrd=initrd.img
#通过光盘安装
label vesa
  menu label Install system with ^basic video driver
  kernel vmlinuz
  append initrd=initrd.img nomodeset
#救援模式
label rescue
  menu label ^Rescue installed system
  kernel vmlinuz
  append initrd=initrd.img rescue
#本地安装启动
label local
  menu label Boot from ^local drive
  localboot 0xffff
#内存检测
label memtest86
  menu label ^Memory test
  kernel memtest
  append -

```

### 自动安装配置文件kickstart

> anaconda-ks.cfg文件

```shell

命令段：指明各种安装前配置，如键盘类型等

程序包段：指明要安装的程序包组或程序包，不安装的程序包等
	%packages
	@group_name
	package
	-package
	%end
脚本段：
	%pre: 安装前脚本
	运行环境：运行于安装介质上的微型Linux环境
	%end
	%post: 安装后脚本
	运行环境：安装完成的系统
	%end

命令段中的命令:
 必备命令
	authconfig: 认证方式配置
	authconfig --useshadow --passalgo=sha512
	bootloader：bootloader的安装位置及相关配置
	bootloader --location=mbr --driveorder=sda –
	append="crashkernel=auto rhgb quiet"
	keyboard: 设定键盘类型
	lang: 语言类型
	part: 创建分区
	rootpw: 指明root的密码
	timezone: 时区
可选命令
	install OR upgrade
	text: 文本安装界面
	network
	firewall
	selinux
	halt
	poweroff
	reboot 
	repo
	user：安装完成后为系统创建新用户
	url: 指明安装源
	key–skip 跳过安装号码,适用于rhel版本
	
```	
	
### kickstart的创建

> 在创建kickstart的方式可以自己参照系统手动编辑anaconda-ks.cfg文件，也可以在图形方式下通过命令来创建

> 命令为 : system-config-kickstart 根据anaconda-ks.cfg文件生成新的配置文件
	
> 生成后可进行语法检查：
> 命令为：ksvalidator /PATH/TO/KICKSTART_FILE
> 创建过程流程图如下:

![kickstart](https://thumbnail0.baidupcs.com/thumbnail/fb498516824af03bfbf057fe540e4015?fid=2905626949-250528-880812320715766&time=1505440800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-BLPAeh3XBLFkcoKJnHCpXBXsU0M%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5971078760552895348&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

![kickstart](https://thumbnail0.baidupcs.com/thumbnail/bb749d0119bc88f9e6dad83f0804421d?fid=2905626949-250528-523262926247992&time=1505440800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-JCYofNPToZoGp9q0uduzmuWJbEY%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5971118617113856162&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

![kickstart](https://thumbnail0.baidupcs.com/thumbnail/58e75a9899e7eed68fffe79bc76357c4?fid=2905626949-250528-655792006635778&time=1505440800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-5C941TijflMtcHZTqyfZygP%2B%2Fi0%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5971129876646730260&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

![kickstart](https://thumbnail0.baidupcs.com/thumbnail/4a466adf9d69d3b09c27c9e9103b683e?fid=2905626949-250528-583033303383530&time=1505440800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-MeKIPrQIMeepp0BH1AVPVqgezg8%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5971142678909553542&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

![kickstart](https://thumbnail0.baidupcs.com/thumbnail/f8bc242fbd66e2babbb6b3094ab5bd1f?fid=2905626949-250528-36738500412313&time=1505440800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-m2E7Y%2BrtZ0YnfF%2F5NgryBBpck%2FA%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5971151641357547994&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

> 将ks.cfg放在ftp的pub的目录下
> 在系统启动时，指定boot启动，按tab键在其后键入ks文件的位置，就会读取自动安装的配置文件执行

![指定ks.cfg路径](https://thumbnail0.baidupcs.com/thumbnail/6366cabd8f2f0cdab1ef9eadd92161d9?fid=2905626949-250528-127105636132607&time=1505444400&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-bbvVQhtH9jxJ1AWrMTZiT86Xju4%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5971223596092496775&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)


**创建完毕**


### 制作U盘启动盘

> #### 编辑自定义的isolinux.cfg

```shell

[root@Centos6 /app/mini/isolinux]#cat isolinux.cfg
 
default vesamenu.c32
#prompt 1
timeout 600

display boot.msg

menu background splash.jpg
menu title Welcome to CentOS 6.9!
menu color border 0 #ffffffff #00000000
menu color sel 7 #ffffffff #ff000000
menu color title 0 #ffffffff #00000000
menu color tabmsg 0 #ffffffff #00000000
menu color unsel 0 #ffffffff #00000000
menu color hotsel 0 #ff000000 #ffffffff
menu color hotkey 7 #ffffffff #ff000000
menu color scrollbar 0 #ffffffff #00000000

label linux
  menu label ^Install or upgrade an existing system
  kernel vmlinuz
  append initrd=initrd.img ks=ftp://172.18.18.18/centos/6   #指定ks.cfg文件的位置，放在ftp的服务器上的
label local
  menu default												#设定为默认的启动选项
  menu label Boot from ^local drive
  localboot 0xffff

```

> 将isolinux的目录存放在自己指定的目录下，这里指定为/app/mini/下

> 将/app/mini/下的文件制作生成光盘镜像，通过命令实现，命令如下:

```shell

[root@Centos6 /app/mini/isolinux]#mkisofs -R -J -T -v --no-emul-boot --boot-load-size 4 --boot-info-table -V "CentOS 6.9 x86_64 boot" -b isolinux/isolinux.bin -c isolinux/boot.cat -o /root/centos6_boot.iso /app/mini/

I: -input-charset not specified, using utf-8 (detected in locale settings)
genisoimage 1.1.9 (Linux)
Scanning /app/mini/
Scanning /app/mini/isolinux
Excluded by match: /app/mini/isolinux/boot.cat
Excluded: /app/mini/isolinux/TRANS.TBL
Writing:   Initial Padblock                        Start Block 0
Done with: Initial Padblock                        Block(s)    16
Writing:   Primary Volume Descriptor               Start Block 16
Done with: Primary Volume Descriptor               Block(s)    1
Writing:   Eltorito Volume Descriptor              Start Block 17
Size of boot image is 4 sectors -> No emulation
Done with: Eltorito Volume Descriptor              Block(s)    1
Writing:   Joliet Volume Descriptor                Start Block 18
Done with: Joliet Volume Descriptor                Block(s)    1
Writing:   End Volume Descriptor                   Start Block 19
Done with: End Volume Descriptor                   Block(s)    1
Writing:   Version block                           Start Block 20
Done with: Version block                           Block(s)    1
Writing:   Path table                              Start Block 21
Done with: Path table                              Block(s)    4
Writing:   Joliet path table                       Start Block 25
Done with: Joliet path table                       Block(s)    4
Writing:   Directory tree                          Start Block 29
Done with: Directory tree                          Block(s)    2
Writing:   Joliet directory tree                   Start Block 31
Done with: Joliet directory tree                   Block(s)    2
Writing:   Directory tree cleanup                  Start Block 33
Done with: Directory tree cleanup                  Block(s)    0
Writing:   Extension record                        Start Block 33
Done with: Extension record                        Block(s)    1
Writing:   The File(s)                             Start Block 34
 21.93% done, estimate finish Wed Sep 13 23:21:41 2017
 43.79% done, estimate finish Wed Sep 13 23:21:41 2017
 65.71% done, estimate finish Wed Sep 13 23:21:41 2017
 87.57% done, estimate finish Wed Sep 13 23:21:41 2017
Total translation table size: 4483
Total rockridge attributes bytes: 1360
Total directory bytes: 2048
Path table size(bytes): 26
Done with: The File(s)                             Block(s)    22658
Writing:   Ending Padblock                         Start Block 22692
Done with: Ending Padblock                         Block(s)    150
Max brk space used 0
22842 extents written (44 MB)

```

> 以上命令的选项不做过多介绍，只是引用完成工作即可 

> 我们看一下生成的文件在root的家目录下

```shell

[root@Centos6 ~]#ls

Desktop    	Downloads  Pictures  Templates  centos6_boot.iso 
bin  		Documents  Music   	 Public     Videos      

```

> 接下来将生成的centos6_boot.iso的镜像文件，放到光盘当中，命令如下:

```shell
dd if=centos6_boot.iso of=/dev/sdb  (U盘的位置) 
```

**在需要重新安装系统的时候。将U盘插入，选中光盘启动即可**

#### 以上只是简单的介绍了系统的自动化安装，还有许多不足之处，敬请谅解！




































