---
title: TCP_Wrappers
tags: linux服务
categories: linux服务
---
## TCP_Wrappers介绍

作者：Wieste Venema，IBM，Google 

工作在第四层（传输层）的TCP协议 

对有状态连接的特定服务进行安全检测并实现访问控制 

以库文件形式实现 

某进程是否接受`libwrap`的控制取决于发起此进程的程序在编译时是否针对 

&ensp;&ensp;libwrap进行编译的 

`判断服务程序是否能够由tcp_wrapper进行访问控制的方法： ` 

&ensp;&ensp;ldd  /PATH/TO/PROGRAM|grep libwrap.so  

&ensp;&ensp;strings PATH/TO/PROGRAM|grep libwrap.so 

## TCP_Wrappers的使用 

配置文件：/etc/hosts.allow, /etc/hosts.deny 默认文件内为空

帮助参考：man 5 hosts_access，man 5 hosts_options 

检查顺序：hosts.allow，hosts.deny（默认允许）    先允许后拒绝

&ensp;&ensp;注意：一旦前面规则匹配，直接生效，将不再继续

基本语法: 

&ensp;&ensp;daemon_list@host: client_list  [ :options :option… ] 

Daemon_list@host格式 

&ensp;&ensp;单个应用程序的二进制文件名，而非服务名，例如vsftpd 

&ensp;&ensp;以逗号或空格分隔的应用程序文件名列表，如:sshd,vsftpd 

&ensp;&ensp;ALL表示所有接受tcp_wrapper控制的服务程序 

&ensp;&ensp;主机有多个IP，可用@hostIP来实现控制 

&ensp;&ensp;如：in.telnetd@192.168.0.254 

客户端Client_list格式 

&ensp;&ensp;以逗号或空格分隔的客户端列表 

&ensp;&ensp;基于IP地址：192.168.10.1   192.168.1. 

&ensp;&ensp;基于主机名：www.magedu.com  .magedu.com 较少用 

&ensp;&ensp;基于网络/掩码：192.168.0.0/255.255.255.0 

&ensp;&ensp;基于net/prefixlen: 192.168.1.0/24（CentOS7） 


&ensp;&ensp;基于网络组（NIS 域）：@mynetwork 

&ensp;&ensp;内置ACL：ALL，LOCAL，KNOWN，UNKNOWN，PARANOID 

&ensp;&ensp;EXCEPT排除用法：    

&ensp;&ensp;&ensp;&ensp;示例：  vsftpd: 172.16. EXCEPT 172.16.100.0/24 EXCEPT 172.16.100.1 
![排除法设定黑名单](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E9%BB%91%E5%90%8D%E5%8D%95%E6%8B%92%E7%BB%9D.png)


示例：只允许192.168.1.0/24的主机访问sshd    

&ensp;&ensp;/etc/hosts.allow     

&ensp;&ensp;&ensp;&ensp;sshd: 192.168.1.             

&ensp;&ensp;/etc/hosts.deny        

&ensp;&ensp;&ensp;&ensp;sshd :ALL  

示例：

只允许192.168.1.0/24的主机访问telnet和vsftpd服务

&ensp;&ensp;/etc/hosts.allow    

&ensp;&ensp;&ensp;&ensp;vsftpd,in.telnetd: 192.168.1.   

&ensp;&ensp;/etc/host.deny    

&ensp;&ensp;&ensp;&ensp;vsftpd,in.telnetd:  ALL  
 


[:options]选项： 

帮助：man 5 hosts_options  

&ensp;&ensp;deny 主要用在/etc/hosts.allow定义“拒绝”规则   

&ensp;&ensp;&ensp;&ensp;如：vsftpd: 172.16. :deny  

&ensp;&ensp;allow 主要用在/etc/hosts.deny定义“允许”规则   

&ensp;&ensp;&ensp;&ensp;如：vsftpd:172.16. :allow  

&ensp;&ensp;spawn 启动一个外部程序完成执行的操作 ，访问设置的服务时，触发其他操作

&ensp;&ensp;twist  实际动作是拒绝访问,使用指定的操作替换当前服务,标准I/O和ERROR 发送到客户端,默认至/dev/null 


`测试工具：`  

&ensp;&ensp;tcpdmatch [-d] daemon[@host] client  

&ensp;&ensp;&ensp;&ensp;-d 测试当前目录下的hosts.allow和hosts.deny 

范例：使用测试工具
```bash
自己创建一个目录，在目录下创建两个此时文件写入拒绝和允许策略
[root@centos7 test]# ls
hosts.allow  hosts.deny
[root@centos7 test]# cat hosts.deny 
in.telnetd:ALL
[root@centos7 test]# tcpdmatch -d in.ternetd 192.168.131.120
client:   address  192.168.131.120
server:   process  in.ternetd
access:   granted
```

示例 

sshd: ALL :spawn echo "$(date +%%F) login  attempt  from  %c  to  %s,%d" >>/var/log/sshd.log 

说明： 

&ensp;&ensp;在/etc/hosts.allow中添加，允许登录，并记录日志 

&ensp;&ensp;在/etc/hosts.deny中添加，拒绝登录，并记录日志

&ensp;&ensp;%c 客户端信息   

&ensp;&ensp;%s 服务器端信息 

&ensp;&ensp;%d 服务名   

&ensp;&ensp;%p 守护进程的PID 

&ensp;&ensp;%% 表示% 

```bash
写在服务端的hosts.allow文件中，变向拒绝，也会在客户端收到提示信息
vsftpd: 172.16.  :twist /bin/echo “connection prohibited” 
```
范例：仅授权用户查看/var/log/目录下开头为messages开头的文件
```bash
[root@centos7 sudoers.d]# vim a.txt 
daizhe ALL=(ALL) /bin/cat /var/log/messages*,!/bin/cat /var/logmessages
* *
```
范例：在centos7上拒绝centos6主机ssh连接和telnet连接
```bash
[root@centos6 ~]# cat /etc/hosts.deny 
in.ternetd,sshd: 192.168.131.180
```
范例：如果主机上又多块网卡设备，也可以针对一块网卡设置次策略
```bash
仅设置对192.168.131.129这块网卡设置被访问时设置的策列
[root@centos6 ~]# cat /etc/hosts.deny 
in.ternetd,sshd@192.168.131.129: 192.168.131.180
```
