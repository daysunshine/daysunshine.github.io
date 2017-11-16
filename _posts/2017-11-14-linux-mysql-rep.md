---
layout: post
title:  mysql备份与恢复
date:   2017-11-14 08:00:00
categories: Linux 
tags:  mysql备份 恢复 日志
---

### mysql的日志系统

> 在介绍mysql的备份及复制的时候就需要对mysql的日志文件系统进行介绍，其中与备份和复制实现密不可分的是二进制日志文件(binlog)，下面会介绍到

> mysql的日志包含以下几类：
	- 查询日志：general_log
	- 慢查询日志：log_slow_queries
	- 错误日志：log_error， log_warnings
	- 二进制日志：binlog
	- 中继日志：relay_log
	- 事务日志：innodb_log

#### 1、查询日志

> 记录查询语句：

```			
	general_log={ON|OFF}			#一般不开启，占用IO资源，开启需要在mysql命令行下使用set @@global.general_log=on
	general_log_file=HOSTNAME.log 	#日志存放文件路径
	log_output={FILE|TABLE|NONE}  	#设定在哪里记录文件的信息
```

#### 2、慢查询日志   
  
> 慢查询：运行时间超出指定时长的查询,通常启用，当执行命令超出设定的时间就会记录到慢查询日志当中

```		
	long_query_time   #定义时长的参数

	slow_query_log={ON|OFF}
	slow_query_log_file=          #设定慢日志的文件位置
	log_output={FILE|TABLE|NONE}

```

#### 3、错误日志

> 记录如下几类信息：
	- (1) mysqld启动和关闭过程中输出的信息； 
	- (2) mysqld运行中产生的错误信息； 
	- (3) event scheduler运行时产生的信息；
	- (4) 主从复制架构中，从服务器复制线程启动时产生的日志；

```	
	log_error=/var/log/mariadb/mariadb.log|OFF
	log_warnings={ON|OFF}

```

#### 4、二进制日志 (非常重要)

> 用于记录引起数据改变或存在引起数据改变的潜在可能性的语句（STATEMENT）或改变后的结果（ROW），也可能是二者混合；

```						
binlog_format={STATEMENT|ROW|MIXED}
	STATEMENT：语句（默认的格式）
	ROW：行；
	MIXED：混编；
```

> 开启二进制文件的设置

> 在/etc/my.cnf.d/server.cnf内的[server]下进行设定，添加命令如下
```
	log_bin=/app/logs/master-log  #指定的存放位置
```
> 此外在mysql的命令行中还可以进行其他的设定

```

	session.sql_log_bin={ON|OFF}  #控制某会话中的“写”操作语句是否会被记录于二进制日志文件中；可以直接在命令行设定是否记录二进制日志当中
	max_binlog_size=1073741824  定义二进制文件记录的大小，超过进行滚动，重新生成一个新的二进制文件
	sync_binlog={1|0}  是否将内存中的（已经提交的commit，立即写到磁盘上去，确保重放事务安全）数据记录到磁盘中
	进行重放时，需要临时关闭记录写操作的内容，没必要进行二次的重复

```

> 通过以下命令进行查看

```	
查看二进制日志文件列表：
	 SHOW MASTER|BINARY LOGS;
				 
查看当前正在使用的二进制日志文件：
	SHOW MASTER STATUS；
				
查看二进制 日志文件中的事件：
	SHOW BINLOG EVENTS	 [IN 'log_name'] [FROM position] [LIMIT [offset,] row_count]

示例：
MariaDB [(none)]> SHOW BINLOG EVENTS  IN 'master-log.000002';
+-------------------+-----+-------------+-----------+-------------+-------------------------------------------+
| Log_name          | Pos | Event_type  | Server_id | End_log_pos | Info                                      |
+-------------------+-----+-------------+-----------+-------------+-------------------------------------------+
| master-log.000002 |   4 | Format_desc |         1 |         245 | Server ver: 5.5.52-MariaDB, Binlog ver: 4 |
+-------------------+-----+-------------+-----------+-------------+-------------------------------------------+
1 row in set (0.00 sec)
		
```

> mysqlbinlog：命令行工具，查看二进制文件的命令,还支持远程连接查看

```	
	--start-datetime="YYYY-MM-DD hh:mm:ss"
	--stop-datetime="YYYY-MM-DD hh:mm:ss"
				 
	-j, --start-position=  		开始位置  eg：mysqlbinlog -j 313 master-log.000002
	--stop-position=			结束位置
				  
	--user, --host, --password 	指定用户、主机及密码进行连接获取
```				
 
> 在命令行下通过mysqlbinlog查看

```
[root@centos7 ~]#mysqlbinlog /app/logs/master-log.000002

/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!40019 SET @@session.max_insert_delayed_threads=0*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#171110 20:41:11 server id 1  end_log_pos 245 	Start: binlog v 4, server v 5.5.52-MariaDB created 171110 20:41:11
# Warning: this binlog is either in use or was not closed properly.
BINLOG '
Z54FWg8BAAAA8QAAAPUAAAABAAQANS41LjUyLU1hcmlhREIAbG9nAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAEzgNAAgAEgAEBAQEEgAA2QAEGggAAAAICAgCAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAA0v9cbQ==
'/*!*/;
DELIMITER ;
# End of log file
ROLLBACK /* added by mysqlbinlog */;
/*!50003 SET COMPLETION_TYPE=@OLD_COMPLETION_TYPE*/;
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=0*/;

```

> 二进制日志事件格式

```
	# at 553
	#160831  9:56:08 server id 1  end_log_pos 624   Query   thread_id=2     exec_time=0     error_code=0
	SET TIMESTAMP=1472608568/*!*/;
	BEGIN
	/*!*/;
				
	事件的起始位置：# at 553
	事件发生的日期时间：#160831  9:56:08
	事件发生的服务器id：server id 1
	事件的结束位置：end_log_pos 624
	事件的类型：Query   #增删改查
	事件发生时所在服务器执行此事件的线程的ID： thread_id=2 
	语句的时间戳与将其写入二进制日志文件中的时间差：exec_time=0
	错误代码：error_code=0
	设定事件发生时的时间戳：SET TIMESTAMP=1472608568/*!*/;
	事件内容：BEGIN

```
	
#### 5、中继日志：
> 从服务器上记录下来从主服务器的二进制日志文件同步过来的事件；用于mysql的主从复制当中的
					
#### 6、事务日志：
> 事务型存储引擎innodb用于保证事务特性的日志文件
```		
	redo log 
	undo log 
```

### Mysql的备份

> 备份分为：
> 根据数据服务是否在线：
	- 热备：读写操作均可进行的状态下所做的备份
	- 温备：可读但不可写状态下进行的备份
	- 冷备：读写操作均不可进行的状态下所做的备份

> 全量备份、增量备份、差异备份含义
	- 全量备份：多所有数据进行备份
	- 增量备份：仅备份自上一次完全备份或 增量备份以来变量的那部数据
	- 差异备份：仅备份自上一次完全备份以来变量的那部数据


> Innodb引擎能够支持热备、温备及冷备，而Myisam最多能够支持到温备

> 备份工具：

	- `mysqldump`	全量+binlog
	- `xtrabackup`	全量+差异+binlog全量+增量+binlog

> 对于数据库的备份，不仅需要要将其存储的数据进行备份还要将一些与数据本身密切相关的参数设定一并进行备份，恢复时才能与源数据相同

	- 数据
	- 二进制日志、InnoDB的事务日志；
	- 代码（存储过程、存储函数、触发器、事件调度器）
	- 服务器的配置文件

### mysqldump

> mysql服务自带的备份工具；逻辑备份工具；

> 命令格式：

```
   mysqldump [OPTIONS] database [tables]   						# 备份单库，可以只备份其中的一部分表（部分备份）；
   mysqldump [OPTIONS] --databases [OPTIONS] DB1 [DB2 DB3...]	# 备份多库；在恢复时会自动进行创建库 ,建议使用
   mysqldump [OPTIONS] --all-databases [OPTIONS] 				# 备份所有库	

```

> 对于MyISAM存储引擎：支持温备，备份时要锁定表；
	- `-x, --lock-all-tables`：锁定所有库的所有表，读锁；
	- `-l, --lock-tables`：锁定指定库所有表；
				
> 对于InnoDB存储引擎：支持温备和热备；
	- 热备：`--single-transaction`：创建一个事务，基于此快照执行备份；	
	- 温备：同上面使用的`-x`、`-l`选项

> mysqldump的其它选项：
	- `-R, --routines`：备份指定库的存储过程和存储函数；
	- `--triggers`：	备份指定库的触发器；
	- `-E, --events`：	备份事件调度器
	- `--flush-logs`：	锁定表完成后，即进行二进制日志刷新滚动操作； 
	- `--master-data[=#]`  记录二进制日志文件从哪里开始进行记录
		`1`：记录为`CHANGE MASTER TO`语句，此语句不被注释；
		`2`：记录为`CHANGE MASTER TO`语句，此语句被注释；只是提供一个信息不去执行（给从服务器用的，要是只有一个主，就可以选择2注释掉，或者不写就什么也没有）
		
### mysqldump实现完整备份及恢复		

> 先进行配置文件的设置，将log_bin进行设置，基于上面已经进行的设定，指定存放的位置log_bin=/app/logs/master-log，启动二进制文件记录功能

**1、进行备份(热备)**

> 在需要进行备份操作的主机上执行以下操作，也可以通过其它主机连接至目标服务器进行远程的备份和复制的操作

```
	mysqldump --single-transaction -R -E --triggers --all-databases --master-data=2 --flush-logs >/root/fullbackup-$(date +%F-%T).sql
```

> 在生成的文件当中可以看到--master-data=2选项生成相关的被注释的语句
```
[root@centos7 ~]#less fullbackup-2017-11-11-10:39:01.sql
...
部分省略
-- CHANGE MASTER TO MASTER_LOG_FILE='master-log.000001', MASTER_LOG_POS=245;
部分省略
...

```

**2、对表进行增删查改等操作**

> 进行全备份后，对于全备份后的时间点发生数据的改变则需要二进制日志文件进行记录
> 添加两份数据

```
INSERT INTO you.students(name,age) VALUES('tom',34),('miki',43);
```

**3、模拟数据库崩溃**

> 我们手动将已经进行备份的数据库的mysql库进行手动的删除
> 生产当中需先拷贝`/etc/my.conf.d/server.cnf`和`/var/lib/mysql/master-log.xxxxxx`到别处。
> (配置文件，一般配置服务器会保存一份；binlog日志文件，一般是存到不同的服务器做冗余。这里假设都是从其他服务器拷贝过来的，暂时copy一份作为恢复用。）

```
	systemctl stop mariadb.service
	rm -rf /var/lib/mysql/*
```

**4、恢复数据库全备文件**

> 恢复配置文件`/etc/my.conf.d/server.cnf`、binlog日志文件`/var/lib/mysql/master-log.xxxx`、数据库备份文件/root/fullbackup-xxxx-xx-xx-xx-xx-xx
> 进入mysql设定不启动bin_log日志记录功能。

```
MariaDB [(none)]> set @@session.sql_log_bin=OFF;
	
或者编辑mysql的配置文件将log_bin选项注释掉同样可以

```
恢复全库备份文件：

第一种方法，本机运行mysql命令,将全备份文件导入：

```
MariaDB [(none)]> \. fullbackup-2017-11-11-10:39:01.sql

#查看导入后的表
MariaDB [you]> select * from students;
+----+-------+------+
| id | name  | age  |
+----+-------+------+
|  1 | jack  |   18 |
|  2 | allen |   20 |
+----+-------+------+
2 rows in set (0.00 sec)

可以看到已经恢复了，但是还有一部分已经更改的内容还需要通过二进制日志文件进行恢复 

```

第二种方法：远程或本机执行命令（远程的前提是有远程权限）：

```
mysql -u USER -h HOST -p PASSWORD < /root/fullbackup-2017-11-11-10:39:01.sql
```

> 这里USER为用户名，HOST为主机名或IP，PASSWORD为密码。

**5、恢复二进制bin_log日志**

在上面已经进行说明，日志记录的`CHANGE MASTER TO`位置就是在全备份后的时间段进行记录的文件目录及位置

```
-- CHANGE MASTER TO MASTER_LOG_FILE='master-log.000001', MASTER_LOG_POS=245;
```
> 其中245就是开头的位置（因为我们备份时候启动日志滚动`--flush-log`了，所以会滚动到下一个日志文件的开头），
> 我们要把包含`master-log.000001`和之后的的所有bin_log日志文件，用`mysqlbinlog`命令工具，读取binlog日志文件内容，然后重定向到到一个文件里。

```
cd /app/logs/
mysqlbinlog master-log.000001 ... master-log.00000x > /root/binlog.sql

```

> 将重定向的二进制文件导入mysql即可

```
[root@centos7 ~]#mysql < binlog.sql

#查看是否导入成功
MariaDB [you]> select * from students;
+----+-------+------+
| id | name  | age  |
+----+-------+------+
|  1 | jack  |   18 |
|  2 | allen |   20 |
|  3 | tom   |   34 |
|  4 | miki  |   43 |
+----+-------+------+
4 rows in set (0.00 sec)

#可以看到数据已经全部恢复

```

> 修复完成后再开启记录二进制日志的选项

```
MariaDB [(none)]> set @@session.sql_log_bin=ON;
```

### xtrabackup

> 由Percona提供，开源工具，支持对InnoDB做热备，物理备份工具；
> Xtrabackup是由percona提供的mysql数据库备份工具，据官方介绍，这也是世界上惟一一款开源的能够对innodb和xtradb数据库进行热备的工具。
> 特点：
- (1)备份过程快速、可靠；
- (2)备份过程不会打断正在执行的事务；
- (3)能够基于压缩等功能节约磁盘空间和流量；
- (4)备份完成后自动校验备份结果集是否可用；
- (5)还原速度快。


> 在安装XtraBackup后主要应用的是封装了的命令`innobackupex`

#### 1、完全备份

> innobackupex的基本用法

`innobackupex --user=DBUSER --password=DBUSERPASS /path/to/BACKUP-DIR/`

> 如果要使用一个最小权限的用户进行备份，则可基于如下命令创建此类用户：

```
mysql> CREATE USER ’bkpuser’@’localhost’ IDENTIFIED BY ’s3cret’;
mysql> REVOKE ALL PRIVILEGES, GRANT OPTION FROM ’bkpuser’;
mysql> GRANT RELOAD, LOCK TABLES, REPLICATION CLIENT ON *.* TO ’bkpuser’@’localhost’;
mysql> FLUSH PRIVILEGES;

```

> 使用innobakupex备份时，其会调用xtrabackup备份所有的InnoDB表，复制所有关于表结构定义的相关文件(.frm)、以及MyISAM、MERGE、CSV和ARCHIVE表的相关文件，同时还会备份触发器和数据库配置信息相关的文件。这些文件会被保存至一个`以时间命名的目录`中。

```
#在本机直接执行innobackupex命令
[root@centos7 ~]#innobackupex /app/data/   #一定在完成备份后出现171111 14:47:20 completed OK!的字样才是成功备份

#查看生成的文件
[root@centos7 /app/data/2017-11-11_14-47-09]#ll
total 18460
-rw-r-----. 1 root root      417 Nov 11 14:47 backup-my.cnf
-rw-r-----. 1 root root 18874368 Nov 11 14:47 ibdata1
drwxr-x---. 2 root root     4096 Nov 11 14:47 mysql
drwxr-x---. 2 root root     4096 Nov 11 14:47 performance_schema
drwxr-x---. 2 root root       20 Nov 11 14:47 test
-rw-r-----. 1 root root       22 Nov 11 14:47 xtrabackup_binlog_info
-rw-r-----. 1 root root      113 Nov 11 14:47 xtrabackup_checkpoints
-rw-r-----. 1 root root      444 Nov 11 14:47 xtrabackup_info
-rw-r-----. 1 root root     2560 Nov 11 14:47 xtrabackup_logfile
drwxr-x---. 2 root root       60 Nov 11 14:47 you

```

> 在使用innobackupex进行备份后还会在目录下生成以下文件

- `xtrabackup_info` ：总体备份信息。

```
[root@centos7 /app/data/2017-11-11_14-47-09]#cat xtrabackup_info
uuid = 2a022082-c6ac-11e7-b9bd-000c294fb3cb
name = 
tool_name = innobackupex
tool_command = /app/data/
tool_version = 2.4.7
ibbackup_version = 2.4.7
server_version = 5.5.52-MariaDB
start_time = 2017-11-11 14:47:11
end_time = 2017-11-11 14:47:19
lock_time = 0
binlog_pos = filename 'master-log.000005', position '245'
innodb_from_lsn = 0
innodb_to_lsn = 1603943
partial = N
incremental = N
format = file
compact = N
compressed = N
encrypted = N

```


- `xtrabackup_checkpoints`：备份类型（如完全或增量）、备份状态（如是否已经为prepared状态）和LSN(日志序列号)范围信息；

```
[root@centos7 /app/data/2017-11-11_14-47-09]#cat xtrabackup_checkpoints
backup_type = full-backuped
from_lsn = 0
to_lsn = 1603943
last_lsn = 1603943
compact = 0
recover_binlog_info = 0

```

> `LSN`：每个InnoDB页(通常为16k大小)都会包含一个日志序列号，即LSN。LSN是整个数据库系统的系统版本号，每个页面相关的LSN能够表明此页面最近是如何发生改变的。

- `xtrabackup_binlog_info` : mysql服务器当前正在使用的二进制日志文件及至备份这一刻为止二进制日志事件的位置。

```
[root@centos7 /app/data/2017-11-11_14-47-09]#cat xtrabackup_binlog_info
master-log.000005	245
```

- `backup-my.cnf` :备份命令用到的配置选项信息。

```
[root@centos7 /app/data/2017-11-11_14-47-09]#cat backup-my.cnf
# This MySQL options file was generated by innobackupex.

# The MySQL server
[mysqld]
innodb_checksum_algorithm=innodb
innodb_log_checksum_algorithm=innodb
innodb_data_file_path=ibdata1:10M:autoextend
innodb_log_files_in_group=2
innodb_log_file_size=5242880
innodb_fast_checksum=false
innodb_page_size=16384
innodb_log_block_size=512
innodb_undo_directory=.
innodb_undo_tablespaces=0
server_id=0

redo_log_version=0

```

> 在使用innobackupex进行备份时，还可以使用--no-timestamp选项来阻止命令自动创建一个以时间命名的目录；如此一来，innobackupex命令将会创建一个BACKUP-DIR目录来存储备份数据。

#### 2、准备(prepare)一个完全备份

> 一般情况下，在备份完成后，数据是不能用于恢复的，因为备份的数据中可能会包含尚未提交的事务或已经提交但尚未同步至数据文件中的事务。
> 因此，此时数据文件仍处理不一致状态。“准备”的主要作用正是通过**回滚未提交的事务**及**同步已经提交的事务至数据文件**使得数据文件与实际的操作一致

> innobakupex命令的`--apply-log`应用事务日志选项可用于实现上述功能。如下面的命令：

```
innobackupex --apply-log  /app/data/2017-11-11_14-47-09/

如果执行正确，其最后输出的几行信息通常如下：
xtrabackup: starting shutdown with innodb_fast_shutdown = 1
120407  9:01:36  InnoDB: Starting shutdown...
120407  9:01:40  InnoDB: Shutdown completed; log sequence number 92033220
120407 09:01:40  innobackupex: completed OK!

```

> 在实现“准备”的过程中，innobackupex通常还可以使用--use-memory选项来指定其可以使用的内存的大小，默认通常为100M。如果有足够的内存可用，可以多划分一些内存给prepare的过程，以提高其完成速度。


#### 3、从一个完全备份中恢复数据 

> innobackupex命令的`--copy-back`选项用于执行恢复操作，其通过复制所有数据相关的文件至mysql服务器DATADIR目录中来执行恢复过程。innobackupex通过`backup-my.cnf`来获取DATADIR目录的相关信息。

```
innobackupex --copy-back  /path/to/BACKUP-DIR

注意：恢复不用启动MySQL

如果执行正确，其输出信息的最后几行通常如下：
innobackupex: Starting to copy InnoDB log files
innobackupex: in '/backup/2017-11-11_08-17-03'
innobackupex: back to original InnoDB log directory '/mydata/data'
innobackupex: Finished copying back files.

120407 09:36:10  innobackupex: completed OK!

```

**请确保如上信息的最行一行出现“innobackupex: completed OK!”**

> 当数据恢复至DATADIR目录以后，还需要确保所有数据文件的属主和属组均为正确的用户，如mysql，否则，在启动mysqld之前还需要事先修改数据文件的属主和属组。
如：
```
chown -R  mysql:mysql  /app/data/*
```

#### 4、使用innobackupex进行增量备份

> 每个InnoDB的页面都会包含一个LSN信息，每当相关的数据发生改变，相关的页面的LSN就会自动增长。这正是InnoDB表可以进行增量备份的基础，即innobackupex通过备份上次完全备份之后发生改变的页面来实现。

> 要实现第一次增量备份，可以使用下面的命令进行：

```
innobackupex --incremental /app/data/ --incremental-basedir=/app/data/2017-11-11_14-47-09
```
> 其中，BASEDIR指的是完全备份所在的目录，此命令执行结束后，innobackupex命令会在/backup目录中创建一个新的以时间命名的目录以存放所有的增量备份数据。
> 另外，在执行过增量备份之后再一次进行增量备份时，其--incremental-basedir应该指向上一次的增量备份所在的目录,而不是全量。
> 需要注意的是，增量备份仅能应用于InnoDB或XtraDB表，对于MyISAM表而言，执行增量备份时其实进行的是完全备份。

#### 5、增量备份的合并

**“准备”(prepare)增量备份与整理完全备份有着一些不同，尤其要注意的是：**
- (1)需要在每个备份(包括完全和各个增量备份)上，将已经提交的事务进行“重放”。“重放”之后，所有的备份数据将合并到完全备份上。
- (2)基于所有的备份将未提交的事务进行“回滚”。

> 整理完全备份，于是，操作就变成了：

```
innobackupex --apply-log --redo-only /app/data/
```
> --redo-only只提交事务已经提交确认的，事务不完整的不执行任何操作，等到最后一次准备备份完成后在进行统一的回滚

>接着执行：

```
innobackupex --apply-log --redo-only /app/data/  --incremental=/app/data/2017-11-11_15-46-42

```

> 而后是第二个增量：

```
innobackupex --apply-log --redo-only BASE-DIR --incremental-dir=INCREMENTAL-DIR-2
```
> 其中BASE-DIR指的是完全备份所在的目录，而INCREMENTAL-DIR-1指的是第一次增量备份的目录，INCREMENTAL-DIR-2指的是第二次增量备份的目录，其它依次类推，即如果有多次增量备份，每一次都要执行如上操作；

> **注意**：在进行合并增量时，路径要使用绝对路径

> 最后一个增量命令完成后，要在全量备份上做“回滚”操作。

```
innobackupex --apply-log BASE-DIR
```

> 将二进制文件保存到其他地方

```
[root@centos7 /app/data/2017-11-11_14-47-09]#mysqlbinlog -j 245 /app/logs/master-log.000005 > /root/bin-log.sql

```

#### 6、模拟数据库崩溃

> 同样将本机的mysql的数据库文件删除，来进行模拟数据库崩溃，这里不再进行赘述


#### 7、数据的恢复

> 前面已经将备份文件进行了准备和合并，这里只需要将之前的全备份文件导入mysql即可

```
#停止mysql服务
systemctl stop mariadb

#导入备份的数据
[root@centos7 ~]# innobackupex --copy-back  /app/data/2017-11-11_14-47-09
...
部分省略
171111 16:10:58 [01]        ...done
171111 16:10:58 [01] Copying ./xtrabackup_info to /var/lib/mysql/xtrabackup_info
171111 16:10:58 [01]        ...done
171111 16:10:58 [01] Copying ./xtrabackup_binlog_pos_innodb to /var/lib/mysql/xtrabackup_binlog_pos_innodb
171111 16:10:58 [01]        ...done
171111 16:10:58 [01] Copying ./ibtmp1 to /var/lib/mysql/ibtmp1
171111 16:11:00 [01]        ...done
171111 16:11:01 completed OK!

#将权限更改为mysql用户
[root@centos7 ~]#chown -R mysql.mysql /var/lib/mysql

```

> 导入数据成功

> 来查看一下mysql的数据

```
MariaDB [you]> select * from students;
+----+-------+------+
| id | name  | age  |
+----+-------+------+
|  1 | jack  |   18 |
|  2 | allen |   20 |
|  3 | tom   |   34 |
|  4 | miki  |   43 |
+----+-------+------+
4 rows in set (0.00 sec)

```

> 可以看到在完全备份后的操作数据还未还原

> 进行二进制的还原

```

#在mysql命令行下先将session.sql_log_bin=OFF;
set @@session.sql_log_bin=OFF;

#导入二进制日志文件
\. /root/log-bin.sql

#set @@session.sql_log_bin=ON;

```

> 查看mysql表中的数据

```

MariaDB [you]> select * from students;
+----+----------+------+
| id | name     | age  |
+----+----------+------+
|  1 | jack     |   18 |
|  2 | allen    |   20 |
|  3 | tom      |   34 |
|  4 | miki     |   43 |
|  5 | student1 |    5 |
|  6 | student2 |    6 |
+----+----------+------+
6 rows in set (0.00 sec)

```

> 所有的数据都已经还原

> 需要注意的是，在每次的备份完成后，之前的备份文件就不能再基于当前的mysql的系统使用了，还需要进行重新的备份操作

#### 如有错误还望批评指正，敬请谅解！
