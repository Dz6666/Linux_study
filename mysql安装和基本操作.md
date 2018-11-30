---
title: mysql安装和基本操作
tags: linux服务
categories: linux服务
---
## MYSQL的特性 
`插件式存储引擎：也称为“表类型”，存储管理器有多种实现版本，功能和特 性可能均略有差别；用户可根据需要灵活选择,Mysql5.5.5开始&innoDB引擎是 MYSQL默认引擎 `  
&ensp;&ensp;MyISAM ==> Aria               
&ensp;&ensp;InnoDB ==> XtraDB   
单进程，多线程     
诸多扩展和新特性     
提供了较多测试组件   
开源 

## 安装MYSQL 
`Mariadb安装方式：`   
1、源代码：编译安装   
2、二进制格式的程序包：展开至特定路径，并经过简单配置后即可使用   
3、程序包管理器管理的程序包   
CentOS 安装光盘   
项目官方：  https://downloads.mariadb.org/mariadb/repositories/   
国内镜像：  https://mirrors.tuna.tsinghua.edu.cn/mariadb/mariadbx.y.z/yum/centos/7/x86_64/     

## RPM包安装MySQL 
RPM包安装           
&ensp;&ensp;CentOS 7：安装光盘直接提供          
&ensp;&ensp;&ensp;&ensp;mariadb-server   服务器包           
&ensp;&ensp;&ensp;&ensp;mariadb       客户端工具包        
&ensp;&ensp;CentOS 6   
提高安全性      
&ensp;&ensp;`mysql_secure_installation   `
&ensp;&ensp;&ensp;&ensp;`设置数据库管理员root口令  ` 
&ensp;&ensp;&ensp;&ensp;`禁止root远程登录   `
&ensp;&ensp;&ensp;&ensp;`删除anonymous用户帐号   `
&ensp;&ensp;&ensp;&ensp;`删除test数据库   `

## MariaDB程序 
客户端程序：    
&ensp;&ensp;mysql: 交互式的CLI工具    
&ensp;&ensp;mysqldump：备份工具，基于mysql协议向mysqld发起查询请求，并将查得的所   
有数据转换成insert等写操作语句保存文本文件中    
&ensp;&ensp;mysqladmin：基于mysql协议管理mysqld    
&ensp;&ensp;mysqlimport：数据导入工具   
MyISAM存储引擎的管理工具：    
&ensp;&ensp;myisamchk：检查MyISAM库    
&ensp;&ensp;myisampack：打包MyISAM表，只读   
服务器端程序    
&ensp;&ensp;mysqld_safe      
&ensp;&ensp;mysqld    
&ensp;&ensp;mysqld_multi    多实例（一个程序在系统上运行多次，多个进程，缺点仅能实现单一版本的多实例） ，示例：mysqld_multi  --example   

## 用户账号   
`mysql用户账号由两部分组成：    `
&ensp;&ensp;'USERNAME'@'HOST‘     
说明：    
&ensp;&ensp;HOST限制此用户可通过哪些远程主机连接mysql服务器    
&ensp;&ensp;支持使用通配符：     
&ensp;&ensp;&ensp;&ensp;% 匹配任意长度的任意字符        
&ensp;&ensp;&ensp;&ensp;172.16.0.0/255.255.0.0 或 172.16.%.%     
&ensp;&ensp;&ensp;&ensp;_  匹配任意单个字符  

## Mysql 客户端   
mysql使用模式：   
交互式模式：    
&ensp;&ensp;可运行命令有两类：     
&ensp;&ensp;客户端命令：      
&ensp;&ensp;&ensp;&ensp;\h, help        
&ensp;&ensp;&ensp;&ensp;\u，use      
&ensp;&ensp;&ensp;&ensp;\s，status      
&ensp;&ensp;&ensp;&ensp;\!，system     
&ensp;&ensp;服务器端命令：      
&ensp;&ensp;&ensp;&ensp;SQL语句， 需要语句结束符；   
脚本模式：    
&ensp;&ensp;mysql –uUSERNAME -pPASSWORD < /path/somefile.sql    
&ensp;&ensp;mysql> source /path/from/somefile.sql   
  
## Mysql客户端   
mysql客户端可用选项：     
-A, --no-auto-rehash 禁止补全    
-u, --user=  用户名,默认为root    
-h, --host=  服务器主机,默认为localhost    
-p, --passowrd= 用户密码,建议使用-p,默认为空密码    
-P, --port=  服务器端口    
-S, --socket= 指定连接socket文件路径    
-D, --database=  指定默认数据库    
-C, --compress 启用压缩    
-e   “SQL“ 执行SQL命令        
-V, --version 显示版本         
-v  --verbose 显示详细信息      
--print-defaults   获取程序默认使用的配置    
  
## socket地址   
服务器监听的两种socket地址：    
&ensp;&ensp;ip socket: 监听在tcp的3306端口，支持远程通信     
&ensp;&ensp;unix sock: 监听在sock文件上，仅支持本机通信          
&ensp;&ensp;&ensp;&ensp;如：/var/lib/mysql/mysql.sock)          
说明：host为localhost,127.0.0.1时自动使用unix sock   

## 执行命令 
运行mysql命令：默认空密码登录    
&ensp;&ensp;mysql>use mysql    
&ensp;&ensp;mysql>select user();查看当前用户    
&ensp;&ensp;mysql>SELECT User,Host,Password FROM user;   
登录系统：mysql  –uroot  –p    
客户端命令：本地执行    
&ensp;&ensp;mysql> help    
&ensp;&ensp;每个命令都完整形式和简写格式  
&ensp;&ensp;mysql> status 或 \s   
服务端命令：通过mysql协议发往服务器执行并取回结果  每个命令末尾都必须使用命令结束符号，默认为分号    
&ensp;&ensp;示例：SELECT VERSION();    

## 服务器端配置 
服务器端(mysqld)：工作特性有多种配置方式   
1、命令行选项：   
2、配置文件：类ini格式      
集中式的配置，能够为mysql的各应用程序提供配置信息   
&ensp;&ensp;[mysqld]   
&ensp;&ensp;[mysqld_safe]   
&ensp;&ensp;[mysqld_multi]   
&ensp;&ensp;[mysql]   
&ensp;&ensp;[mysqldump]   
&ensp;&ensp;[server]   
&ensp;&ensp;[client]   
格式：parameter = value     
说明：_和- 相同    
&ensp;&ensp;1，ON，TRUE意义相同， 0，OFF，FALSE意义相同   

## 配置文件 
`配置文件：   `  
`后面覆盖前面的配置文件，顺序如下：下面的优先级高  ` 
/etc/my.cnf              Global选项   
/etc/mysql/my.cnf        Global选项   
SYSCONFDIR/my.cnf        Global选项    
$MYSQL_HOME/my.cnf       Server-specific 选项     
--defaults-extra-file= path     
~/.my.cnf                User-specific 选项   

## MairaDB配置   
侦听3306/tcp端口可以在绑定有一个或全部接口IP上   
vim /etc/my.cnf       
[mysqld]       
skip-networking=1        
关闭网络连接，只侦听本地客户端， 所有和服务器的交互都通过一个socket实 现，socket的配置存放在/var/lib/mysql/mysql.sock） 可在/etc/my.cnf修改   

## 通用二进制格式安装过程   
二进制格式安装过程   
(1) 准备用户   
&ensp;&ensp;groupadd -r -g 306 mysql   
&ensp;&ensp;useradd -r -g 306 -u 306 –d /data/mysql  mysql   
(2) 准备数据目录，建议使用逻辑卷    
&ensp;&ensp;mkdir /data/mysql   
&ensp;&ensp;chown mysql:mysql  /data/mysql   
(3) 准备二进制程序   
&ensp;&ensp;tar xf mariadb-VERSION-linux-x86_64.tar.gz -C   /usr/local   
&ensp;&ensp;cd /usr/local   
&ensp;&ensp;ln -sv mariadb-VERSION mysql   
&ensp;&ensp;chown -R root:mysql /usr/local/mysql/     
(4) 准备配置文件   
&ensp;&ensp;mkdir /etc/mysql/  
&ensp;&ensp;cp support-files/my-large.cnf /etc/mysql/my.cnf   
&ensp;&ensp;[mysqld]中添加三个选项：   
&ensp;&ensp;datadir = /data/mysql   
&ensp;&ensp;innodb_file_per_table = on   
&ensp;&ensp;skip_name_resolve = on    禁止主机名解析，建议使用     
(5)创建数据库文件     
&ensp;&ensp;cd /usr/local/mysql/    
&ensp;&ensp;./scripts/mysql_install_db --datadir=/data/mysql --user=mysql   
(6)准备服务脚本，并启动服务        
&ensp;&ensp;cp ./support-files/mysql.server /etc/rc.d/init.d/mysqld   
&ensp;&ensp;chkconfig --add mysqld 
service mysqld start   
(7)PATH路径     
&ensp;&ensp;echo ‘PATH=/user/local/mysql/bin:$PATH’ >   /etc/profile.d/mysql     
(8)安全初始化    
 &ensp;&ensp;/user/local/mysql/bin/mysql_secure_installation  

## 源码编译安装mariadb   
安装包         
&ensp;&ensp;yum install bison bison-devel  zlib-devel libcurl-devel libarchive-devel  boost-devel  gcc  gcc-c++  cmake ncurses-devel gnutls-devel libxml2-devel openssl-devel libevent-devel libaio-devel        
做准备用户和数据目录   
&ensp;&ensp;useradd –r –s /sbin/nologin –d  /data/mysql/  mysql   
&ensp;&ensp;mkdir   /data/mysql   
&ensp;&ensp;chown  mysql.mysql  /data/mysql   
&ensp;&ensp;tar xvf   mariadb-10.2.18.tar.gz    
cmake 编译安装     
&ensp;&ensp;cmake的重要特性之一是其独立于源码(out-of-source)的编译功能，即编译工作可以在 另一个指定的目录中而非源码目录中进行，这可以保证源码目录不受任何一次编译的影 响，因此在同一个源码树上可以进行多次不同的编译，如针对于不同平台编译  编译选项:https://dev.mysql.com/doc/refman/5.7/en/source-configuration-options.html  

 
cd mariadb-10.2.18/   
cmake . \   
-DCMAKE_INSTALL_PREFIX=/app/mysql \   
-DMYSQL_DATADIR=/data/mysql/ \   
-DSYSCONFDIR=/etc \     
-DMYSQL_USER=mysql \     
-DWITH_INNOBASE_STORAGE_ENGINE=1 \   
-DWITH_ARCHIVE_STORAGE_ENGINE=1 \   
-DWITH_BLACKHOLE_STORAGE_ENGINE=1 \   
-DWITH_PARTITION_STORAGE_ENGINE=1  \     
-DWITHOUT_MROONGA_STORAGE_ENGINE=1 \   
-DWITH_DEBUG=0 \   
-DWITH_READLINE=1 \   
-DWITH_SSL=system \     
-DWITH_ZLIB=system \     
-DWITH_LIBWRAP=0 \   
-DENABLED_LOCAL_INFILE=1  \    
-DMYSQL_UNIX_ADDR=/data/mysql/mysql.sock \   
-DDEFAULT_CHARSET=utf8 \   
-DDEFAULT_COLLATION=utf8_general_ci make && make install    
提示：如果出错，执行rm -f CMakeCache.txt    

准备环境变量   
echo 'PATH=/app/mysql/bin:$PATH' > /etc/profile.d/mysql.sh   
.     /etc/profile.d/mysql.sh   
生成数据库文件   
cd   /app/mysql/   
scripts/mysql_install_db --datadir=/data/mysql/ --user=mysql   
准备配置文件       
cp  /app/mysql/support-files/my-huge.cnf   /etc/my.cnf   
准备启动脚本      
cp /app/mysql/support-files/mysql.server  /etc/init.d/mysqld   
启动服务     
chkconfig --add mysqld ;service mysqld start     

## 关系型数据库的常见组件   
数据库：database   
表：table  行：row  列：column   
索引：index   
视图：view   
用户：user   
权限：privilege   
存储过程：procedure   
存储函数：function   
触发器：trigger   
事件调度器：event scheduler，任务计划   

## SQL语言的兴起与语法标准 
20世纪70年代，IBM开发出SQL，用于DB2   
1981年，IBM推出SQL/DS数据库   
业内标准微软和Sybase的T-SQL，Oracle的PL/SQL   
SQL作为关系型数据库所使用的标准语言，最初是基于IBM的实现在1986年被 批准的。1987年，“国际标准化组织(ISO)”把ANSI(美国国家标准化组织) SQL作为国际标准。   
SQL：ANSI SQL   
SQL-1986, SQL-1989, SQL-1992, SQL-1999, SQL-2003 , SQL-2008  SQL-2011   

## SQL语言规范   
在数据库系统中，SQL语句不区分大小写(建议用大写)    
SQL语句可单行或多行书写，以“;”结尾   
关键词不能跨多行或简写   
用空格和缩进来提高语句的可读性   
子句通常位于独立行，便于编辑，提高可读性   
注释：   
&ensp;&ensp;SQL标准：    
&ensp;&ensp;/*注释内容*/   多行注释    
&ensp;&ensp;-- 注释内容    单行注释，注意有空格   
&ensp;&ensp;MySQL注释：  #     

## 数据库对象   
数据库的组件(对象)：    
&ensp;&ensp;数据库、表、索引、视图、用户、存储过程、函数、触发器、事件调度器等   
命名规则：   
&ensp;&ensp;必须以字母开头   
&ensp;&ensp;可包括数字和三个特殊字符（# _ $）  
&ensp;&ensp;不要使用MySQL的保留字   
&ensp;&ensp;同一database(Schema)下的对象不能同名   

## SQL语句分类   
SQL语句分类：   
DDL: Data Defination Language 数据定义语言     
&ensp;&ensp;CREATE，DROP，ALTER   
DML: Data Manipulation Language 数据操纵语言     
&ensp;&ensp;INSERT，DELETE，UPDATE    
DCL：Data Control Language 数据控制语言      
&ensp;&ensp;GRANT，REVOKE，COMMIT，ROLLBACK   
DQL：Data Query Language 数据查询语言     
&ensp;&ensp;SELECT 

## SQL语句构成 
SQL语句构成：   
&ensp;&ensp;Keyword组成clause   
&ensp;&ensp;多条clause组成语句   
示例：   
SELECT *                   SELECT子句   
FROM products              FROM子句  
WHERE price>400            WHERE子句         
说明：一组SQL语句，由三个子句构成，SELECT,FROM和WHERE是关键字     

## `数据库操作 `  
创建数据库：    
&ensp;&ensp;CREATE DATABASE|SCHEMA [IF NOT EXISTS] 'DB_NAME';     
&ensp;&ensp;CHARACTER SET 'character set name'    
&ensp;&ensp;COLLATE 'collate name'   
删除数据库    
&ensp;&ensp;DROP DATABASE|SCHEMA [IF EXISTS] 'DB_NAME';    
查看支持所有字符集：  
&ensp;&ensp;SHOW CHARACTER SET;   
查看支持所有排序规则：  
&ensp;&ensp;SHOW COLLATION;   
获取命令使用帮助：   
&ensp;&ensp;mysql> HELP KEYWORD;   
查看数据库列表：    
&ensp;&ensp;mysql> SHOW DATABASES;  



 
 
 