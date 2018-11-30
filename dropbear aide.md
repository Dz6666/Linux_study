---
title: dropbear、AIDE
tags: linux服务
categories: linux服务
---
## 编译安装dropbear示例 

`ssh协议的另一个实现：dropbear `

源码编译安装： 

&ensp;&ensp;1、安装开发包组:yum groupinstall “Development tools” 

&ensp;&ensp;2、下载dropbear-2017.75.tar.bz2 

&ensp;&ensp;3、tar xf dropbear-2017.75.tar.bz2 

&ensp;&ensp;4、less INSTALL README 

&ensp;&ensp;5、./configure 

&ensp;&ensp;6、make PROGRAMS="dropbear dbclient dropbearkey dropbearconvert scp" 

&ensp;&ensp;7、make PROGRAMS="dropbear dbclient dropbearkey dropbearconvert scp" install 

启动ssh服务： 

&ensp;&ensp;8、ls /usr/local/sbin/  /usr/local/bin/ 

&ensp;&ensp;9、/usr/local/sbin/dropbear  -h 

&ensp;&ensp;10、mkdir /etc/dropbear 

&ensp;&ensp;11、dropbearkey -t rsa -f /etc/dropbear/dropbear_rsa_host_key -s 2048 

&ensp;&ensp;12、dropbearkey -t dss -f /etc/dropbear/dropbear_dsa_host_key  

&ensp;&ensp;13、dropbear -p :2222 -F –E   #前台运行  dropbear -p :2222 #后台运行 客户端访问： 

&ensp;&ensp;14、ssh -p 2222 root@127.0.0.1 • 15、dbclient -p 2222 root@127.0.0.1 

范例：源码编译安装dropbear
```bash
http://matt.ucc.asn.au/dropbear/dropbear.html
下载安装包
[root@centos6 ~]# wget http://matt.ucc.asn.au/dropbear/dropbear-2017.75.tar.bz2
解压
[root@centos7 ~]# tar xvf dropbear-2017.75.tar.bz2 
[root@centos7 data]# ls
dropbear-2017.75  dropbear-2017.75.tar.bz2

[root@centos7 data]# cd dropbear-2017.75

安装包组
[root@centos7 httpd-2.4.25]# yum groupinstall "development tools" -y

指定安装路径
[root@centos7 dropbear-2017.75]# ./configure --prefix=/app/dropbear --sysconfdir=/etc/dropbear --disable-status
安装依赖的包
[root@centos7 dropbear-2017.75]# yum install zlib-devel 

编译安装
[root@centos7 dropbear-2017.75]# make && make install


[root@centos7 app]# mkdir /etc/dropbear

程序路径
[root@centos7 sbin]# cd /app/dropbear/sbin/
-rwxr-xr-x. 1 root root 224960 Nov 17 11:00 dropbear

生成私钥对应key
[root@centos7 bin]# cd /app/dropbear/bin
[root@centos7 bin]# ll
total 484
-rwxr-xr-x. 1 root root 219776 Nov 17 11:00 dbclient
-rwxr-xr-x. 1 root root 137992 Nov 17 11:00 dropbearconvert
-rwxr-xr-x. 1 root root 133224 Nov 17 11:00 dropbearkey
[root@centos7 bin]# ./dropbearkey -t rsa -f /etc/dropbear/dropbear_rsa_host_key
Generating key, this may take a while...
Public key portion is:
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCfzhjxliyVoMc0bF/s+ty2gkmF8CFp/Wnjutv9u0KxnxyYBfx7aFyi1lBQb1rCNzNv4HvHJ0s7obXSd/aTbccJLBvkgeRZIMZkQst9tg5NmPQSxAETvVsVp+/lFnWzOwEd75OR7UD6qxcYHq1W4AZ1KZx5hblVR2WtjLd8pG1HjVbHM63f7tYxIVPIas45lmh/9Hy6r1WDeeULWIged6yDFsrZcUrW8I4ZqjBgKRPBf4CPPjoOyAPeRi2ubZSmFIxW/jc4IX/CKEtOLtScUKJrrL8GwgypX8XPStBtfp/2woVNVYsVXry6F7MDeFeVg0qfYEXmxOVBv/V3WpPXITXD root@centos7.com
Fingerprint: md5 50:00:87:51:26:a6:02:8d:d7:89:81:73:2d:03:31:ab
[root@centos7 dropbear]# ll
total 4
-rw-------. 1 root root 805 Nov 17 11:07 dropbear_rsa_host_key

启用程序 (bropbear端口启动默认和ssh端口冲突，开启时需手动指定端口号)
[root@centos7 sbin]# cd /app/dropbear/sbin/
[root@centos7 sbin]# ll
-rwxr-xr-x. 1 root root 224960 Nov 17 11:00 dropbear
[root@centos7 sbin]# ./dropbear  -FE -p 9527
```

## AIDE 

&ensp;&ensp;当一个入侵者进入了你的系统并且种植了木马，通常会想办法来隐蔽这个木马 （除了木马自身的一些隐蔽特性外，他会尽量给你检查系统的过程设置障碍）， 通常入侵者会修改一些文件，比如管理员通常用ps -aux来查看系统进程，那 么入侵者很可能用自己经过修改的ps程序来替换掉你系统上的ps程序，以使用 ps命令查不到正在运行的木马程序。如果入侵者发现管理员正在运行crontab 作业，也有可能替换掉crontab程序等等。所以由此可以看出对于系统文件或 是关键文件的检查是很必要的。目前就系统完整性检查的工具用的比较多的有 两款：Tripwire和AIDE，前者是一款商业软件，后者是一款免费的但功能也很 强大的工具 

&ensp;&ensp;AIDE(Advanced Intrusion Detection Environment) • 高级入侵检测环境)是一个入侵检测工具，主要用途是检查文件的完整性，审计计算机 上的那些文件被更改过了。  • AIDE能够构造一个指定文件的数据库，它使用aide.conf作为其配置文件。AIDE数据库 能够保存文件的各种属性，包括：权限(permission)、索引节点序号(inode number)、 所属用户(user)、所属用户组(group)、文件大小、最后修改时间(mtime)、创建时间 (ctime)、最后访问时间(atime)、增加的大小以及连接数。AIDE还能够使用下列算法： sha1、md5、rmd160、tiger，以密文形式建立每个文件的校验码或散列号. • 这个数据库不应该保存那些经常变动的文件信息，例如：日志文件、邮件、/proc文件 系统、用户起始目录以及临时目录. 
 

`安装 ` 

&ensp;&ensp;yum install aide 

`修改配置文件 ` 

&ensp;&ensp;vim /etc/aide.conf (指定对哪些文件进行检测) 

&ensp;&ensp;/test/chameleon R 

&ensp;&ensp;/bin/ps R+a 

&ensp;&ensp;/usr/bin/crontab R+a 

&ensp;&ensp;/etc    PERMS 

&ensp;&ensp;!/etc/mtab   #“!”表示忽略这个文件的检查 

&ensp;&ensp;R=p+i+n+u+g+s+m+c+md5 权限+索引节点+链接数+用户+组+大小+ 

&ensp;&ensp;最后一次修改时间+创建时间+md5校验值  

&ensp;&ensp;NORMAL = R+rmd60+sha256 

初始化默认的AIDE的库： 

&ensp;&ensp;/usr/local/bin/aide --init 

生成检查数据库（建议初始数据库存放到安全的地方）  

&ensp;&ensp;cd /var/lib/aide  

&ensp;&ensp;mv aide.db.new.gz aide.db.gz 

检测：  -C

&ensp;&ensp;/usr/local/bin/aide --check 

更新数据库  

&ensp;&ensp;aide --update 

版本信息 -V

&ensp;&ensp;aide -v

范例：使用aide监控文件

```bash
查看aide配置文件查看监控规则
[root@centos7 ~]# cat /etc/aide.conf 
@@define DBDIR /var/lib/aide     aide数据库
@@define LOGDIR /var/log/aide    aide日志文件

database=file:@@{DBDIR}/aide.db.gz         文件数据库的原数据库默认不会自动生成
database_out=file:@@{DBDIR}/aide.db.new.gz 生成的文件数据库，进行比对要和上面生成的原数据库比对

gzip_dbout=yes   是否支持压缩

监控哪些属性
# These are the default rules.
#
#p:      permissions               权限      
#i:      inode:                    节点号
#n:      number of links           连接数
#u:      user                      用户
#g:      group
#s:      size
#b:      block count
#m:      mtime
#a:      atime
#c:      ctime
#S:      check for growing size
#acl:           Access Control Lists
#selinux        SELinux security context
#xattrs:        Extended file attributes
#md5:    md5 checksum
#sha1:   sha1 checksum
#sha256:        sha256 checksum
#sha512:        sha512 checksum
#rmd160: rmd160 checksum
#tiger:  tiger checksum

#haval:  haval checksum (MHASH only)
#gost:   gost checksum (MHASH only)
#crc32:  crc32 checksum (MHASH only)
#whirlpool:     whirlpool checksum (MHASH only)

FIPSR = p+i+n+u+g+s+m+c+acl+selinux+xattrs+sha256    代表上述监控的集合（监控文件的属性、规则）
.....

自己定义监控的文件和监控文件的哪些属性

TEST = p+s+u         自己定义的监控属性
/data/  TEST         监控/data下的所有文件
!/data/a.txt         排除/data下的a.txt文件不监控

初始化，记录监控文件的属性
[root@centos7 data]# aide --init
AIDE, version 0.15.1
### AIDE database at /var/lib/aide/aide.db.new.gz initialized.          监控文件的数据库放置路径

[root@centos7 data]# ll /var/lib/aide/aide.db.new.gz 
-rw-------. 1 root root 144 Nov 17 12:25 /var/lib/aide/aide.db.new.gz

比较就要移动改名字
[root@centos7 data]# mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz
[root@centos7 data]# aide -C 
AIDE, version 0.15.1
### All files match AIDE database. Looks okay!

修改查看是否变化
[root@centos7 data]# touch aa.txt
[root@centos7 data]# aide -C 
AIDE 0.15.1 found differences between database and filesystem!!
Start timestamp: 2018-11-17 13:26:26

Summary:
  Total number of files:	3
  Added files:			1
  Removed files:		0
  Changed files:		0


---------------------------------------------------
Added files:
---------------------------------------------------

added: /data/aa.txt
```