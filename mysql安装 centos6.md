---
title: mysql安装 centos6
tags: linux服务
categories: linux服务
---
## mysql安装 centos6
`光盘自带的版本`
```bash
[root@centos6 ~]# yum info mysql
Loaded plugins: fastestmirror, refresh-packagekit, security
Loading mirror speeds from cached hostfile
 * base: mirror.bit.edu.cn
 * extras: ftp.sjtu.edu.cn
 * updates: mirrors.huaweicloud.com
Available Packages
Name        : mysql
Arch        : x86_64
Version     : 5.1.73
Release     : 8.el6_8
Size        : 895 k
Repo        : base
Summary     : MySQL client programs and shared libraries
URL         : http://www.mysql.com
License     : GPLv2 with exceptions
Description : MySQL is a multi-user, multi-threaded SQL database
            : server. MySQL is a client/server implementation
            : consisting of a server daemon (mysqld) and many different
            : client programs and libraries. The base package contains
            : the standard MySQL client programs and generic MySQL
            : files.
```

`rpm 方式安装Mariadb：` 
mysql端口默认为tcp 3306
```bash
安装：
[root@centos6 ~]# yum install mysql-server -y

查看安装包主要的文件列表：
[root@centos6 ~]# rpm -ql mysql-server
/etc/rc.d/init.d/mysqld  服务启动脚本
/usr/libexec/mysqld      服务器主程序
/var/lib/mysql           存放数据库的数据的路径
/var/log/mysqld.log      日志文件
/etc/my.cnf              服务的配置文件
[root@centos6 ~]# rpm -qf /etc/my.cnf （可以作为mysql数据库的服务器的配置文件，也可以作为客户端的配置文件）
mysql-libs-5.1.73-8.el6_8.x86_64

启动服务：
[root@centos6 ~]# service mysqld star
Installing MySQL system tables...
OK
Filling help tables...
OK

To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your syste

PLEASE REMEMBERTO  SET A PASSWORD FOR THE MySQL root USER !
To do so, start the server, then issue the following command

/usr/bin/mysqladmin -u root password 'new-password'
/usr/bin/mysqladmin -u root -h centos6.com password 'new-pasd'

Alternatively you can run:
/usr/bin/mysql_secure_installation 初始化服务脚本，可以设置root口令，也可以更安全的数据库

which will also give you the option of removing the test
databases and anonymous user created by default.  This is
strongly recommended for production servers.

See the manual for more instructions.

You can start the MySQL daemon with:
cd /usr ; /usr/bin/mysqld_safe &

You can test the MySQL daemon with mysql-test-run.pl
cd /usr/mysql-test ; perl mysql-test-run.pl

Please report any problems with the /usr/bin/mysqlbug script

                                                           [ ok ]
Starting mysqld:                                           [ ok ]

  启动程序后，生成数据库数据相关的文件，未启动之前时空的：
  [root@centos6 ~]# ls /var/lib/mysql/
  ibdata1  ib_logfile0  ib_logfile1  mysql  mysql.sock（数据库的套接字）  test
  自己连接本机的mysql服务端，可以走套接字（数据库服务的用户与linux用户无关）

使用客户端工具连接数据库
[root@centos6 ~]# which mysql
/usr/bin/mysql
[root@centos6 ~]# rpm -qf /usr/bin/mysql
mysql-5.1.73-8.el6_8.x86_64

查看进程：
[root@centos6 ~]# ps aux
root       4616  0.0  0.0 108228  1468 pts/1    S    21:05   0:00 /bin/sh /usr/bin/mysqld_safe --datadir=/var/lib/mysql --socket=/var/lib/mysql/mysql.sock --
（数据库的主程序）mysql      4718  0.0  1.6 367520 30848 pts/1    Sl   21:05   0:00 /usr/libexec/mysqld --basedir=/usr --datadir=/var/lib/mysql --user=mysql --log-error=/var/l

mysql数据库是单进程多线程的数据库程序：
[root@centos6 ~]# pstree -p
├─mysqld_safe(4616)───mysqld(4718)─┬─{mysqld}(4720)
        │                                  ├─{mysqld}(4721)线程
        │                                  ├─{mysqld}(4722)
        │                                  ├─{mysqld}(4723)
        │                                  ├─{mysqld}(4724)
        │                                  ├─{mysqld}(4725)
        │                                  ├─{mysqld}(4726)
        │                                  ├─{mysqld}(4727)
        │                                  └─{mysqld}(4728)

查看数据库安装脚本
[root@centos6 ~]# rpm -q --scripts mysql-server
preinstall scriptlet (using /bin/sh):
/usr/sbin/groupadd -g 27 -o -r mysql >/dev/null 2>&1 || :
/usr/sbin/useradd -M -N -g mysql -o -r -d /var/lib/mysql -s /bin/bash \
	-c "MySQL Server" -u 27 mysql >/dev/null 2>&1 || :
postinstall scriptlet (using /bin/sh):
if [ $1 = 1 ]; then
    /sbin/chkconfig --add mysqld
fi
/bin/chmod 0755 /var/lib/mysql
/bin/touch /var/log/mysqld.log
preuninstall scriptlet (using /bin/sh):
if [ $1 = 0 ]; then
    /sbin/service mysqld stop >/dev/null 2>&1
    /sbin/chkconfig --del mysqld
fi
postuninstall scriptlet (using /bin/sh):
if [ $1 -ge 1 ]; then
    /sbin/service mysqld condrestart >/dev/null 2>&1 || :
fi

连接数据库：（本地连接数据库是由本机的sock套接字连接）
  使用mysql客户端工具连接：
  mysql -u 数据库用户 -p （提示输入口令）-s 指定套接字路径 （默认为/var/lib/mysql/mysql.sock）

[root@centos6 ~]# mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.1.73 Source distribution
Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' 帮助for help. Type '\c'清屏 to clear the current input statement.
mysql> 
```
`范例：下面为mysql数据库的客户端命令`
```bash
下面为mysql数据库的客户端命令
mysql> help      
List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
?         (\?) Synonym for `help'. 
clear     (\c) --清除当前输入的语句
connect   (\r) --重新连接，通常用于被剔除或异常断开后重新连接，SQL*plus下也有这样一个connect命令
delimiter (\d) --设置命令终止符，缺省为；，比如我们可以设定为/来表示语句结束 
edit      (\e) --编辑缓冲区的上一条SQL语句到文件，缺省调用vi，文件会放在/tmp路径下
ego       (\G) --控制结果显示为垂直显示
exit      (\q) --退出mysql
go        (\g) --发送命令到mysql服务
help      (\h) Display this help.
nopager   (\n) --关闭页设置，打印到标准输出  
notee     (\t) --关闭输出到文件
pager     (\P) --设置pager方式，可以设置为调用more,less等等，主要是用于分页显示
print     (\p) Print current command.           
prompt    (\R) --改变mysql的提示符 
quit      (\q) Quit mysql.                             
rehash    (\#) --自动补齐相关对象名字  
source    (\.) --执行脚本文件
status    (\s) --获得状态信息
system    (\!) --执行系统命令   
tee       (\T) --操作结果输出到文件 
use       (\u) --切换数据库
charset   (\C) --设置字符集
warnings  (\W) --打印警告信息
nowarning (\w) Don't show warnings after every statement.
--上面的所有命令，扩号内的为快捷操作，即只需要输入“\”+ 字母即可执行

查看mysql数据库的存储引擎：（服务器端命令）
mysql> show engines;
+------------+---------+------------------------------------------------------------+--------------+------+------------+
| Engine     | Support | Comment                                                    | Transactions | XA   | Savepoints |
+------------+---------+------------------------------------------------------------+--------------+------+------------+
| MRG_MYISAM | YES     | Collection of identical MyISAM tables                      | NO           | NO   | NO         |
| CSV        | YES     | CSV storage engine                                         | NO           | NO   | NO         |
| MyISAM     | DEFAULT | Default engine as of MySQL 3.23 with great performance     | NO           | NO   | NO         |
| InnoDB     | YES     | Supports transactions, row-level locking, and foreign keys | YES          | YES  | YES        |
| MEMORY     | YES     | Hash based, stored in memory, useful for temporary tables  | NO           | NO   | NO         |
+------------+---------+------------------------------------------------------------+--------------+------+------------+
5 rows in set (0.00 sec)

从服务器中得到相关的状态信息
mysql> status
--------------
mysql  Ver 14.14 Distrib 5.1.73, for redhat-linux-gnu (x86_64) using readline 5.1
Connection id:		2
Current database:	
Current user:		root@localhost（当前连接身份）
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		5.1.73 Source distribution
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	latin1 （字符集）
Db     characterset:	latin1
Client characterset:	latin1
Conn.  characterset:	latin1
UNIX socket:		/var/lib/mysql/mysql.sock（套接字文件路径）
Uptime:			1 hour 1 min 14 sec
Threads: 1（当前线程）  Questions: 8  Slow queries: 0  Opens: 15  Flush tables: 1  Open tables: 8  Queries per second avg: 0.2
--------------

调用linux命令
mysql> system hostname
centos6.com

```
`范例：mysql中的提示符`
```bash
提示符：
修改方式建议
为了方便我们在平时的使用，有效的给我们提示信息。 
建议参考Linux系统的提示符方式命名，即：用户名@主机名+当前所在位置。 
在MySQL中可以通过参数来获取提示符信息，下面列表中列出了常用的四个信息，方便我们等下修改MySQL提示符。

参数	描述
\D	完整的日期
\d	当前数据库
\h	服务器名称
\u	当前用户
mysql> PROMPT \u@\h \d >    
root@localhost (none) >CREATE DATABASE testdb;
root@localhost (none) >USE testdb;
root@localhost testdb >

修改mysql数据库的提示符
mysql> prompt mysql-->
PROMPT set to 'mysql-->'
mysql-->

命令行进入mysql顺便修改提示符
[root@centos6 ~]# mysql --prompt="\u@\D"
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.1.73 Source distribution
Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
root@Tue Nov 27 22:53:50 2018

修改mysql提示符，永久保存生效(centos6的服务端和客户端的配置文件在同一个文件中)
  编辑数据库的配置文件，写入客户端配置
[root@centos6 ~]# vim /etc/my.cnf 
服务端配置
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
服务端配置
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
客户端配置，写入提示符信息
[mysql]
prompt='\u@\D->'

保存，进入数据库，查看提示符，是否发生变化
[root@centos6 ~]# mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.1.73 Source distribution
Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
root@Tue Nov 27 22:58:29 2018->
```

