---
title: mariadb实现二进制安装
tags: linux服务
categories: linux服务
---
## 通用二进制格式安装过程 
范例：二进制格式安装的mysql版本为：mysql-10.2
```bash
第一步：将二进制编译完的文件传进linux中，解压缩、创建软连接
[root@centos7 ~]# ls
mariadb-10.2.19-linux-x86_64.tar.gz 
[root@centos7 ~]# tar xfv mariadb-10.2.19-linux-x86_64.tar.gz -C /usr/local
[root@centos7 ~]# ls /usr/local
bin  games    lib    libexec                       sbin   src
etc  include  lib64  mariadb-10.2.19-linux-x86_64  share
   创建软连接，方便下次升级（链接程序所在路径，因为源码编译时文档中指定程序路径放置在/usr/local/mysql）
[root@centos7 ~]# ln -s /usr/local/mariadb-10.2.19-linux-x86_64/ /usr/local/mysql
[root@centos7 ~]# ll /usr/local/mysql
lrwxrwxrwx. 1 root root 40 Nov 27 17:01 /usr/local/mysql -> /usr/local/mariadb-10.2.19-linux-x86_64/

第二步：修改程序目录的属性
[root@centos7 ~]# chown -R root:root /usr/local/mysql/
[root@centos7 ~]# ll /usr/local/mysql/
total 180
drwxrwxr-x.  2 root root  4096 Sep 23 10:13 bin  程序
-rw-r--r--.  1 root root 17987 Nov 13 00:32 COPYING
-rw-r--r--.  1 root root 86263 Nov 13 00:32 COPYING.thirdparty
-rw-r--r--.  1 root root  2354 Nov 13 00:32 CREDITS
drwxrwxr-x.  3 root root    18 Nov 13 07:37 data
-rw-r--r--.  1 root root  8245 Nov 13 00:32 EXCEPTIONS-CLIENT
drwxrwxr-x.  3 root root    19 Nov 13 07:37 include
-rw-r--r--.  1 root root  8694 Nov 13 00:32 INSTALL-BINARY
drwxrwxr-x.  5 root root  4096 Sep 23 10:14 lib
drwxrwxr-x.  4 root root    30 Nov 13 07:37 man
drwxrwxr-x. 11 root root  4096 Nov 13 07:37 mysql-test
-rw-r--r--.  1 root root  2469 Nov 13 00:32 README.md
-rw-r--r--.  1 root root 19510 Nov 13 00:32 README-wsrep
drwxrwxr-x.  2 root root    30 Nov 13 07:37 scripts
drwxrwxr-x. 32 root root  4096 Nov 13 07:37 share
drwxrwxr-x.  4 root root  4096 Nov 13 07:37 sql-bench
drwxrwxr-x.  3 root root   275 Nov 13 07:37 support-files

第三步：创建程序用户
[root@centos7 ~]# useradd -r -s /sbin/nologin -d /data/mysql -c "mariadb user" mysql
[root@centos7 ~]# getent passwd mysql
mysql:x:989:983:mariadb user:/data/mysql:/sbin/nologin

第四步：创建数据库目录：存放数据库的数据
[root@centos7 ~]# ls -ld /data/mysql
ls: cannot access /data/mysql: No such file or directory
[root@centos7 ~]# mkdir /data/mysql
[root@centos7 ~]# install -d /data/mysql -o root -g mysql
[root@centos7 ~]# ls -ld /data/mysql
drwxr-xr-x. 2 root mysql 6 Nov 27 17:15 /data/mysql

第四步：生成系统数据库
[root@centos7 ~]# ls /usr/local/mysql/
bin                 include         README-wsrep
COPYING             INSTALL-BINARY  scripts
COPYING.thirdparty  lib             share
CREDITS             man             sql-bench
data                mysql-test      support-files
EXCEPTIONS-CLIENT   README.md
  
  安装数据库的脚本：生成系统数据库
  [root@centos7 ~]# ls /usr/local/mysql/scripts/
   mysql_install_db

[root@centos7 mysql]# pwd
/usr/local/mysql
[root@centos7 mysql]# scripts/mysql_install_db --user=mysql --datadir=/data/mysql

   确定是否生成了数据库
   [root@centos7 mysql]# ls /data/mysql/
    aria_log.00000001  ibdata1      mysql
    aria_log_control   ib_logfile0  performance_schema
    ib_buffer_pool     ib_logfile1  test

第五步：准备数据库的配置文件
[root@centos7 ~]# mkdir /etc/mysql
[root@centos7 ~]# ls /usr/local/mysql/support-files/
binary-configure        my-medium.cnf        policy
magic                   my-small.cnf         wsrep.cnf
my-huge.cnf             mysqld_multi.server  wsrep_notify
my-innodb-heavy-4G.cnf  mysql-log-rotate
my-large.cnf            mysql.server
[root@centos7 ~]# cd /usr/local/mysql/support-files/
[root@centos7 support-files]# cp my-huge.cnf /etc/mysql/my.cnf

第六步：修改配置文件，根据自己定义的数据路径进行修改
[root@centos7 ~]# vim /etc/mysql/my.cnf 
[mysqld]
datadir=/data/mysql
port            = 3306
socket          = /tmp/mysql.sock

第七步：准备程序服务的启动脚本
[root@centos7 ~]# ls /usr/local/mysql/support-files/
mysql.server
[root@centos7 ~]# cp /usr/local/mysql/support-files/mysql.server  /etc/init.d/
  可以改名，方便启动
[root@centos7 ~]# cd /etc/init.d/
[root@centos7 init.d]# ls
functions  mysql.server  netconsole  network  README
[root@centos7 init.d]# mv mysql.server mysqld

第八步：准备启动
[root@centos7 ~]# chkconfig --add mysqld
[root@centos7 ~]# chkconfig --list
mysqld         	0:off	1:off	2:on	3:on	4:on	5:on	6:off
netconsole     	0:off	1:off	2:off	3:off	4:off	5:off	6:off
network        	0:off	1:off	2:on	3:on	4:on	5:on	6:off

启动
[root@centos7 ~]# service mysqld restart
Restarting mysqld (via systemctl):                         [  OK  ]

准备PATH变量
[root@centos7 ~]# echo 'PATH=/usr/local/mysql/bin:$PATH' > /etc/profile.d/mysql.sh
[root@centos7 ~]# source /etc/profile.d/mysql.sh 

连接测试
[root@centos7 ~]# mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 10
Server version: 10.2.19-MariaDB-log MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> exit

查看数据库路径
方法1
MariaDB [(none)]> show variables like 'datadir'
    -> ;
+---------------+--------------+
| Variable_name | Value        |
+---------------+--------------+
| datadir       | /data/mysql/ |
+---------------+--------------+
1 row in set (0.01 sec)

方法2
MariaDB [(none)]> select @@datadir
    -> ;
+--------------+
| @@datadir    |
+--------------+
| /data/mysql/ |
+--------------+
1 row in set (0.00 sec)


执行初始化安装脚本
[root@centos7 ~]# ls /usr/local/mysql/bin/
 mysql_secure_installation
[root@centos7 ~]# mysql_secure_installation

``` 
