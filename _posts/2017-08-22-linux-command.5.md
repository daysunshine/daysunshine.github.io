---
layout: post
title:  RAID��LVM��ʵ��
date:   2017-08-22 08:00:00
categories: Linux 
tags:  RAID  LVM
---


��һ���� : RAID���ܼ�����

- 1��mdadm����

- 2������RAID 5��ɾ��RAID

�ڶ����� : LVM��ʵ�� 

- 1����������

- 2��LVM���������ݺ�����

- 3��LVM����

##RAID���ܼ�����
---

###1��mdadm����

```shell 

mdadm [mode] <raid device> [options] <������豸>
   [mode] 
         -C : ����
    	 -A : װ��
		 -F : ���
		 ����ģʽ
		     -f : ���ָ������Ϊ��
			 -a : ��Ӵ���
			 -r : �Ƴ�����
	 -C : ����ģʽ
	     -n# : ʹ��#���豸��������RAID
		 -l# : ָ��Ҫ������RAID����
		 -a [yes | no] : �Ƿ��Զ������豸�ļ�
		 -c : ָ�����С
		 -x# : ָ�������̸���
		 -D : ��ʾRAID����ϸ��Ϣ

�Ƚ���һ��һЩ����RAID����
	 
     RAID 0
	         ������Ϊ100%
			 ���ݴ�����
			 ��С������ 2
	 RAID 1  
			 ������Ϊ50%
	         ����������
			 ���ٴ����� 2
	 RAID 5  
			 ������Ϊ(n-1)/n ,��һ����Ϊ����
			 ���ݴ�����
			 ���ٴ����� 3
	 RAID 10
			 ������Ϊ50%
			 ���ݴ���������ÿ�龵��ֻ�ܻ�һ��
			 ���ٴ��� 4
```
	
###2������������RAID�豸

     �����Դ���RAID 5Ϊ��
	 
```shell

	 ��ʼ����: ׼��ϵͳ���̣�����С���Ѿ���������,����ϵͳID��ΪRAID����
	 ����: 
	 
		Device Boot         Start         End      Blocks   Id  System
		/dev/sdb1               1         262     2104483+  fd  Linux raid autodetect
		/dev/sdb2             263         524     2104515   fd  Linux raid autodetect
		/dev/sdb3             525         786     2104515   fd  Linux raid autodetect
		/dev/sdb4             787        1048     2104515   fd  Linux raid autodetect

	 (1)�� ��/dev�´���RAID5����Ϊmd0������Ϊ�����Զ�������ģʽΪRAID 5 ʹ��3���豸������RAID��/dev/sdb4��Ϊ����
	     
		 mdadm -C /dev/md0 -a yes -l5 -n3 -x1 /dev/sdb{1,2,3,4}
		 
	 (2)�� �Դ�����md0�豸���и�ʽ��
		 
		 mke2fs -j /dev/md0
		 
	 (3)�� ��/dev/md0���й��أ��Ϳ��Խ���ʹ����

	       ����RAID 5 �ܼ򵥵ľʹ������
	 
	 (4)�� ����ͨ���������鿴RAID���豸״��
	 
	     mdadm -D /dev/md0
		 
		[root@Centos6 ~]#mdadm -D /dev/md0
		/dev/md0 :
         Version : 1.2
   Creation Time : Mon Aug 14 12:44:33 2017
	  Raid Level : raid5						# RAID 5ģʽ
	  Array Size : 4204544 (4.01 GiB 4.31 GB)	 
   Used Dev Size : 2102272 (2.00 GiB 2.15 GB)	# ʹ�õ��豸��С
	Raid Devices : 3                            # RAID �豸��
   Total Devices : 4							# RAID ���豸��
	 Persistence : Superblock is persistent

	 Update Time : Mon Aug 14 12:44:56 2017
		   State : clean                        # �Ƿ��ƻ�
  Active Devices : 3                            # ��豸
 Working Devices : 4							# �����豸
  Failed Devices : 0							# ���豸
   Spare Devices : 1							# �����豸

          Layout : left-symmetric
	  Chunk Size : 512K							# chunk(��)��С

            Name : Centos6.9ymd:0  (local to host Centos6.9ymd)
            UUID : 7d8465ed:f8b2c03b:8e7e2a82:0af865ee
          Events : 18

     Number   Major   Minor   RaidDevice State
        0       8       17        0      active sync   /dev/sdb1  # ��豸
        1       8       18        1      active sync   /dev/sdb2  # ��豸
        4       8       19        2      active sync   /dev/sdb3  # ��豸

        3       8       20        -      spare   /dev/sdb4        # �����豸
	 
	 (5)�� �������ʼ������ʱ��û�б��õ��豸�����ǿ������豸�������³�Ա�������ݴ�����
	     
		 mdadm  -G  /dev/md0  -n4 -a /dev/add
	 
	 (6)�� ���������ļ����Ա����ǽ���ֹͣ���ܹ�����������
	     
		 mdadm -Ds >> /etc/mdadm.conf 
		 
	 (7)�� ֹͣRAID�豸
		 
		 mdadm -S /dev/md0 
		 
	 (8)����������RAID�豸
	     
		 mdadm -As /dev/md0 
```		 
###3����ô�����ú�����ɾ�����أ�
```shell
     
	 (1)�� ����������Ҫ����ж��
	 
		 umount /dev/md0
		 
	 (2)�� ֹͣRAID�豸
	 
	     mdadm -S /dev/md0 
		 
	 (3)�� ɾ�������ļ�
	 
	     rm -rf /etc/mdadm.conf
		 
	 (4)�� ɾ��RAID��������Ա��Ԫ��Ϣ
	 
	     mdadm --zero-superblock /dev/sdb{1,2,3,4}
		 
     (5)�������/etc/fstab�ļ��������˿����Զ����ص���Ϣ��Ҫ����ɾ��
	     
```

## LVM��ʵ��
---

###1����������
 
```shell
    
	 1��������ɾ�������LV
	     
		 pvcreate  device 
		 pvremove  device 
		 
	 2����������VG
	 
		 vgcreate vgname device
		     vgname : Ϊ�Լ�ָ����vg����
			 -s # : ��ָ��PE�Ĵ�С��Ĭ��Ϊ4M 
	     
	    �������
		 
		 vgextend  vgname  device  ��vg�����pv
		 vgreduce  vgname  device  ��vg���Ƴ�pv
         vgchange                  ����vg�Ƿ�����
		 vgremove  device          ɾ��һ��vg
		 vgs | vgdisplay           �鿴vg��Ϣ
	 3�������߼���LV
	 
	     lvcreate  [options] [lvname] device 
		     [options]
			     -L # [+][G,g,T,t,M,m] �ı�LV����
				 -l #% VG              �԰ٷֱ���ʽ�ı�����
				 -n name               ����LV����
		 lvextend          ����LV����
         lvreduce          ����LV����
		 lvremove          ɾ��һ��LV
		 lvresize          ����lv������С
		 lvs | lvdisplay   �鿴lv��Ϣ
	 4������ʹ���߼���
	 
```

###2�����������ǿ�ʼ�߼���Ĵ���
    ���Լ������Ĺ��̼���������г�
����:
```shell 
     
	 1������pv
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
	 2������vg
	     [root@Centos6 ~]#vgcreate vg0 /dev/sdb{1..4}
		 Volume group "vg0" successfully created
		 
		 [root@Centos6 ~]#vgs
		 VG   #PV #LV #SN Attr   VSize VFree 
		 vg0    4   1   0 wz--n- 8.02g 16.00m
     3������lv
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

###3��LV���ݼ�����

####(1)������LV
```shell

     1������ϵͳID��LVM�ķ���
	     ������/dev/sdc1Ϊ��
	 
	 2����/dev/sdc1��������
	     pvcreate /dev/sdc1
     3��������������
	     vgcreate vg0 /dev/sdc1
     4������LV
	     lvextend  -L +#G  /dev/vg0/lv0
	 5��ͬ���ļ�ϵͳ(���ڴ����Ͽ��������ӵ�����ʱ����)
	     resize2fs  /dev/vg0/lv0 #G 
	     
		 Ҳ�ɽ�4��5�ϲ�����������������
		 lvextend -r -L +#G  /dev/vg0/lv0

```
####(2)������LV
```shell
     1��ж���Ѿ����ص�lv
	     umount
	 2��ǿ�ƽ��д��̼��
	     e2fsck -f /dev/vg0/lv0
	 3��ͬ���ļ�ϵͳ
	     resize2fs  /dev/vg0/lv0  #G
     4������LV
	     lvreduce  -L #G  /dev/vg0/lv0 
	 5����������ʹ��
         mount 	 
		 		 
```

###4����ο�����Ǩ�ƾ��鵽��ϵͳ

```shell
     
	 ����Ҫȷ����Ǩ�Ƶľ����Ƿ����¾������Ƴ�ͻ�������ͻ����Ҫ��������
	     vgrename  vg0   newvg0name
	 1����ж�ر����ص��߼���
	     umount
	 2�����þ����е������߼���
	     vgchange -an vg0
     3���þ��鴦�ڵ���״̬
	     vgexport  vg0
	 4���ػ����Ӳ�̣������µ�ϵͳ
	 5������ת�Ƶ�vg0
	     vgimport vg0
	 6�������߼���
	     vgchange -ny vg0 
	 7������ʹ��
	 
```
###5���߼������

���գ�����˼�壬���浱ǰ��״̬���Ա��Ժ��ܹ���ԭ����ǰ��״̬����ԭ�����ﲻ��׸��
```shell

     1��Ϊ���е�LV��������
	     lvcreate  -s  -l 60  -n /dev/vg0/data  -p r /dev/vg0/lv0 
		     -s : ��������
			 -l : ʹ��PE��������Ϊ������ʹ��
			 -n : �������豸����
			 -p r : �趨����Ϊֻ������
     2�����ؿ���
	     mount -o ro /dev/vg0/data  /mnt/snap 
	 3���ָ�����
	     lvconvert --merge  /dev/vg0/data
	 
```

	