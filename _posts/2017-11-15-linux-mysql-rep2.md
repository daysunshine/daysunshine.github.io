---
layout: post
title:  mysql同步
date:   2017-11-15 08:00:00
categories: Linux 
tags:  主从模型 主主模型 半同步复制
---


### 主从复制

> 主节点将事件记录于二进制日志文件，从节点通过IO_THREAD同步二进制日志文件到中继日志当中，并通过SQL_THREAD将事件进行重放，完成数据的同步功能

> master配置文件设定

```
[server]
skip_name_resolve = on    	不进行解析主机名
innodb_file_per_table = on	每个表为单独的空间存放
server_id=1					服务编号，主从不能相同
log_bin=log-bin				开启二进制日志文件

```

> 设定好配置文件后进行启动服务

```
#对从服务器进行授权
MariaDB [(none)]> GRANT REPLICATION SLAVE,REPLICATION CLIENT ON *.* TO 'repuser'@'172.18.18DENTIFIED BY 'centos';

#重新进行权限更新
MariaDB [(none)]> FLUSH PRIVILEGES;

```

> 查看master的此时的数据存放位置,以便从服务器能够进行设置从什么地方进行同步

```
MariaDB [(none)]> show master status;
+----------------+----------+--------------+------------------+
| File           | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+----------------+----------+--------------+------------------+
| log-bin.000004 |      494 |              |                  |
+----------------+----------+--------------+------------------+
1 row in set (0.00 sec)

```


> slave配置文件的设定

```
[server]
skip_name_resolve = ON
innodb_file_per_table = ON
server_id=2
read_only=ON				设定为只读模式
relay_log=relay-log

```

```
MariaDB [(none)]> CHANGE MASTER TO MASTER_HOST='172.18.18.10',MASTER_USER='repuser',MASTER_PASSWORD='centos',MASTER_LOG_FILE='log-bin.000004',MASTER_LOG_POS=494;

#启动slave服务
MariaDB [(none)]> START SLAVE [IO_THREAD|SQL_THREAD];  不指定启动哪个线程默认都会启动

```

> 查看此时的slave的状态
> Slave_IO_Running和Slave_SQL_Running需要都为Yes，表示主从复制成功
            

```
MariaDB [(none)]> show slave status\G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 172.18.18.10
                  Master_User: repuser
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: log-bin.000004
          Read_Master_Log_Pos: 494
               Relay_Log_File: relay-log.000002
                Relay_Log_Pos: 527
        Relay_Master_Log_File: log-bin.000004
             Slave_IO_Running: Yes	同步线程已经开启
            Slave_SQL_Running: Yes	重放线程已经开启
						...
						部分省略
						...
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 494  当前的master的二进制日志记录位置
              Relay_Log_Space: 815	中继日志的空间
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No   SSL可以更安全的进行数据传输
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 0   当前从服务器落后master多少
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
  Replicate_Ignore_Server_Ids: 
             Master_Server_Id: 1
1 row in set (0.00 sec)

```

> 测试：

> 在master服务器上进行数据的变更，会在slave服务器上进行同步，实现了数据同步的效果


### 主主复制

> 互为主从：两个节点各自都要开启binlog和relay log，同时不能设定read_only

```
	1、数据不一致；为了防止主主互相进行同步数据时ID号相同导致数据相互冲突,需要定义各自的ID号
	2、自动增长id；
		定义一个节点使用奇数id
			auto_increment_offset=1     设定初始值为1
			auto_increment_increment=2  设定增长的步长为2
		另一个节点使用偶数id
			auto_increment_offset=2
			auto_increment_increment=2
```			

> 配置：

```
	1、server_id必须要使用不同值； 
	2、均启用binlog和relay log；
	3、存在自动增长id的表，为了使得id不相冲突，需要定义其自动增长方式；

```			
> 服务启动后执行如下两步：

```
	4、都授权有复制权限的用户账号；
	5、各把对方指定为主节点；
```

> 在复制时需要注意的问题

```
1、从服务设定为“只读”；
	在从服务器启动read_only，但仅对非SUPER权限的用户有效；  
			
	阻止所有用户：
		mysql> FLUSH TABLES WITH READ LOCK;
				
2、尽量确保复制时的事务安全
	在master节点启用参数：
		sync_binlog = ON 每当事务提交时就同步二进制日志
				
		如果用到的是InnoDB存储引擎：
			innodb_flush_logs_at_trx_commit=ON  将master.info改变时立即同步到磁盘上，防止数据不一致
			innodb_support_xa=ON
					
3、从服务器意外中止时尽量避免自动启动复制线程
				
		
4、从节点：设置参数
	sync_master_info=ON
	sync_relay_log_info=ON

```

### 半同步复制

> 半同步复制的目的是为了解决在同步时由于各种原因主从同步时存在延时，而导致数据不能及时的复制，在出现主服务宕机时，部分事务会丢失，所以
> 半同步复制，就是一部分是完全同步与master服务器，其他的为异步服务器。并在主节点宕机后能够将从节点的服务器，提升为主节点，并将落后的事务
> 通过所有的从节点上进度事务进行取并集，并将提升为主节点的服务器没有的数据进行更新，与原主节点的服务器的数据一致


> master的配置文件设置
> 基于主从复制，增加相应的半同步对应的模块即可实现

> 与半同步复制相关的模块为
	- semisync_master.so
	- semisync_slave.so

> 1、对于主节点来进行设定为：

```

#添加master模块
MariaDB [(none)]> INSTALL PLUGIN rpl_semi_sync_master SONAME 'semisync_master.so';
Query OK, 0 rows affected (0.02 sec)

```

> 查看对应的模块信息

```
MariaDB [(none)]> show global variables like '%rpl%';
+------------------------------------+-------+
| Variable_name                      | Value |
+------------------------------------+-------+
| rpl_semi_sync_master_enabled       | OFF   |    	主节点上半同步复制是否启动
| rpl_semi_sync_master_timeout       | 10000 |		超时时间
| rpl_semi_sync_master_trace_level   | 32    |		
| rpl_semi_sync_master_wait_no_slave | ON    |
+------------------------------------+-------+
5 rows in set (0.00 sec)

```

> 对应的全局变量

```
#关于半同步复制的相关信息
MariaDB [(none)]> show global status like '%rpl%';
+--------------------------------------------+-------------+
| Variable_name                              | Value       |
+--------------------------------------------+-------------+
| Rpl_semi_sync_master_clients               | 0           |	从服务器的数量
| Rpl_semi_sync_master_net_avg_wait_time     | 0           |	平均时长
| Rpl_semi_sync_master_net_wait_time         | 0           |	等待时长
| Rpl_semi_sync_master_net_waits             | 0           |
| Rpl_semi_sync_master_no_times              | 0           |
| Rpl_semi_sync_master_no_tx                 | 0           |
| Rpl_semi_sync_master_status                | OFF         |	主服务器是否启用
| Rpl_semi_sync_master_timefunc_failures     | 0           |
| Rpl_semi_sync_master_tx_avg_wait_time      | 0           |
| Rpl_semi_sync_master_tx_wait_time          | 0           |
| Rpl_semi_sync_master_tx_waits              | 0           |
| Rpl_semi_sync_master_wait_pos_backtraverse | 0           |
| Rpl_semi_sync_master_wait_sessions         | 0           |
| Rpl_semi_sync_master_yes_tx                | 0           |
| Rpl_status                                 | AUTH_MASTER |
+--------------------------------------------+-------------+
15 rows in set (0.00 sec)

```

> 2、启用半同步复制功能

```
MariaDB [(none)]> SET @@global.rpl_semi_sync_master_enabled=ON;

```


> 3、从节点设置

> 安装模块

```
MariaDB [(none)]> INSTALL PLUGIN rpl_semi_sync_slave SONAME 'semisync_slave.so';

```

> 查看参数

```
MariaDB [(none)]> show global variables like '%rpl%';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| rpl_semi_sync_slave_enabled     | OFF   |
| rpl_semi_sync_slave_trace_level | 32    |
+---------------------------------+-------+
```

> 4、启用从节点半同步复制功能

```
MariaDB [(none)]> SET @@global.rpl_semi_sync_slave_enabled=ON;
```

> 这里还需要将IO_THREAD线程从新启动才会能够同步主节点的二进制日志文件

```
MariaDB [(none)]> STOP SLAVE IO_THREAD;
MariaDB [(none)]> START SLAVE IO_THREAD;

```

> 5、查看是否启用成功

```

MariaDB [(none)]> show global variables like '%rpl%';
+---------------------------------+-------+
| Variable_name                   | Value |
+---------------------------------+-------+
| rpl_recovery_rank               | 0     |
| rpl_semi_sync_slave_enabled     | ON    |
| rpl_semi_sync_slave_trace_level | 32    |
+---------------------------------+-------+

```

> 测试：在主节点进行更改数据库的操作，就会相应的在从服务器上实现数据的同步

```
#主节点上插入两个数据
MariaDB [you]> insert into you.students(name,age) values ('student56',63),('student31',41);

#查询半同步变量的记录状态
MariaDB [you]> show global status like '%rpl%';
+--------------------------------------------+-------------+
| Variable_name                              | Value       |
+--------------------------------------------+-------------+
| Rpl_semi_sync_master_clients               | 1           |
| Rpl_semi_sync_master_net_avg_wait_time     | 2071        |
| Rpl_semi_sync_master_net_wait_time         | 4143        |
| Rpl_semi_sync_master_net_waits             | 2           |
| Rpl_semi_sync_master_no_times              | 0           |
| Rpl_semi_sync_master_no_tx                 | 0           |
| Rpl_semi_sync_master_status                | ON          |
| Rpl_semi_sync_master_timefunc_failures     | 0           |
| Rpl_semi_sync_master_tx_avg_wait_time      | 1481        |
| Rpl_semi_sync_master_tx_wait_time          | 2962        |
| Rpl_semi_sync_master_tx_waits              | 2           |
| Rpl_semi_sync_master_wait_pos_backtraverse | 0           |
| Rpl_semi_sync_master_wait_sessions         | 0           |
| Rpl_semi_sync_master_yes_tx                | 2           |
| Rpl_status                                 | AUTH_MASTER |
+--------------------------------------------+-------------+

```

> 可以看到已经进行了数据的同步


#### 如有错误还望批评指正，敬请谅解！
