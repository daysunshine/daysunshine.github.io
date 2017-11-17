---
layout: post
title:  redis的同步、sentinel、集群
date:   2017-11-16 09:00:00
categories: Linux 
tags:  复制 主从复制  cluster sentinel
---


### redis同步

> 配置redis的同步、主从复制非常简单，只需在从节点上进行指定主节点的IP地址及端口号就行
> 前提同样需要设定主节点以及从节点能够被互相将听到其开放的端口

> master进行设定

```
bind  0.0.0.0 默认对所有的主机进行开放

#需要开启认证功能，来增加安全性
requirepass   ilinux 

```

> slave进行设定

```
slaveof       指定主节点的地址及服务器
masterauth    如果主节点进行设定需要通过认证，这里填写设定的明文密码即可

#同样也可以在redis的命令行进行设定
redis-cli> SLAVEOF 172.18.18.10 6379
redis-cli> CONFIG SET masterauth ilinux  配置主节点的认证密码

```

> 测试：

> 在master上执行info replication 查看主从同步的信息  

```
127.0.0.1:6379> info replication
# Replication
role:master
connected_slaves:1
slave0:ip=172.18.18.15,port=6379,state=online,offset=687,lag=0
master_repl_offset:687
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:2
repl_backlog_histlen:686

```

> 在从节点slave上同样进行查看  

```
127.0.0.1:6379> info replication
# Replication
role:slave
master_host:172.18.18.10
master_port:6379
master_link_status:up
master_last_io_seconds_ago:8
master_sync_in_progress:0
slave_repl_offset:799
slave_priority:100
slave_read_only:1
connected_slaves:0
master_repl_offset:0
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

```


### sentinel

> sentinel存在的目的就是为了，当主从复制进行数据同步时，主节点宕机后能够实时的监控，并通知管理员，同时还能够进行自动故障转移
> 其中，能够在以主从复制的基础上，通过流言协议，及投票协议，选出一个作为新的主节点，从而实现对redis的守卫功能

> 在redis中sentinel拥有自己独立的服务及配置文件，要能够实现监控的功能，我们需要对主从复制当中的所有主机进行sentinel的设定

> 配置文件如下：编辑/etc/redis-sentinel.conf

```

port 26379  设定的默认端口
bind 0.0.0.0  绑定IP

sentinel monitor <master-name> <ip> <redis-port> <quorum>  连接主节点的主机
sentinel monitor mymaster (代称可以随便指定内部使用) 172.18.18.10 6379  2    数字2 为需要超过2个投票才算
			
sentinel auth-pass <master-name> <password>   需要连接时的认证，与主节点设定的密码相关
sentinel monitor mymaster  ilinux
			
	<quorum>表示sentinel集群的quorum机制，即至少有quorum个sentinel节点同时判定主节点故障时，才认为其真的故障；
	例如有3个sentinel主机，需要有超过2个才行
		s_down: subjectively down
		o_down: objectively down
			
sentinel down-after-milliseconds <master-name> <milliseconds>
	监控到指定的集群的主节点异常状态持续多久方才将标记为“故障”；
sentinel monitor mymaster  30000                     
		
sentinel parallel-syncs <master-name> <numslaves>
	指在failover过程中，能够被sentinel并行配置的从节点的数量；
				
sentinel failover-timeout <master-name> <milliseconds>
	sentinel必须在此指定的时长内完成故障转移操作，否则，将视为故障转移操作失败；
				
sentinel notification-script <master-name> <script-path>
	通知脚本，此脚本被自动传递多个参数；

```

> 设定完成将所有的主机都设定成一样

> 设定成功后通过以下进行查看

```
redis-cli -h SENTINEL_HOST -p SENTINEL_PORT 
	redis-cli> 
		SENTINEL masters
    	SENTINEL slaves <MASTER_NAME>
		SENTINEL failover <MASTER_NAME>
		SENTINEL get-master-addr-by-name <MASTER_NAME>

```

### redis集群

> redis的集群原理是将redis的存储的单元根据需求进行设定分配，我们仅需要对集群内的主机分配好slots即可

> cluster的前提，各个节点无主从复制的其他关系

> 1、编辑/etc/redis.conf

```
#开启集群配置
cluster-enabled  yes

#指定生成的配置文件的名称
cluster-config-file  nodes-6379.conf

#集群节点互连超时的阀值，单位毫秒
cluster-node-timeout  15000

#判断slave是否和master失联时间过长
cluster-slave-validity-factor

```

> 2、启动redis后为每个节点分配slots（槽）

```

#因每个slot都要独立创建，数量较大，不能够一个一个创建，故在命令行进行批量设定
redis-cli -a ilinux -h ip   CLUSTER ADDSLOTS  {0..###} 

```

> redis中一共有16384个hash槽，将槽平均分配至所有的主机当中

> 3、进行各集群成员间的相互确认

```

#进行集群成员的设定
 CLUSTE MEET  IP port 

#设定完成后在redis命令行中进行查看
127.0.0.1:6379> cluster info


```


