---
layout: post
title:  Linux-任务计划
date:   2017-09-12 08:00:00
categories: Linux 
tags:  任务计划  at  cron
---

## 介绍

> &emsp;&emsp; 相信每个人都有使用闹钟的习惯，我们设定闹钟的种类有很多。比如说，只提醒一次、工作日提醒、休息日提醒等。在设定闹钟之后，每天的设定时间都会按时的提醒你去做什么事情，以免自己忘记一些重要的会议等事情。像这样在每天特定的时间安排做一些事情。这样一种事情我们就称之为例行任务计划。
其实在个系统平台上都有类似的例行性任务计划功能，那如何去像设定闹钟一样在Linux系统之上指定例行性任务计划。
&emsp;&emsp; Linux的计划任务是系统管理方面的一个重要内容，是系统自动完成工作的一种实现方式，正因为有了计划任务，我们才可以完全实现系统管理的脚本化和自动化，提高工作效率。
 
> 本文将从以下两个大方面介绍Linux系统中计划任务。  

> -  一次性计划任务--at命令
> -  周期性计划任务--cron命令


## 一次性计划任务--at命令

> 一次性计划任务就是根据用户设定在未来的某个时间点执行一次任务，执行完毕后就结束，本文所有内容皆以CentOS6版本为例。

一、at 命令


```

# 查询命令是否已经启动，at命令依赖于atd服务，包括以下将要介绍的cron的crond服务、anacron服务想要执行相应的计划任务，对应的服务都要开启，查看服务是否开启

service atd status

# 如果命令没有启动的话，将其启动

systemctl start atd

# at主要为以下的几个文件，接下来会介绍到

/etc/at.deny								#at命令的黑名单
/etc/at.allow 								#at命令的白名单
/etc/rc.d/init.d/atd						#at命令的服务名称
/etc/sysconfig/atd							#at的服务
/var/spool/at								#计划任务的暂存目录


```
 
> at 命令的设定格式  `at [-V] [-q queue] [-f file] [-mMlbv] TIME`

> options:

```
		-V: 显示版本信息
		-l: 列出队列中等待运行的作业列表=>等价于atq
		-d: 删除指定的作业；相当于atrm
		-c: 查看具体作业任务
		-f /path/from/somefile:从指定的文件中读取任务
		-m:当任务执行完后，向用户发送邮件，即使没有标准输出

```

> TIME  指定任务的开始执行时间


**TIME的几种表达格式**


```

TIME格式：
		绝对时间：HH:MM，DD.MM.YY ， MM/DD/YY
			eg  ：06:06   
		相对时间：now+#
			单位：minutes, hours, days, weeks
		    eg  ：now+3minutes	
		模糊时间：noon, midnight, teatime
			eg  : noon
```	

**这里说明：作业执行命令的结果中的标准输出和错误以mail通知给安排任务的用户，如果任务非常多需要考虑定期清理邮件内容，防止垃圾过多影响系统稳定运行**	


### at 命令创建计划任务

> 在创建一次性计划任务时，有多中方法下面列举出几种供读者来比较

#### 交互式创建

> 交互式定义计划任务的方式，是最简单的。    
> 这里说明：交互式定义计划任务的时候，需要Ctrl+D正常退出命令   
 
>**举例说明：**

```

#指定时间创建计划任务
[root@Centos6 ~]#at 06:06
at> echo "hello world"
at> <EOT>
job 1 at 2017-09-01 06:06

#使用-l 选项查看计划任务是否设置成功
[root@Centos6 ~]#at -l
1	2017-09-01 06:06 a root

```

#### 输入重定向设定计划任务  

> at命令也可以接受重定向，因此我们按照格式直接设定就好

```

#利用多行重定向
[root@Centos6 ~]#at 06:00 << EOF
> echo "hello world"
> EOF
job 2 at 2017-09-04 06:00


```

#### 从文件中读入

> 与其它许多命令一样，at也可以接受来自文件的输入。

```

#创建一个计划任务文件
[root@centos6 ~]#cat f1
echo "hello world "
echo "hi"

#通过-f选项将文件传给at命令
[root@localhost ~]#at -f f1 05:00

```

#### at 命令 白名单 黑名单


> - 白名单：/etc/at.allow 默认不存在，只有该文件中的用户才能执行at命令
> - 黑名单：/etc/at.deny  默认存在，拒绝该文件中用户执行at命令，而没有在at.deny 文件中的使用者则可执行
> - 如果两个文件都不存在，只有 root 可以执行 at 命令,如果两个文件都存在，则at.deny文件失效，只有at.allow文件中白名单可以执行at命令，不在allow文件中的用户（除了root）,都不能执行at命令。



### 周期性的计划任务 cron
> cron自身是一个不间断运行的程序
> 周期性的计划任务 分为三种： 系统cron任务，anacron任务，用户cron
> anacron 任务:  为cron的补充，能够实现让cron因为各种原因在过去的时间该执行而未执行的任务在恢复正常执行一次，适用于经常开机关机的设备，而像大型的服务器大多处于长时间开机的状态，默认下anacron服务处于关闭状态。
> 以下主要介绍:系统cron以及用户cron
#### 系统的cron

> 系统的计划任务，顾名思义，只有root管理员才能设定的计划任务，其实普通用户也能设定计划任务只是设定的方法有区别，接下来会介绍到
> 系统的cron,定义在```/etc/crontab```文件中，包含7个字段，我们来看一下/etc/crontab内的内容

```

SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root								# 默认向指定用户发送邮件

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)		
# |  .------------- hour (0 - 23)		
# |  |  .---------- day of month (1 - 31)		
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat	
# |  |  |  |  |		.---- username		
# |  |  |  |  | 	|
# *  *  *  *  * user-name  command to be executed
分别表示为：

分钟  小时  天  月  周  用户  命令  

时间的有效取值：
			分钟：0-59
			小时：0-23
			天：1-31
			月：1-12
			周：0-7，0和7都表示周日

注意：
(1) 每一行定义一个周期性任务，共7个字段；
   *  *  *  *  * : 定义周期性时间
   user-name 	 : 运行任务的用户身份
   command to be executed: 任务
 
(2) 此处的环境变量不同于用户登录后获得的环境，因此，建议命令使用绝对路径，或者自定义PATH环境变量；
 
(3) 执行结果邮件发送给MAILTO设定的用户

```

**在/etc/crontab中时间的表示方法**：
    
> 时间表示法：  

>(1) 特定值；   
>   &emsp;&emsp;给定时间点有效取值范围内的值     
>   &emsp;&emsp;注意：day of week和day of month一般不同时使用；  
>(2) *  
>   &emsp;&emsp;给定时间点上有效取值范围内的所有值；表“每..”     
>(3) 离散取值：     
>   &emsp;&emsp;在时间点上使用逗号分隔的多个值； 
>   #,#,#        
>(4) 连续取值：-        
>   &emsp;&emsp;在时间点上使用-连接开头和结束；
>   #-#      
>(5) 在指定时间点上，定义步长: 
>   /#：#即步长；
 
**注意：**

>(1) 指定的时间点不能被整除时，其意义将不复存在；   
>(2) 最小时间单位为“分钟”，想完成“秒”级任务，得需要额外借助于其它机制，例如写成脚本；

**示例：**

```

(1) 3 * * * *       ：每小时执行一次；每小时的第3分钟；
(2) 3 4 * * 5       ：每周执行一次；每周5的4点3分；
(3) 5 6 7 * *       ：每月执行一次；每月的7号的6点5分；
(4) 7 8 9 10 *      ：每年执行一次；每年的10月9号8点7分；
(5) 9 8 * * 3,7     ：每周三和周日；
(6) 0 8,20 * * 3,7  ：每周三和周日；8点整和20点整
(7) 0 9-18 * * 1-5  ：每周一到周五：9点正到18点正
(8) */5 * * * *     ：每5分钟执行一次某任务

```

**这里说明当星期时间和每个月的某天发生冲突时，最终的执行时间为两者的并集**

**系统自带计划任务命令**

> 系统本身自带的计划任务命令，代替了较为简单的设定，比如每天、每月、每小时等等。

```

@reboot 	Run once after reboot.
@yearly 	0 0 1 1 * 
@annually 	0 0 1 1 *
@monthly	0 0 1 * *
@weekly		0 0 * * 0
@daily 		0 0 * * *
@hourly 	0 * * * *

```
#### cron 命令 白名单 黑名单

> &emsp;&emsp;cron也有对应的黑白名单设定文件位置为`/etc/cron.deny` 和 `/etc/cron.allow` ,关于黑名单和白名单与at命令的使用是一致的。

###  用户的cron:

>  &emsp;&emsp;相比系统管理员root设定计划任务来说，用户当然也可以设定自己的计划任务，用户可以通过命令生成任务文件，也可以直接在对应的目录下自己编辑文件（不建议）。

>  &emsp;&emsp;用户创建计划任务的文件位置：/var/spool/cron/USERNAME,如果用户创建了cron任务，在/var/spool/cron/ 下面就会生成一个与用户名一致的临时文件，里面记录了需要执行的计划任务。

> crontab命令的格式(两种)

```

       crontab [-u user] file
       crontab [-u user] [-l | -r | -e] [-i] [-s]
				-l: 列出所有任务；
				-e: 编辑任务；
				-r: 移除所有任务；
				-i：同-r一同使用，以交互式模式移除指定任务
				-u user: 仅root可运行，指定用户管理cron任务
				file : 从相应文件读取计划任务
```
**示例：**

> 用户创建计划任务
> 
> &emsp;&emsp;crontab -u wang  -e 
>
> 查看任务列表
>
> &emsp;&emsp;crontab -l 
> 
> 删除所有任务(交互式)
> 
> &emsp;&emsp;crontab -r -i 
>

**&emsp;&emsp;这里说明一下：只有root用户才能够指定以哪个普通用户的身份来创建，在创建完成后被指定的用户在/etc/spool/cron/下能够找到与自己用户同名的任务文件。**

### anacron 计划任务 （cron的补充任务）

>&emsp;&emsp;前面提到过anacron存在的作用，下面只对其进行简单的介绍，anacron的大致用法与cron差异不大。

> anacron 的文件在`/etc/anacrontab`，我们来看一下

```

# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1	5	cron.daily		nice run-parts /etc/cron.daily
7	25	cron.weekly		nice run-parts /etc/cron.weekly
@monthly 45	cron.monthly		nice run-parts /etc/cron.monthly

分为4个字段，每段表示为：
过去有多少天没有执行   开机后多少分钟开始执行  描述  命令 

```

**&emsp;&emsp;这里说明：开机后系统为了防止存在众多需要执行的计划任务在同一时间执行，影响系统运行速率，便设定了生成随机的开机时间，来解决这个问题**   

**&emsp;&emsp;到此为止系统的计划任务已介绍完成，当然还有不足及错误之处，望提出指正。**