---
title: mysql安装 centos7
tags: linux服务
categories: linux服务
---
## mysql安装 centos7
`光盘自带的版本`
```bash
[root@centos7 ~]# yum info mariadb
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
 * base: mirrors.tuna.tsinghua.edu.cn
 * extras: mirror.bit.edu.cn
 * updates: mirror.bit.edu.cn
Available Packages
Name        : mariadb
Arch        : x86_64
Epoch       : 1
Version     : 5.5.60
Release     : 1.el7_5
Size        : 8.9 M
Repo        : updates/7/x86_64
Summary     : A community developed branch of MySQL
URL         : http://mariadb.org
License     : GPLv2 with exceptions and LGPLv2 and BSD
Description : MariaDB is a community developed branch of MySQL.
            : MariaDB is a multi-user, multi-threaded SQL database
            : server. It is a client/server implementation consisting
            : of a server daemon (mysqld) and many different client
            : programs and libraries. The base package contains the
            : standard MariaDB/MySQL client programs and generic MySQL
            : files.
```

`rpm 方式安装Mariadb：` 
mysql端口默认为tcp 3306
```bash
安装：
[root@centos7 ~]# yum install mariadb-server -y

查看安装包主要的文件列表：
[root@centos7 ~]# rpm -ql mariadb-server
/etc/my.cnf.d/server.cnf   服务器端配置文件
/usr/libexec/mysqld        服务器主程序
/var/lib/mysql             存放数据库数据的路径
/var/log/mariadb/mariadb.log 日志
/usr/lib/systemd/system/mariadb.service 服务启动脚本

启动程序：
    启动前查看数据库数据目录是为空
    [root@centos7 ~]# ls /var/lib/mysql/
    [root@centos7 ~]# 
[root@centos7 ~]# systemctl restart mariadb
root@centos7 ~]# ls /var/lib/mysql/
aria_log.00000001  ib_logfile0  mysql.sock
aria_log_control   ib_logfile1  performance_schema
ibdata1            mysql        test

连接数据库：
[root@centos7 ~]# mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.60-MariaDB MariaDB Server
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
MariaDB [(none)]> 

查看搜索引擎：
MariaDB [(none)]> show engines;
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                                    | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables                  | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                                      | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                                         | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears)             | NO           | NO   | NO         |
| MyISAM             | YES     | MyISAM storage engine                                                      | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Percona-XtraDB, Supports transactions, row-level locking, and foreign keys | YES          | YES  | YES        |
| ARCHIVE            | YES     | Archive storage engine                                                     | NO           | NO   | NO         |
| FEDERATED          | YES     | FederatedX pluggable storage engine                                        | YES          | NO   | YES        |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                                         | NO           | NO   | NO         |
| Aria               | YES     | Crash-safe tables with MyISAM heritage                                     | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
10 rows in set (0.00 sec)

从服务器中得到相关的状态信息
MariaDB [(none)]> \s
--------------
mysql  Ver 15.1 Distrib 5.5.60-MariaDB, for Linux (x86_64) using readline 5.1

Connection id:		2
Current database:	
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server:			MariaDB
Server version:		5.5.60-MariaDB MariaDB Server
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	latin1
Db     characterset:	latin1
Client characterset:	utf8
Conn.  characterset:	utf8
UNIX socket:		/var/lib/mysql/mysql.sock
Uptime:			9 min 44 sec
Threads: 1  Questions: 6  Slow queries: 0  Opens: 0  Flush tables: 2  Open tables: 26  Queries per second avg: 0.010
--------------

```
`范例：centos7修改提示符：`  
（centos7mysql的配置文件和客户端是分开的）
```bash
[root@centos7 ~]# cd /etc/my.cnf.d/
[root@centos7 my.cnf.d]# ls
client.cnf  mysql-clients.cnf  server.cnf

编辑客户端配置文件修改提示符
[root@centos7 my.cnf.d]# vim mysql-clients.cnf 
[mysql]
prompt='\u@\D->'

查看是否修改成功
[root@centos7 my.cnf.d]# mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 5.5.60-MariaDB MariaDB Server
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and other
Type 'help;' or '\h' for help. Type '\c' to clear the current inputatement.
root@Tue Nov 27 15:04:34 2018->
```

```bash
MariaDB [(none)]>    none:表示当前正在处于哪个数据库里面

查看数据库的数据路径目录形式的代表数据库，也是系统自带的数据库，所以可以理解为数据库存放数据 分为系统自身用的数据、用户创建生产的数据库
[root@centos7 ~]# ls /var/lib/mysql/
aria_log.00000001  ib_logfile0  mysql.sock
aria_log_control   ib_logfile1  performance_schema
ibdata1            mysql        test

查看当前有多少数据库即表
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| test               |
+--------------------+
4 rows in set (0.00 sec)

查看当前数据库的版本信息（数据库中带有括号的命令，表现为系统自带的函数）
MariaDB [(none)]> select version();
+----------------+
| version()      |
+----------------+
| 5.5.60-MariaDB |
+----------------+
1 row in set (0.00 sec)

查看当前的用户信息
MariaDB [(none)]> select user();
+----------------+
| user()         |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)

查看用户当前所在哪个数据库中
MariaDB [(none)]> select database();
+------------+
| database() |
+------------+
| NULL       |
+------------+
1 row in set (0.00 sec)

```
```bash
使用use 客户端工具切换到指定的数据库，作为当前使用的数据库
MariaDB [(none)]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
Database changed
MariaDB [mysql]> 

查看当前使用的数据库中的所有表列表
MariaDB [mysql]> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
24 rows in set (0.00 sec)

命令行指定显示指定数据库中的表列表(意义同上命令)
MariaDB [mysql]> show tables from mysql;
....

上面的数据库中的表实际表现为
[root@centos7 ~]# ls /var/lib/mysql/mysql

查看服务端命令的帮助
help + 服务端命令

``` 
`范例：安装完数据库，linux上的任何用户都可以使用mysql的root用户登陆，也可以使用任何一个用户连接登陆`
```bash
查看mysql存放的用户信息
[root@centos7 ~]# mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 5.5.60-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
24 rows in set (0.00 sec)

MariaDB [mysql]> select user,host,password from user;
+------+-------------+----------+
| user | host        | password |
+------+-------------+----------+
| root | localhost   |          |
| root | centos7.com |          |
| root | 127.0.0.1   |          |
| root | ::1         |          |
|      | localhost   |          |
|      | centos7.com |          |
+------+-------------+----------+
6 rows in set (0.00 sec)

安全加固，执行初始化命令
[root@centos7 ~]# mysql_secure_installation 
Enter current password for root (enter for none): （输入当前数据库中root用户的口令，若无口令，直接回车）
Set root password? [Y/n] （是否设置root的口令）y
New password:              口令
Re-enter new password:     确定口令
Password updated successfully!
Reloading privilege tables..
 ... Success!
Remove anonymous users? [Y/n]  （是否删除匿名用户）y
Disallow root login remotely? [Y/n] (是否禁用远程登陆)y
Remove test database and access to it? [Y/n] (是否删除测试数据库)y
Reload privilege tables now? [Y/n]  (是否重新加载特权表)y
Thanks for using MariaDB!

再次连接mysql数据库
[root@centos7 ~]# mysql -u root -p

MariaDB [(none)]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
24 rows in set (0.00 sec)

MariaDB [mysql]> select user,host,password from user;
+------+-----------+-------------------------------------------+
| user | host      | password                                  |
+------+-----------+-------------------------------------------+
| root | localhost | *0E04F27C8B21547FD069D6E8519AE49B7ECE8E94 |
| root | 127.0.0.1 | *0E04F27C8B21547FD069D6E8519AE49B7ECE8E94 |
| root | ::1       | *0E04F27C8B21547FD069D6E8519AE49B7ECE8E94 |
+------+-----------+-------------------------------------------+
3 rows in set (0.00 sec)

目前仅可以本机连接，使用centos6连接测试
[root@centos6 ~]# mysql -u root -p centos -h 172.18.135.88
Enter password: 
连接不上
```
`范例：查看mysql账号数据库是否活跃`
```bash
[root@centos7 ~]# mysqladmin -u root -p ping
Enter password: 
mysqld is alive
```
`范例：停止此用户的数据库`
```bash
[root@centos7 ~]# mysqladmin -u root -p shutdown
Enter password: 

测试：连接不上去了
[root@centos7 ~]# mysql -u root -p
Enter password: 
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)

启动服务
[root@centos7 ~]# systemctl restart mariadb

查看连接信息
[root@centos7 ~]# mysqladmin status
Uptime: 23  Threads: 1  Questions: 3  Slow queries: 0  Opens: 0  Flush tables: 2  Open tables: 18  Queries per second avg: 0.130
````
