---
title: mairadb实现源码安装
tags: linux服务
categories: linux服务
---
## mairadb实现源码安装10.2.19
```bash
第一步：安装相关的依赖包
[root@centos7 yum.repos.d]# yum install bison bison-devel  zlib-devel libcurl-devel libarchive-devel  boost-devel  gcc  gcc-c++  cmake ncurses-devel gnutls-devel libxml2-devel openssl-devel libevent-devel libaio-devel

第二步：创建对应的账号              （数据库存放数据的路径）
[root@centos7 yum.repos.d]# useradd -r -s /sbin/nologin -d  /data/mysql/  mysql 

第三步：创建数据对应的数据库路径
[root@centos7 ~]# mkdir /data/mysql
[root@centos7 ~]# chown mysql:mysql /data/mysql

第四步：下载源码解压
[root@centos7 ~]# ls
mariadb-10.2.19.tar.gz
[root@centos7 ~]# tar xvf mariadb-10.2.19.tar.gz 
[root@centos7 ~]# ls
mariadb-10.2.19        
mariadb-10.2.19.tar.gz
[root@centos7 ~]# du -sh mariadb-10.2.19
506M	mariadb-10.2.19

第五步：cmack编译
[root@centos7 ~]# cd mariadb-10.2.19/
[root@centos7 mariadb-10.2.19]# cmake . \
-DCMAKE_INSTALL_PREFIX=/app/mysql \
-DMYSQL_DATADIR=/data/mysql/ \
-DSYSCONFDIR=/etc \
-DMYSQL_USER=mysql \
-DWITH_INNOBASE_STORAGE_ENGINE=1 \
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \
-DWITH_PARTITION_STORAGE_ENGINE=1 \
-DWITHOUT_MROONGA_STORAGE_ENGINE=1 \
-DWITH_DEBUG=0 \
-DWITH_READLINE=1 \
-DWITH_SSL=system \
-DWITH_ZLIB=system \
-DWITH_LIBWRAP=0 \
-DENABLED_LOCAL_INFILE=1 \
-DMYSQL_UNIX_ADDR=/data/mysql/mysql.sock \
-DDEFAULT_CHARSET=utf8 \
-DDEFAULT_COLLATION=utf8_general_ci


 多线程编译
[root@centos7 ~]# make -j 4 && make install

[root@centos7 mariadb-10.2.19]# ls /app/mysql/
bin                 EXCEPTIONS-CLIENT  README.md
COPYING             include            README-wsrep
COPYING.thirdparty  INSTALL-BINARY     scripts
CREDITS             lib                share
data                man                sql-bench
docs                mysql-test         support-files


第五步：生成数据库文件
[root@centos7 mysql]# scripts/mysql_install_db --user=mysql --datadir=/data/mysql

[root@centos7 mysql]# ls -l  /data/mysql/
total 110620
-rw-rw----. 1 mysql mysql    16384 Nov 27 21:02 aria_log.00000001
-rw-rw----. 1 mysql mysql       52 Nov 27 21:02 aria_log_control
-rw-rw----. 1 mysql mysql      938 Nov 27 21:02 ib_buffer_pool
-rw-rw----. 1 mysql mysql 12582912 Nov 27 21:02 ibdata1
-rw-rw----. 1 mysql mysql 50331648 Nov 27 21:02 ib_logfile0
-rw-rw----. 1 mysql mysql 50331648 Nov 27 21:02 ib_logfile1
drwx------. 2 mysql root      4096 Nov 27 21:02 mysql
drwx------. 2 mysql mysql       20 Nov 27 21:02 performance_schema
drwx------. 2 mysql root         6 Nov 27 21:02 test

第六步：设置PATH变量
[root@centos7 mysql]# echo 'PATH=/app/mysql/bin:$PATH' > /etc/profile.d/mysql.sh
[root@centos7 mysql]# source /etc/profile.d/mysql.sh 

第七步：拷贝模板配置文件
[root@centos7 mysql]# pwd
/app/mysql
[root@centos7 mysql]# cp support-files/my-huge.cnf /etc/my.cnf
cp: overwrite ‘/etc/my.cnf’? y

第八步：设置启动脚本
[root@centos7 ~]# cd /app/mysql/support-files/
[root@centos7 support-files]# ls
binary-configure        my-medium.cnf        policy
magic                   my-small.cnf         wsrep.cnf
my-huge.cnf             mysqld_multi.server  wsrep_notify
my-innodb-heavy-4G.cnf  mysql-log-rotate
my-large.cnf            mysql.server
[root@centos7 support-files]# cp mysql.server /etc/init.d/mysqld

[root@centos7 ~]# chkconfig --add mysqld
[root@centos7 ~]# chkconfig --list

Note: This output shows SysV services only and does not include native
      systemd services. SysV configuration data might be overridden by native
      systemd configuration.

      If you want to list systemd services use 'systemctl list-unit-files'.
      To see services enabled on particular target use
      'systemctl list-dependencies [target]'.

mysqld         	0:off	1:off	2:on	3:on	4:on	5:on	6:off

启动
[root@centos7 ~]# service mysqld restart
Restarting mysqld (via systemctl):                         [  OK  ]

[root@centos7 ~]# ss -ntl
LISTEN      0      80     :::3306               :::*     

安全脚本
mysql_secure_installation
```
