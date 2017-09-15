---
layout: post
title:  RAID及LVM的实现
date:   2017-08-22 08:00:00
categories: Linux 
tags:  RAID  LVM
---


第一部分 : RAID介绍及创建

- 1、mdadm工具

- 2、创建RAID 5及删除RAID

第二部分 : LVM的实现 

- 1、基本命令

- 2、LVM创建及扩容和缩减

- 3、LVM快照

##RAID介绍及创建
---

###1、mdadm工具

```shell 

mdadm [mode] <raid device> [options] <任意块设备>
   [mode] 
         -C : 创建
    	 -A : 装配
		 -F : 监控
		 管理模式
		     -f : 标记指定磁盘为损坏
			 -a : 添加磁盘
			 -r : 移除磁盘
	 -C : 创建模式
	     -n# : 使用#个设备来创建此RAID
		 -l# : 指明要创建的RAID级别
		 -a [yes | no] : 是否自动创建设备文件
		 -c : 指明块大小
		 -x# : 指明空闲盘个数
		 -D : 显示RAID的详细信息

先介绍一下一些常见RAID阵列
	 
     RAID 0
	         利用率为100%
			 无容错能力
			 最小磁盘数 2
	 RAID 1  
			 利用率为50%
	         有冗余能力
			 最少磁盘数 2
	 RAID 5  
			 利用率为(n-1)/n ,有一块作为备用
			 有容错能力
			 最少磁盘数 3
	 RAID 10
			 利用率为50%
			 有容错能力，但每组镜像只能坏一块
			 最少磁盘 4
```
	
###2、创建并定义RAID设备

     这里以创建RAID 5为例
	 
```shell

	 开始工作: 准备系统磁盘，这里小编已经先做好了,并将系统ID改为RAID类型
	 如下: 
	 
		Device Boot         Start         End      Blocks   Id  System
		/dev/sdb1               1         262     2104483+  fd  Linux raid autodetect
		/dev/sdb2             263         524     2104515   fd  Linux raid autodetect
		/dev/sdb3             525         786     2104515   fd  Linux raid autodetect
		/dev/sdb4             787        1048     2104515   fd  Linux raid autodetect

	 (1)、 在/dev下创建RAID5命名为md0，且设为开机自动创建，模式为RAID 5 使用3个设备创建此RAID，/dev/sdb4作为备用
	     
		 mdadm -C /dev/md0 -a yes -l5 -n3 -x1 /dev/sdb{1,2,3,4}
		 
	 (2)、 对创建的md0设备进行格式化
		 
		 mke2fs -j /dev/md0
		 
	 (3)、 将/dev/md0进行挂载，就可以进行使用了

	       到此RAID 5 很简单的就创建完毕
	 
	 (4)、 可以通过命令来查看RAID的设备状况
	 
	     mdadm -D /dev/md0
		 
		[root@Centos6 ~]#mdadm -D /dev/md0
		/dev/md0 :
         Version : 1.2
   Creation Time : Mon Aug 14 12:44:33 2017
	  Raid Level : raid5						# RAID 5模式
	  Array Size : 4204544 (4.01 GiB 4.31 GB)	 
   Used Dev Size : 2102272 (2.00 GiB 2.15 GB)	# 使用的设备大小
	Raid Devices : 3                            # RAID 设备数
   Total Devices : 4							# RAID 总设备数
	 Persistence : Superblock is persistent

	 Update Time : Mon Aug 14 12:44:56 2017
		   State : clean                        # 是否被破坏
  Active Devices : 3                            # 活动设备
 Working Devices : 4							# 工作设备
  Failed Devices : 0							# 损坏设备
   Spare Devices : 1							# 空闲设备

          Layout : left-symmetric
	  Chunk Size : 512K							# chunk(块)大小

            Name : Centos6.9ymd:0  (local to host Centos6.9ymd)
            UUID : 7d8465ed:f8b2c03b:8e7e2a82:0af865ee
          Events : 18

     Number   Major   Minor   RaidDevice State
        0       8       17        0      active sync   /dev/sdb1  # 活动设备
        1       8       18        1      active sync   /dev/sdb2  # 活动设备
        4       8       19        2      active sync   /dev/sdb3  # 活动设备

        3       8       20        -      spare   /dev/sdb4        # 空闲设备
	 
	 (5)、 如若在最开始创建的时候并没有备用的设备，我们可以在设备中增添新成员，增加容错能力
	     
		 mdadm  -G  /dev/md0  -n4 -a /dev/add
	 
	 (6)、 生成配置文件，以备我们将其停止后能够重新启动它
	     
		 mdadm -Ds >> /etc/mdadm.conf 
		 
	 (7)、 停止RAID设备
		 
		 mdadm -S /dev/md0 
		 
	 (8)、重新启动RAID设备
	     
		 mdadm -As /dev/md0 
```		 
###3、那么创建好后，怎样删除它呢？
```shell
     
	 (1)、 首先我们需要将其卸载
	 
		 umount /dev/md0
		 
	 (2)、 停止RAID设备
	 
	     mdadm -S /dev/md0 
		 
	 (3)、 删除配置文件
	 
	     rm -rf /etc/mdadm.conf
		 
	 (4)、 删除RAID，清除其成员的元信息
	 
	     mdadm --zero-superblock /dev/sdb{1,2,3,4}
		 
     (5)、如果在/etc/fstab文件中增加了开机自动加载的信息则还要将其删除
	     
```

## LVM的实现
---

###1、基本命令
 
```shell
    
	 1、创建及删除物理卷LV
	     
		 pvcreate  device 
		 pvremove  device 
		 
	 2、创建卷组VG
	 
		 vgcreate vgname device
		     vgname : 为自己指定的vg名称
			 -s # : 可指定PE的大小，默认为4M 
	     
	    管理卷组
		 
		 vgextend  vgname  device  在vg内添加pv
		 vgreduce  vgname  device  在vg内移除pv
         vgchange                  配置vg是否启动
		 vgremove  device          删除一个vg
		 vgs | vgdisplay           查看vg信息
	 3、创建逻辑卷LV
	 
	     lvcreate  [options] [lvname] device 
		     [options]
			     -L # [+][G,g,T,t,M,m] 改变LV容量
				 -l #% VG              以百分比形式改变容量
				 -n name               设置LV名字
		 lvextend          增加LV容量
         lvreduce          削减LV容量
		 lvremove          删除一个LV
		 lvresize          调整lv容量大小
		 lvs | lvdisplay   查看lv信息
	 4、挂载使用逻辑卷
	 
```

###2、接下来我们开始逻辑卷的创建
    将自己创建的过程及相关命令列出
如下:
```shell 
     
	 1、创建pv
	     [root@Centos6 ~]#pvcreate /dev/sdb{1..4}
		 Physical volume "/dev/sdb1" successfully created
		 Physical volume "/dev/sdb2" successfully created
		 Physical volume "/dev/sdb3" successfully created
		 Physical volume "/dev/sdb4" successfully created
		 
		 [root@Centos6 ~]#pvs
		 PV         VG   Fmt  Attr PSize PFree 
		 /dev/sdb1  vg0  lvm2 a--u 2.00g     0 
		 /dev/sdb2  vg0  lvm2 a--u 2.00g     0 
		 /dev/sdb3  vg0  lvm2 a--u 2.00g     0 
		 /dev/sdb4  vg0  lvm2 a--u 2.00g 16.00m
	 2、创建vg
	     [root@Centos6 ~]#vgcreate vg0 /dev/sdb{1..4}
		 Volume group "vg0" successfully created
		 
		 [root@Centos6 ~]#vgs
		 VG   #PV #LV #SN Attr   VSize VFree 
		 vg0    4   1   0 wz--n- 8.02g 16.00m
     3、创建lv
		 [root@Centos6 ~]#lvcreate -L +8G -n lv0 vg0
		 Logical volume "lv0" created.
		 
		 [root@Centos6 ~]#lvdisplay 
		 --- Logical volume ---
		 LV Path                /dev/vg0/lv0
		 LV Name                lv0
		 VG Name                vg0
		 LV UUID                NfI3VT-QXop-Ju9V-qdzT-jZOj-Okmz-lcDAAf
		 LV Write Access        read/write
		 LV Creation host, time Centos6.9ymd, 2017-08-17 06:11:37 +0800
		 LV Status              available
 		 # open                 0
		 LV Size                8.00 GiB
		 Current LE             2048
		 Segments               4
		 Allocation             inherit
		 Read ahead sectors     auto
		 - currently set to     256
		 Block device           253:0
	  		 
``` 

###3、LV扩容及缩减

####(1)、扩容LV
```shell

     1、创建系统ID是LVM的分区
	     这里以/dev/sdc1为例
	 
	 2、将/dev/sdc1变成物理卷
	     pvcreate /dev/sdc1
     3、将其加入卷组中
	     vgcreate vg0 /dev/sdc1
     4、扩容LV
	     lvextend  -L +#G  /dev/vg0/lv0
	 5、同步文件系统(当在磁盘上看不到增加的容量时可用)
	     resize2fs  /dev/vg0/lv0 #G 
	     
		 也可将4、5合并成以下命令来代替
		 lvextend -r -L +#G  /dev/vg0/lv0

```
####(2)、缩减LV
```shell
     1、卸载已经挂载的lv
	     umount
	 2、强制进行磁盘检测
	     e2fsck -f /dev/vg0/lv0
	 3、同步文件系统
	     resize2fs  /dev/vg0/lv0  #G
     4、缩减LV
	     lvreduce  -L #G  /dev/vg0/lv0 
	 5、挂载重新使用
         mount 	 
		 		 
```

###4、如何跨主机迁移卷组到新系统

```shell
     
	 首先要确定被迁移的卷组是否与新卷组名称冲突，如果冲突则先要更改名字
	     vgrename  vg0   newvg0name
	 1、先卸载被挂载的逻辑卷
	     umount
	 2、禁用卷组中的所有逻辑卷
	     vgchange -an vg0
     3、让卷组处于导出状态
	     vgexport  vg0
	 4、关机拆除硬盘，出入新的系统
	 5、导入转移的vg0
	     vgimport vg0
	 6、激活逻辑卷
	     vgchange -ny vg0 
	 7、挂载使用
	 
```
###5、逻辑卷快照

快照，顾名思义，保存当前的状态，以备以后能够还原到当前的状态，其原理这里不再赘述
```shell

     1、为现有的LV创建快照
	     lvcreate  -s  -l 60  -n /dev/vg0/data  -p r /dev/vg0/lv0 
		     -s : 创建快照
			 -l : 使用PE数量，作为快照区使用
			 -n : 快照区设备名称
			 -p r : 设定快照为只读属性
     2、挂载快照
	     mount -o ro /dev/vg0/data  /mnt/snap 
	 3、恢复快照
	     lvconvert --merge  /dev/vg0/data
	 
```

	