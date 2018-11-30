---
title: PXE自动化安装系统
tags: linux服务
categories: linux服务
---
## tftp
`范例：搭建TFTP服务器`在centos6上tftp也是非独立服务，启动tftp启动：chkconfig tftp on  &ensp;&ensp;service xinetd restart  
tftp:端口udp 69服务器端口
```bash
查看tftp的工作目录
[root@centos7 ~]# rpm -ql tftp-server
/usr/lib/systemd/system/tftp.service服务
/var/lib/tftpboot工作目录

服务端安装TFTP服务端包tftp-server
[root@centos7 yum.repos.d]# yum install tftp-server -y
[root@centos7 yum.repos.d]# systemctl restart tftp
[root@centos7 yum.repos.d]# ss -ntlu

udp   UNCONN     0      0      :::69                 :::*   

客户端安装软件连接服务端
[root@centos6 ~]# yum install tftp

连接服务端ip
[root@centos6 ~]# tftp 192.168.52.151
tftp> help
connect：连接到远程tftp服务器
mode：文件传输模式
put：上传文件
get：下载文件
quit：退出
verbose：显示详细的处理信息
tarce：显示包路径
status：显示当前状态信息
binary：二进制传输模式
ascii：ascii传送模式
rexmt：设置包传输的超时时间
timeout：设置重传的超时时间
help：帮助信息
?：帮助信息

从服务端下载文件
从客户端将文件放在服务的工作目录中
[root@centos7 tftpboot]# pwd
/var/lib/tftpboot
[root@centos7 tftpboot]# ls
a.txt

客户端下载文件默认下载到当前工作目录中
[root@centos6 ~]# tftp 192.168.52.151
tftp> get a.txt
[root@centos6 ~]# ls
a.txt   
```
## PXE介绍
`PXE：`    
&ensp;&ensp;Preboot Excution Environment 预启动执行环境    &ensp;&ensp;Intel公司研发    
&ensp;&ensp;基于Client/Server的网络模式，支持远程主机通过网络从远端服务器下载 映像，并由此支持通过网络启动操作系统  &ensp;&ensp;PXE可以引导和安装Windows,linux等多种操作系统 

&ensp;&ensp;XE(preboot execute environment，预启动执行环境)是由Intel公司开发的最新技术，工作于Client/Server的网络模式，支持工作站通过网络从远端服务器下载映像，并由此支持通过网络启动操作系统，在启动过程中，终端要求服务器分配IP地址，再用TFTP（trivial file transfer protocol）或MTFTP(multicast trivial file transfer protocol)协议下载一个启动软件包到本机内存中执行，由这个启动软件包完成终端（客户端）基本软件设置，从而引导预先安装在服务器中的终端操作系统。PXE可以引导多种操作系统.

## PXE工作原理
![PXE工作原理](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/PXE%E5%8E%9F%E7%90%86%E5%9B%BE.png)

Client向PXE Server上的DHCP发送IP地址请求消息，DHCP检测Client是否合法（主要是检 测Client的网卡MAC地址），如果合法则返回Client的IP地址，同时将启动文件pxelinux.0的 位置信息一并传送给Client  


Client向PXE Server上的TFTP发送获取pxelinux.0请求消息，TFTP接收到消息之后再向Client 发送pxelinux.0大小信息，试探Client是否满意，当TFTP收到Client发回的同意大小信息之后， 正式向Client发送pxelinux.0 

Client执行接收到的pxelinux.0文件 

Client向TFTP Server发送针对本机的配置信息文件（在TFTP 服务的pxelinux.cfg目录下）， TFTP将配置文件发回Client，继而Client根据配置文件执行后续操作。  

Client向TFTP发送Linux内核请求信息，TFTP接收到消息之后将内核文件发送给Client 

Client向TFTP发送根文件请求信息，TFTP接收到消息之后返回Linux根文件系统  

Client启动Linux内核 


Client下载安装源文件，读取自动化安装脚本 

## `PXE自动化安装CentOS 7 `

安装前准备：关闭防火墙和SELINUX，DHCP服务器静态IP 

`安装软件包 `

&ensp;&ensp;httpd tftp-server dhcp  syslinux  system-config-kickstart 

`配置文件共享服务：` 

&ensp;&ensp;systemctl enable httpd 

&ensp;&ensp;systemctl start httpd 

&ensp;&ensp;mkdir /var/www/html/centos/7 

&ensp;&ensp;mount /dev/sr0 /var/www/html/centos/7 

`准备kickstart文件 ` 

&ensp;&ensp;/var/www/html/ks/centos7.cfg  注意：权限 

`配置tftp服务 `

&ensp;&ensp;systemctl enable tftp.socket  

&ensp;&ensp;systemctl start tftp.socket  

`配置DHCP服务` 

vim /etc/dhcp/dhcpd.conf 

&ensp;&ensp;option domain-name "example.com"; 

&ensp;&ensp;default-lease-time 600; 

&ensp;&ensp;max-lease-time 7200; 

&ensp;&ensp;subnet 192.168.100.0 netmask 255.255.255.0 {    
    &ensp;&ensp; &ensp;&ensp;range 192.168.100.1 192.168.100.200;   
 &ensp;&ensp; &ensp;&ensp;filename "pxelinux.0";   
&ensp;&ensp; &ensp;&ensp;next-server 192.168.100.100; 
 
&ensp;&ensp;} 
 
 &ensp;&ensp;systemctl enable dhcpd 

 &ensp;&ensp;systemctl  start dhcpd 

`准备相关文件` 

 &ensp;&ensp;mkdir /var/lib/tftpboot/pxelinux.cfg/   

 &ensp;&ensp;cp  /usr/share/syslinux/{pxelinux.0,menu.c32} /var/lib/tftpboot/     

 &ensp;&ensp;cp  /misc/cd/isolinux/{vmlinuz,initrd.img} /var/lib/tftpboot/ 

 &ensp;&ensp;cp  /misc/cd/isolinux/isolinux.cfg   /var/lib/tftpboot/pxelinux.cfg/default 

 &ensp;&ensp;文件列表如下： 

 &ensp;&ensp;/var/lib/tftpboot/

 &ensp;&ensp; &ensp;&ensp; ├── initrd.img
 
  &ensp;&ensp; &ensp;&ensp; ├── menu.c32 
  
  &ensp;&ensp; &ensp;&ensp; ├── pxelinux.0
  
 &ensp;&ensp; &ensp;&ensp;├── pxelinux.cfg
   
 &ensp;&ensp; &ensp;&ensp;│   └── default

 &ensp;&ensp; &ensp;&ensp;└── vmlinuz 

`准备启动菜单` 

Vim /var/lib/tftpboot/pxelinux.cfg/default 

&ensp;&ensp;default menu.c32 

&ensp;&ensp;timeout 600 

&ensp;&ensp;menu title PXE INSTALL MENU 

&ensp;&ensp;label auto   

&ensp;&ensp;&ensp;&ensp;menu label Auto Install CentOS 7    

&ensp;&ensp;&ensp;&ensp;kernel vmlinuz   

&ensp;&ensp;&ensp;&ensp;append initrd=initrd.img ks=http://192.168.100.100/ks/centos7.cfg  

&ensp;&ensp;label manual   

&ensp;&ensp;&ensp;&ensp;menu label Manual Install CentOS 7    

&ensp;&ensp;&ensp;&ensp;kernel vmlinuz   

&ensp;&ensp;&ensp;&ensp;append initrd=initrd.img inst.repo=http://192.168.100.100/centos/7 

&ensp;&ensp;label local        

&ensp;&ensp;&ensp;&ensp;menu default      

&ensp;&ensp;&ensp;&ensp;menu label ^Boot from local drive    

&ensp;&ensp;&ensp;&ensp;localboot 0xffff 

`范例：PXE自动安装安装centos7`
```bash
在服务端安装dhcp服务和tftp服务、http服务、syslinux服务

第一步：在服务端安装服务
[root@centos7 ~]# yum install dhcp httpd tftp

第二步：配置服务端的http服务充当实现基于网络的yum源
[root@centos7 html]# tree /var/www/html/
/var/www/html/
└── centos
    ├── 6
    │   └── os
    │       └── x86_64
    └── 7
        └── os
            └── x86_64
[root@centos7 html]# mount /dev/sr0 centos/7/os/x86_64/
mount: /dev/sr0 is write-protected, mounting read-only

第二步：在服务端准备应答文件也是基于网络 
[root@centos7 ks]# pwd
/var/www/html/ks
[root@centos7 ks]# tree
└── ks.cfg
[root@centos7 ks]# cat ks.cfg 
#version=DEVEL
# System authorization information
auth --enableshadow --passalgo=sha512
# 基于网络的yum源地址，这里指的是自身服务端
url --url=http://192.168.52.151/centos/7/os/x86_64/
# 纯文本安装
text
# Run the Setup Agent on first boot
firstboot --enable
ignoredisk --only-use=sda
# Keyboard layouts
keyboard --vckeymap=us --xlayouts='us'
# System language
lang en_US.UTF-8

# Network information
network  --bootproto=dhcp --device=ens33 --ipv6=auto --activate
network  --hostname=centos7.com

# Root password
rootpw --iscrypted $6$ToAVuC4c08xK1qtq$f6gDK7AeuLhbBoRkcNdhMmFmznMwDIdse0.EHCm6Pj1I1RRLAibpqYo7/D/EoRdMqsw3.fDc8dnVird267xgf/
# 关闭防火墙和selinux
firewall --disabled
selinux --disabled
services --disabled="chronyd"
# System timezone时区
timezone Asia/Shanghai --isUtc --nontp
#系统初始创建的普通用户
user --name=daizhe --password=$6$FovXU2UHXFQFBUty$8WHbDLyMK58OUQRhtJGuAOTdyd6XHka4GaD6MzsZa8UhGxjmSAyJoJePLMVdB2Pbodtht1w8xovUJbhxbl82W. --iscrypted --gecos="daizhe"
# X Window System configuration information
bootloader --append=" crashkernel=auto" --location=mbr --boot-drive=sda
# 初始化分区、清空mbr、安装完自动重启
clearpart --all --initlabel
zerombr
reboot
# 设置分区策略
part /boot --fstype="xfs" --ondisk=sda --size=2048
part swap --fstype="swap" --ondisk=sda --size=4096
part / --fstype="xfs" --ondisk=sda --size=51200
part /data --fstype="xfs" --ondisk=sda --size=30720
%packages
@core
%end
#仅安装一个核心包

[root@centos7 ks]# chmod o+r *
-rw----r--. 1 root root 1522 Nov 19 21:47 ks.cfg

第三步：配置服务端的DHCP服务
[root@centos7 ~]# vim /etc/dhcp/dhcpd.conf 

subnet 192.168.52.0 netmask 255.255.255.0 {     （分配的网段）
        range 192.168.52.50 192.168.52.90;        （ip范围）
        option routers 192.168.52.1;               （网关）
        option domain-name-servers 8.8.8.8,1.1.1.1;  （dns）
        next-server 192.168.52.151;（tftp服务器的地址这里指服务器本机）
        filename "pxelinux.0"（安装计算机的引导文件）
}
[root@centos7 ~]# systemctl restart dhcpd
查看dhcp服务端端口udp 67

第四步：TFTP服务器配置（在服务端准备安装主机的引导文件 ，本机默认是没有这个文件的需要安装一个syslinux程序）
[root@centos7 ~]# yum install syslinux
[root@centos7 ~]# rpm -ql syslinux
/usr/share/syslinux/pxelinux.0
/usr/share/syslinux/menu.c32  （计算机启动时字符界面的蓝色背景）


将计算机的启动菜单文件放在tftp服务器上特定的目录中
[root@centos7 ~]# mkdir /var/lib/tftpboot/pxelinux.cfg/
[root@centos7 ~]# cp /usr/share/syslinux/{menu.c32,pxelinux.0} /var/lib/tftpboot/
[root@centos7 ~]# cd !$
cd /var/lib/tftpboot/
[root@centos7 tftpboot]# tree
├── menu.c32
├── pxelinux.0
└── pxelinux.cfg

将服务端光盘中的启动内核文件以及对应的initrd文件也放在TFTP服务器上
[root@centos7 ~]# cp /misc/cd/isolinux/{vmlinuz,initrd.img} /var/lib/tftpboot/  内核文件
[root@centos7 tftpboot]# cp /misc/cd/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default  复制启动菜单并改名
[root@centos7 tftpboot]# tree
├── initrd.img
├── menu.c32
├── pxelinux.0
├── pxelinux.cfg
│?? └── default
└── vmlinuz
1 directory, 5 files

第五步：修改启动菜单文件
[root@centos7 tftpboot]# cat pxelinux.cfg/default 

#系统自带的图形化菜单
default menu.c32
#启动标题
menu title RXE Install Centos
#自定义的安装菜单
label linux
  menu label ^Auto Install Mini CentOS 7
  kernel vmlinuz
#指明应答文件
  append initrd=initrd.img ks=http://192.168.52.151/ks/ks.cfg

#默认本地启动
label local
  menu default
  menu label Boot from ^local drive
  localboot 0xffff

第六步：创建一个新的虚拟机使网卡与服务端在同一网络类型
开启测试
```
![](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/DHCP.png)
![](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E6%A1%8C%E9%9D%A2.png)

## `PXE自动化安装CentOS 6 `

安装前准备：关闭防火墙和SELINUX，DHCP服务器静态IP 

`1 安装相应软件包 `

&ensp;&ensp;yum install dhcp httpd  tftp-server  syslinux-nolinux

&ensp;&ensp;chkconfig tftp on 

&ensp;&ensp;chkconfig xinetd on 

&ensp;&ensp;chkconfig httpd on 

&ensp;&ensp;chkconfig dhcpd on 

&ensp;&ensp;service httpd start 

&ensp;&ensp;service xneted start

`2 准备Yum 源和相关目录`  

&ensp;&ensp;mkdir -pv /var/www/html/centos/{6,ks}  

&ensp;&ensp;mount /dev/sr0 /var/www/html/centos/6 

`3 准备kickstart文件 `

&ensp;&ensp;/var/www/html/centos/ks/centos6.cfg  

&ensp;&ensp;注意权限：  

&ensp;&ensp;chmod  644 /var/www/html/centos/ks/centos6.cfg 

`4 准备相关的启动文件` 

&ensp;&ensp;mkdir /var/lib/tftpboot/pxelinux.cfg/ 

&ensp;&ensp;cp  /usr/share/syslinux/pxelinux.0   /var/lib/tftpboot/ 


&ensp;&ensp;Cd /misc/cd/isolinux/ 

&ensp;&ensp;cp boot.msg,vesamenu.c32, splash.jpg ,vmlinuz ,initrd.img/var/lib/tftpboot 

`5 准备启动菜单文件 `

&ensp;&ensp;cp /misc/cd/isolinux/isolinux.cfg /var/lib/tftpboot/pxelinux.cfg/default 
 
&ensp;&ensp;vim /var/lib/tftpboot/pxelinux.cfg/default   

&ensp;&ensp;default vesamenu.c32 指定菜单风格 

&ensp;&ensp;#prompt 1 

&ensp;&ensp;timeout 600  

&ensp;&ensp;display boot.msg  

&ensp;&ensp;menu background splash.jpg 

&ensp;&ensp;menu title Welcome to wang CentOS 6 

&ensp;&ensp;menu color border 0 #ffffffff #00000000

&ensp;&ensp;menu color sel 7 #ffffffff #ff000000 

&ensp;&ensp;menu color title 0 #ffffffff #00000000 

&ensp;&ensp;menu color tabmsg 0 #ffffffff #00000000 

&ensp;&ensp;menu color unsel 0 #ffffffff #00000000 

&ensp;&ensp;menu color hotsel 0 #ff000000 #ffffffff 

&ensp;&ensp;menu color hotkey 7 #ffffffff #ff000000 

&ensp;&ensp;menu color scrollbar 0 #ffffffff #00000000  

&ensp;&ensp;label auto   

&ensp;&ensp;&ensp;&ensp;menu label ^Automatic Install Centos6    

&ensp;&ensp;&ensp;&ensp;kernel vmlinuz   

&ensp;&ensp;&ensp;&ensp;append initrd=initrd.img ks=http://192.168.100.100/centos/ks/centos6.cfg 

&ensp;&ensp;label manual   

&ensp;&ensp;&ensp;&ensp;menu label ^Manual Install Centos   

&ensp;&ensp;&ensp;&ensp;kernel vmlinuz   

&ensp;&ensp;&ensp;&ensp;append initrd=initrd.img inst.repo=http://192.168.100.100/centos/6 

&ensp;&ensp;label local   

&ensp;&ensp;&ensp;&ensp;menu default    

&ensp;&ensp;&ensp;&ensp;menu label Boot from ^local drive   localboot 0xffff 

`tftp目录结构如下： `

&ensp;&ensp;tree /var/lib/tftpboot/ 

&ensp;&ensp;/var/lib/tftpboot/ 

&ensp;&ensp;&ensp;&ensp;├── boot.msg

&ensp;&ensp;&ensp;&ensp;├── initrd.img 

&ensp;&ensp;&ensp;&ensp;├── pxelinux.0 

&ensp;&ensp;&ensp;&ensp;├── pxelinux.cfg 

&ensp;&ensp;&ensp;&ensp;│   └── default 

&ensp;&ensp;&ensp;&ensp;├── splash.jpg 

&ensp;&ensp;&ensp;&ensp;├── vesamenu.c32 

&ensp;&ensp;&ensp;&ensp;└── vmlinuz   

&ensp;&ensp;1 directory, 7 files 

`6 配置dhcp服务` 

&ensp;&ensp;cp /usr/share/doc/dhcp-4.1.1/dhcpd.conf.sample /etc/dhcp/dhcpd.conf 

&ensp;&ensp;vim /etc/dhcp/dhcpd.conf 

&ensp;&ensp;&ensp;&ensp;option domain-name "magedu.com"; 

&ensp;&ensp;&ensp;&ensp;option domain-name-servers 192.168.100.1; 

&ensp;&ensp;&ensp;&ensp;subnet 192.168.100.0 netmask 255.255.255.0 {         
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;range 192.168.100.1 192.168.100.200;         

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;option routers 192.168.100.1;         

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;filename "pxelinux.0";         

&ensp;&ensp;&ensp;&ensp;next-server 192.168.100.100; 

&ensp;&ensp;&ensp;&ensp;} 

&ensp;&ensp;&ensp;&ensp;service dhcpd start 

注意事项:

在服务端安装dhcp服务和tftp服务、http服务、syslinux服务(centos6与centos7不同，)
将虚拟机网卡设置为仅主机模式，避免配置的程序与网络中现有的服务冲突，将服务端的网卡设置为静态手动ip

配置服务端的DHCP服务
注意：如果在做此实验之前已经做了上面安装centos7的实验时应该将centos7的dhcp程序关闭，避免冲突。

## 在一个服务端主机上创建两种系统的安装
注意事项:
![](https://github.com/Dz6666/Dz6666.github.ii/blob/master/css/7%E4%B8%8A%E6%98%AF%E5%AE%9E%E7%8E%B0%E4%B8%A4%E4%B8%AA%E7%B3%BB%E7%BB%9F%E8%87%AA%E5%8F%91%E5%8A%A8%E5%AE%89%E8%A3%85.png?raw=true)
![](https://github.com/Dz6666/Dz6666.github.ii/blob/master/css/default%E5%90%AF%E5%8A%A8%E6%96%87%E4%BB%B6%E9%85%8D%E7%BD%AE.png?raw=true)

实验：在centos7实现基于PXE安装centos6,7

mount /dev/sr1    /var/www/html/centos/6/os/x86_64

[root@centos7 tftpboot]#ls /var/www/html/ksdir/
ks6-mini.cfg  ks7-mini.cfg

mkdir /var/lib/tftpboot/linux{6,7} 

cp /var/www/html/centos/6/os/x86_64/isolinux/{vmlinuz,initrd.img} linux6/
cp /var/www/html/centos/7/os/x86_64/isolinux/{vmlinuz,initrd.img} linux7/
 
[root@centos7 tftpboot]#tree /var/lib/tftpboot/
.
├── linux6
│?? ├── initrd.img
│?? └── vmlinuz
├── linux7
│?? ├── initrd.img
│?? └── vmlinuz
├── menu.c32
├── pxelinux.0
└── pxelinux.cfg
    └── default
	
[root@centos7 tftpboot]#cat /var/lib/tftpboot/pxelinux.cfg/default 
default menu.c32
timeout 100
menu title PXE Install CentOS

label mini7
  menu label ^Auto Install Mini CentOS 7
  kernel linux7/vmlinuz
  append initrd=linux7/initrd.img ks=http://192.168.34.7/ksdir/ks7-mini.cfg


label mini6
  menu label ^Auto Install Mini CentOS 6
  kernel linux6/vmlinuz
  append initrd=linux6/initrd.img ks=http://192.168.34.7/ksdir/ks6-mini.cfg

label local
  menu default
  menu label Boot from ^local drive
  localboot 0xffff

以上实验是基于BIOS安装，不支持UEFI

bios mbr最多持续2T的分区

uefi gpt更大的硬盘分区

