---
title: sudu
tags: linux服务
categories: linux服务
---
## 更改身份 

`su 切换身份:su –l  username –c  ‘command’ `

sudo  

&ensp;&ensp;来自sudo包，man 5 sudoers 

&ensp;&ensp;sudo能够授权指定用户在指定主机上运行某些命令。如果未授权用户尝试使 用 sudo，会提示联系管理员 

&ensp;&ensp;sudo可以提供日志，记录每个用户使用sudo操作 

&ensp;&ensp;sudo为系统管理员提供配置文件，允许系统管理员集中地管理用户的使用权 限和使用的主机 

&ensp;&ensp;sudo使用时间戳文件来完成类似“检票”的系统，默认存活期为5分钟的“入 场券” 

通过visudo命令编辑配置文件，具有语法检查功能  

&ensp;&ensp;visudo –c 检查语法 

&ensp;&ensp;visudo  -f /etc/sudoers.d/test 

## sudo 

`配置文件：/etc/sudoers, /etc/sudoers.d/ 可以在此目录创建文件写入授权信息 `

时间戳文件：/var/db/sudo 

日志文件：/var/log/secure 

配置文件支持使用通配符glob：  

&ensp;&ensp;？:任意单一字符  

&ensp;&ensp;* ：匹配任意长度字符  

&ensp;&ensp;[wxc]:匹配其中一个字符  

&ensp;&ensp;[!wxc]:除了这三个字符的其它字符  

&ensp;&ensp;\x : 转义    

&ensp;&ensp;[[alpha]] :字母  示例： /bin/ls [[alpha]]* 

配置文件规则有两类；  

&ensp;&ensp;1、别名定义:不是必须的  

&ensp;&ensp;2、授权规则:必须的

范例：查看配置文件修改用户的权限
```bash
[root@centos7 data]# cat /etc/sudoers
root	ALL=(ALL) 	ALL
daizhe  ALL=(ALL)   ALL
用户    登陆主机=（授权他代表谁的身份） 执什么操作
普通用户代表普通用户执行命令要使用sudo -u 执行cmd

%wheel  ALL=(ALL)       ALL
组
[root@centos7 ~]# id daizhe
uid=1000(daizhe) gid=1000(daizhe) groups=1000(daizhe)
[root@centos7 ~]# groupmems -a daizhe -g wheel
[root@centos7 ~]# id daizhe
uid=1000(daizhe) gid=1000(daizhe) groups=1000(daizhe),10(wheel)
[daizhe@centos7 ~]$ sudo mount /dev/sr0 /mnt
[sudo] password for daizhe: 
mount: /dev/sr0 is write-protected, mounting read-only


范例：让daizhe这个用户在所有主机上代表以root的身份挂载光盘设备（注意配置文件授权授权的权限要和用户执行的命令对应，授权多个操作，要用逗号做分隔，明确授权的指令和操作的路径）
daizhe ALL=(ALL) /usr/bin/mount /dev/sr0 /mnt

[root@centos7 data]# su - daizhe
[daizhe@centos7 ~]$ sudo mount /dev/sr0 /mnt

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for daizhe: 口令
mount: /dev/sr0 is write-protected, mounting read-only

配置文件写入
[root@centos7 data]# vim /etc/sudoers
daizhe ALL=(ALL) ALL

[daizhe@centos7 ~]$ sudo -i
[sudo] password for daizhe: 
[root@centos7 ~]# 
```

## sudoers 


授权规则格式：  

&ensp;&ensp;用户 登入主机=(代表用户) 命令 

示例：  

&ensp;&ensp;root ALL=(ALL)  ALL 

格式说明： 

&ensp;&ensp;user: 运行命令者的身份 

&ensp;&ensp;host: 通过哪些主机

&ensp;&ensp;(runas)：以哪个用户的身份 

&ensp;&ensp;command: 运行哪些命令 

`visudo=vim /etc/sudoers两个命令基本相同，但是visudo具有语法检查功能`

`visudo -c 可以检查此配置文件出错的信息,以及文件原有的权限`

`可以声明变量使得vi也带有颜色提示：export EDITOR=vim`

```bash
相当于授权daizhe用户有权限修改sudoers ，sudo的配置文件
[root@centos7 ~]# cd /etc/sudoers.d/
[root@centos7 sudoers.d]# ls
[root@centos7 sudoers.d]# vim a.txt
daizhe ALL=(ALL) sudoedit

[daizhe@centos7 ~]$ sudoedit /etc/sudoers
[sudo] password for daizhe: 
## Sudoers allows particular users to run various commands as
```

别名

Users和runas:   

&ensp;&ensp;&ensp;&ensp;username  

&ensp;&ensp;&ensp;&ensp;#uid  

&ensp;&ensp;&ensp;&ensp;%group_name  

&ensp;&ensp;&ensp;&ensp;%#gid  

&ensp;&ensp;&ensp;&ensp;user_alias|runas_alias 

host:  

&ensp;&ensp;&ensp;&ensp;ip或hostname  

&ensp;&ensp;&ensp;&ensp;network(/netmask)  

&ensp;&ensp;&ensp;&ensp;host_alias 

command:  

&ensp;&ensp;&ensp;&ensp;command name  

&ensp;&ensp;&ensp;&ensp;directory  

&ensp;&ensp;&ensp;&ensp;sudoedit  

&ensp;&ensp;&ensp;&ensp;Cmnd_Alias 

sudo别名和示例

别名有四种类型：User_Alias, Runas_Alias, Host_Alias ，Cmnd_Alias 

别名格式：[A-Z]([A-Z][0-9]_)* 

别名定义：  

&ensp;&ensp;&ensp;&ensp;Alias_Type NAME1 = item1, item2, item3 : NAME2 = item4, item5 

示例1：  

&ensp;&ensp;&ensp;&ensp;Student   ALL=(ALL)   ALL  

&ensp;&ensp;&ensp;&ensp;%wheel   ALL=(ALL)   ALL 

示例2：  

&ensp;&ensp;&ensp;&ensp;student ALL=(root)    /sbin/pidof,/sbin/ifconfig  

&ensp;&ensp;&ensp;&ensp;%wheel ALL=(ALL)  NOPASSWD: ALL 第一次也不需要输入口令

示例3  

&ensp;&ensp;&ensp;&ensp;User_Alias  NETADMIN= netuser1,netuser2  

&ensp;&ensp;&ensp;&ensp;Cmnd_Alias  NETCMD = /usr/sbin/ip  

&ensp;&ensp;&ensp;&ensp;NETADMIN ALL=（root） NETCMD 

示例4 

&ensp;&ensp;&ensp;&ensp;User_Alias SYSADER=wang,mage,%admins  

&ensp;&ensp;&ensp;&ensp;User_Alias DISKADER=tom 

&ensp;&ensp;&ensp;&ensp;Host_Alias SERS=www.magedu.com,172.16.0.0/24 

&ensp;&ensp;&ensp;&ensp;Runas_Alias OP=root  

&ensp;&ensp;&ensp;&ensp;Cmnd_Alias SYDCMD=/bin/chown,/bin/chmod  

&ensp;&ensp;&ensp;&ensp;Cmnd_Alias DSKCMD=/sbin/parted,/sbin/fdisk   

&ensp;&ensp;&ensp;&ensp;SYSADER SERS=   SYDCMD,DSKCMD  

&ensp;&ensp;&ensp;&ensp;DISKADER ALL=(OP)  DSKCMD  

示例5  

&ensp;&ensp;&ensp;&ensp;User_Alias ADMINUSER = adminuser1,adminuser2  

&ensp;&ensp;&ensp;&ensp;Cmnd_Alias ADMINCMD = /usr/sbin/useradd，/usr/sbin/usermod, /usr/bin/passwd [a-zA-Z]*, !/usr/bin/passwd root  

&ensp;&ensp;&ensp;&ensp;ADMINUSER ALL=(root) NOPASSWD:ADMINCMD， PASSWD:/usr/sbin/userdel 

示例6 

&ensp;&ensp;&ensp;&ensp;Defaults:wang runas_default=tom  

&ensp;&ensp;&ensp;&ensp;wang ALL=(tom,jerry) ALL 

示例7  

&ensp;&ensp;&ensp;&ensp;wang 192.168.1.6,192.168.1.8=(root)  /usr/sbin/,!/usr/sbin/useradd 

示例8  

&ensp;&ensp;&ensp;&ensp;wang   ALL=(ALL)  /bin/cat /var/log/messages* 

## sudo命令 

`ls -l /usr/bin/sudo` 

`sudo –i –u wang 切换身份 `

`sudo [-u user] COMMAND `  

&ensp;&ensp;-V 显示版本信息等配置信息  

&ensp;&ensp;-u user  默认为root  ,切换身份

&ensp;&ensp;-l,ll 列出用户在主机上可用的和被禁止的命令  

&ensp;&ensp;-v 再延长密码有效期限5分钟,更新时间戳  

&ensp;&ensp;-k 清除时间戳（1970-01-01），下次需要重新输密码  

&ensp;&ensp;-K 与-k类似，还要删除时间戳文件  

&ensp;&ensp;-b 在后台执行指令  

&ensp;&ensp;-p 改变询问密码的提示符号    

&ensp;&ensp;示例：-p ”password on %h for user %p:”  

