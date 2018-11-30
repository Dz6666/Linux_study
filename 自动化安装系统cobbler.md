---
title: 自动化安装系统cobbler
tags: linux服务
categories: linux服务
---
![自动化发展历史和技术应用](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E8%87%AA%E5%8A%A8%E5%8C%96%E5%8F%91%E5%B1%95%E5%8E%86%E5%8F%B2.png)

## 安装程序 

`CentOS系统安装 `    

&ensp;&ensp;系统启动流程：      

&ensp;&ensp;bootloader-->kernel(initramfs)-->rootfs-->/sbin/init 

`anaconda: 系统安装程序  `

&ensp;&ensp;gui：图形窗口  

&ensp;&ensp;tui: 基于图形库curses的文本窗口 

## 安装程序启动过程

MBR：isolinux/boot.cat 二进制文件55 aa

stage2: isolinux/isolinux.bin 

配置文件：isolinux/isolinux.cfg 

&ensp;&ensp;每个对应的菜单选项：   

&ensp;&ensp;&ensp;&ensp;加载内核：isolinuz/vmlinuz   

&ensp;&ensp;&ensp;&ensp;向内核传递参数：append initrd=initrd.img ... 

装载根文件系统，并启动anaconda  

&ensp;&ensp;默认启动GUI接口  

&ensp;&ensp;若是显式指定使用TUI接口：向内核传递text参数即可  

&ensp;&ensp;(1)按tab键,在后面增加text  

&ensp;&ensp;(2)按ESC键：boot: linux text

## anaconda工作过程 

Anaconda安装系统分成三个阶段： 

安装前配置阶段  

&ensp;&ensp;安装过程使用的语言  

&ensp;&ensp;键盘类型  

&ensp;&ensp;安装目标存储设备   

&ensp;&ensp;&ensp;&ensp;Basic Storage：本地磁盘   

&ensp;&ensp;&ensp;&ensp;特殊设备：iSCSI  

&ensp;&ensp;设定主机名  

&ensp;&ensp;配置网络接口  

&ensp;&ensp;时区  

&ensp;&ensp;管理员密码  

&ensp;&ensp;设定分区方式及MBR的安装位置 
  
  
&ensp;&ensp;创建一个普通用户   

&ensp;&ensp;选定要安装的程序包 
 
安装阶段  

&ensp;&ensp;在目标磁盘创建分区，执行格式化操作等  

&ensp;&ensp;将选定的程序包安装至目标位置  

&ensp;&ensp;安装bootloader和initramfs 

图形模式首次启动  

&ensp;&ensp;iptables  

&ensp;&ensp;selinux  

&ensp;&ensp;core dump

## 系统安装 

启动安装过程一般应位于引导设备；后续的anaconda及其安装用到的程序包等 可来自下面几种方式:  

&ensp;&ensp;本地光盘  

&ensp;&ensp;本地硬盘  

&ensp;&ensp;NFS   

&ensp;&ensp;URL:              

&ensp;&ensp;&ensp;&ensp;ftp server: yum repository        

&ensp;&ensp;&ensp;&ensp;http server: yum repostory 
 
如果想手动指定安装源：  

&ensp;&ensp;boot: linux askmethod 

![centos6开机界面](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/centos6%E5%BC%80%E6%9C%BA%E7%95%8C%E9%9D%A2.png)

第一项：基本安装

第二项：已基本的显卡驱动安装

第三项：救援环境

第四项：本机硬盘安装

第五项：内存检查

最底下TAB：编辑选项（安装向导软件，调用内核anaconda 存放在root的家目录anaconda-ks.cfg）

![安装界面按tab键](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/centos%E5%BC%80%E6%9C%BA%E5%AE%89%E8%A3%85%E7%95%8C%E9%9D%A2%E6%8C%89%E4%B8%8Btab%E9%94%AE.png)

可以在后面输入rescue就相当于进入了人救援模式，也等于菜单选中救援模式，按tab键出来的内容

基本显卡驱动安装按tab键：nomodeset

在系统安装界面，按esc
![安装界面](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/centos6%E5%BC%80%E6%9C%BA%E7%95%8C%E9%9D%A2.png)
![安装界面按esc](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/centos6%E5%AE%89%E8%A3%85%E7%95%8C%E9%9D%A2%E6%8C%89esc.png)
![可以敲的指令](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E5%90%AF%E5%8A%A8%E7%95%8C%E9%9D%A2esc%E5%8F%AF%E8%BE%93%E5%85%A5%E7%9A%84%E6%8C%87%E4%BB%A4.png)linux test 为纯文本安装字符界面  

在此界面下可以输入命令安装对应的linux,输入的命令对应的文件
```bash
[root@centos6 isolinux]# pwd
/misc/cd/isolinux        光盘
[root@centos6 isolinux]# cat isolinux.cfg 
[root@centos6 isolinux]# pwd
/misc/cd/isolinux
[root@centos6 isolinux]# cat isolinux.cfg 
default vesamenu.c32 安装界面背景风格
#prompt 1
timeout 600 安装系统界面倒计时十分之一秒为单位
display boot.msg
menu background splash.jpg
menu title Welcome to CentOS 6.9!  欢迎菜单语句
menu color border 0 #ffffffff #00000000
menu color sel 7 #ffffffff #ff000000
menu color title 0 #ffffffff #00000000
menu color tabmsg 0 #ffffffff #00000000
menu color unsel 0 #ffffffff #00000000
menu color hotsel 0 #ff000000 #ffffffff
menu color hotkey 7 #ffffffff #ff000000
menu color scrollbar 0 #ffffffff #00000000

label linux正常标准安装
  menu label ^Install or upgrade an existing system
  menu default默认安装
  kernel vmlinuz
  append initrd=initrd.img
label vesa基本显卡安装
  menu label Install system with ^basic video driver
  kernel vmlinuz
  append initrd=initrd.img nomodeset
label rescue救援模式
  menu label ^Rescue installed system
  kernel vmlinuz
  append initrd=initrd.img rescue
label local本机硬盘安装
  menu label Boot from ^local drive
  localboot 0xffff
label memtest86内存检查
  menu label ^Memory test
  kernel memtest
  append -
```
`范例：安装源可以是网络`
```bash
事先可以将安装光盘共享到网络中
第一步：使用另一台centos6 模拟共享光盘
[root@centos6 ~]# cd /var/www/html
[root@centos6 html]# tree
.
└── centos
    ├── 6
    │   └── os
    │       └── x86_64
    └── 7
        └── os
            └── x86_64
[root@centos6 html]# mount /dev/sr0 /centos/6/os/x86_64/
mount: block device /dev/sr0 is write-protected, mounting read-only
```
![基于网络安装询问安装方法](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E5%9F%BA%E4%BA%8E%E7%BD%91%E7%BB%9C%E5%AE%89%E8%A3%85%E8%AF%A2%E9%97%AE%E5%AE%89%E8%A3%85%E6%96%B9%E6%B3%95.png)  
也可以事先指定ip
![事先设置ip地址](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E7%BD%91%E7%BB%9C%E5%9C%B0%E5%9D%80.png)
![网络源安装第一步](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E7%BD%91%E7%BB%9C%E5%AE%89%E8%A3%85%E7%AC%AC%E4%B8%80%E6%AD%A5.png)
![网络安装提示第二步](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E7%BD%91%E7%BB%9C%E5%AE%89%E8%A3%85%E6%8F%90%E7%A4%BA%E7%AC%AC%E4%BA%8C%E6%AD%A5.png)
![网络仓库路径](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E7%BD%91%E7%BB%9C%E4%BB%93%E5%BA%93%E8%B7%AF%E5%BE%84.png)
![基于网络安装](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E5%9F%BA%E4%BA%8E%E7%BD%91%E7%BB%9C%E5%AE%89%E8%A3%85%E6%88%90%E5%8A%9F.png)

## 指定安装源 

centos6 

DVD drive   repo=cdrom :device 

Hard Drive   repo=hd:device/path 

HTTP Server   repo=http://host/path 

HTTPS Server  repo=https://host/path 

FTP Server   repo=ftp://username:password@ host/path 

NFS Server   repo=nfs:server:/path ISO images on an 

NFS Server repo=nfsiso:server:/path 

centos7 

Any CD/DVD drive    inst.repo=cdrom 

Hard Drive  inst.repo=hd:device:/path 

HTTP Server   inst.repo=http://host/path 

HTTPS Server  inst.repo=https://host/path 

FTP Server   inst.repo=ftp://username:password@ host/path 

NFS Server   inst.repo=nfs:[options:]server:/path 

![指定网络源](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/%E7%BD%91%E7%BB%9C%E4%BB%93%E5%BA%93%E5%AE%89%E8%A3%85.png)


## 系统安装 

anaconda的配置方式：  

&ensp;&ensp;(1) 交互式配置方式 

&ensp;&ensp;(2) 通过读取事先给定的配置文件自动完成配置   

&ensp;&ensp;&ensp;&ensp;按特定语法给出的配置选项    

&ensp;&ensp;&ensp;&ensp;kickstart文件 

安装boot引导选项：boot:    

&ensp;&ensp;text: 文本安装方式    

&ensp;&ensp;askmethod: 手动指定使用的安装方法 

与网络相关的引导选项：   

&ensp;&ensp;ip=IPADDR  

&ensp;&ensp;netmask=MASK  

&ensp;&ensp;gateway=GW  

&ensp;&ensp;dns=DNS_SERVER_IP  

&ensp;&ensp;ifname=NAME:MAC_ADDR 

与远程访问功能相关的引导选项：  

&ensp;&ensp;vnc  

&ensp;&ensp;vncpassword='PASSWORD' 

指明kickstart文件的位置：  ks=  

&ensp;&ensp;DVD drive: ks=cdrom:/PATH/TO/KICKSTART_FILE  

&ensp;&ensp;Hard drive: ks=hd:device:/directory/KICKSTART_FILE  

&ensp;&ensp;HTTP server: ks=http://host:port/path/to/KICKSTART_FILE  

&ensp;&ensp;FTP server: ks=ftp://host:port/path/to/KICKSTART_FILE  

&ensp;&ensp;HTTPS server: ks=https://host:port/path/to/KICKSTART_FILE  

&ensp;&ensp;NFS server:ks=nfs:host:/path/to/KICKSTART_FILE 

启动紧急救援模式：  rescue 

官方文档：《Installation Guide》 

## kickstart文件的格式 

命令段：指明各种安装前配置，如键盘类型等 

程序包段：指明要安装的程序包组或程序包，不安装的程序包等  

&ensp;&ensp;%packages  

&ensp;&ensp;@group_name  

&ensp;&ensp;package  

&ensp;&ensp;-package 

&ensp;&ensp;%end 

脚本段：  

&ensp;&ensp;%pre: 安装前脚本   

&ensp;&ensp;&ensp;&ensp;运行环境：运行于安装介质上的微型Linux环境  

&ensp;&ensp;%post: 安装后脚本   运

&ensp;&ensp;&ensp;&ensp;行环境：安装完成的系统 

命令段中的命令： 

必备命令  

&ensp;&ensp;authconfig: 认证方式配置   

&ensp;&ensp;&ensp;&ensp;authconfig --useshadow  --passalgo=sha512  

&ensp;&ensp;bootloader：bootloader的安装位置及相关配置   

&ensp;&ensp;&ensp;&ensp;bootloader --location=mbr --driveorder=sda –     

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;append="crashkernel=auto  rhgb quiet"  

&ensp;&ensp;keyboard: 设定键盘类型  

&ensp;&ensp;lang: 语言类型  

&ensp;&ensp;part: 创建分区  

&ensp;&ensp;rootpw: 指明root的密码  

&ensp;&ensp;timezone: 时区 

可选命令  

&ensp;&ensp;install OR upgrade  

&ensp;&ensp;text: 文本安装界面  

&ensp;&ensp;network  

&ensp;&ensp;firewall  

&ensp;&ensp;selinux  

&ensp;&ensp;halt  

&ensp;&ensp;poweroff  

&ensp;&ensp;reboot  

&ensp;&ensp;repo  

&ensp;&ensp;user：安装完成后为系统创建新用户  

&ensp;&ensp;url: 指明安装源  

&ensp;&ensp;key –skip 跳过安装号码,适用于rhel版本 

## kickstart文件创建

创建kickstart文件的方式

&ensp;&ensp;直接手动编辑  

&ensp;&ensp;依据某模板修改 

&ensp;&ensp;可使用创建工具：system-config-kickstart   

&ensp;&ensp;依据某模板修改并生成新配置   /root/anaconda-ks.cfg  

检查ks文件的语法错误：`ksvalidator  `

&ensp;&ensp;ksvalidator /PATH/TO/KICKSTART_FILE 

`范例：通过工具制作应答文件`
```bash
在服务端

图形化工具生成应答文件
[root@centos6 ~]# yum install system-config-kickstart
```
![制定应答文件](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E5%88%B6%E5%AE%9A%E5%BA%94%E7%AD%94%E6%96%87%E4%BB%B6.png)
设定好保存查看（在centos7上设置将要安装的包的时候要将本地yum源命名为[development]）
```bash
[root@centos6 ~]# cat ks.cfg 
#platform=x86, AMD64, or Intel EM64T
#version=DEVEL
# Firewall configuration
firewall --disabled
# Install OS instead of upgrade
install
# Use network installation
url --url="http://192.168.52.146/centos/6/os/x86_64"
# Root password
rootpw --plaintext centos
# System authorization information
auth  --useshadow  --passalgo=sha512
# Use text mode install
text
# System keyboard
keyboard us
# System language
lang en_US
# SELinux configuration
selinux --disabled
# Do not configure the X Window System
skipx
# Installation logging level
logging --level=info
# Reboot after installation
reboot
# System timezone
timezone  Africa/Abidjan
# Network information
network  --bootproto=static --device=eth0 --ip=192.168.52.188 --netmask=255.255.255.0 --onboot=on
# System bootloader configuration
bootloader --location=mbr
# Clear the Master Boot Record
zerombr
# Partition clearing information
clearpart --all --initlabel 
# Disk partitioning information
part / --fstype="ext4" --size=10000
part /boot --fstype="ext4" --size=1000
part /home --fstype="ext4" --size=20000
part swap --fstype="swap" --size=1000

%packages
@base

%end
````
创建新的虚拟机利用生成的应答文件实现安装
```bash
将上面服务器上面生成的应答文件放在http网站目录下
[root@centos6 ~]# mkdir /var/www/html/ks
[root@centos6 ~]# mv ks.cfg /var/www/html/ks/

第一步：安装客户端使用光盘启动按esc
```
![可以敲的指令](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E5%90%AF%E5%8A%A8%E7%95%8C%E9%9D%A2esc%E5%8F%AF%E8%BE%93%E5%85%A5%E7%9A%84%E6%8C%87%E4%BB%A4.png)
在客户端指定服务端的应答文件路径，声明本机的ip地址以及子网掩码   
boot:linux ks=http://192.168.52.146/ks/ks.cfg ip=192.168.52.188 netmask=255.255.255.0  
实现自动安装

`范例：安装图形化界面`
```bash
[root@centos7 ~]# yum groupinstall desktop
```

范例：系统默认就把自己第一次安装系统的的信息生成了一个应答文件
```bash
[root@centos7/6 ~]# ls
anaconda-ks.cfg 
```

## 系统光盘中isolinux目录列表 

solinux.bin：光盘引导程序，在mkisofs的选项中需要明确给出文件路径，这个 文件属于SYSLINUX项目   

isolinux.cfg：isolinux.bin的配置文件，当光盘启动后（即运行isolinux.bin）， 会自动去找isolinux.cfg文件 

vesamenu.c32：是光盘启动后的安装图形界面，也属于SYSLINUX项目， menu.c32版本是纯文本的菜单 

Memtest：内存检测，这是一个独立的程序 

splash.jgp：光盘启动界面的背景图 

vmlinuz是内核映像 

initrd.img是ramfs (先cpio，再gzip压缩)

## 制作引导光盘和U盘 

创建引导光盘：

mkdir –pv /app/myiso 

cp -r /misc/cd/isolinux/  /app/myiso/ 

vim  /app/myiso/isolinux/isolinux.cfg   initrd=initrd.img text ks=cdrom:/myks.cfg 

cp /root/myks.cfg /app/myiso/ 

mkisofs -R -J -T -v --no-emul-boot --boot-load-size 4 --boot-info-table -V "CentOS 6.9 x86_64 boot" -b isolinux/isolinux.bin -c isolinux/boot.cat -o /root/boot.iso  /app/myiso/    

注意：以上相对路径都是相对于光盘的根，和工作目录无关 

创建U盘启动盘 

dd if=/dev/sr0 of=/dev/sdb 

## mkisofs选项 
-o 指定映像文件的名称。 
-b 指定在制作可开机光盘时所需的开机映像文件。 

-c 制作可开机光盘时，会将开机映像文件中的 no-eltorito-catalog 全部内容 作成一个文件。 

-no-emul-boot 非模拟模式启动。 

-boot-load-size 4 设置载入部分的数量 

-boot-info-table 在启动的图像中现实信息 

-R 或 -rock    使用 Rock RidgeExtensions  

-J 或 -joliet    使用 Joliet 格式的目录与文件名称 

-v 或 -verbose    执行时显示详细的信息 

-T 或 -translation-table    建立文件名的转换表，适用于不支持 Rock Ridge Extensions 的系统上 

`范例：制作光盘引导和u盘`
```bash
第一步：创建引导光盘
cp光盘的isolinux目录包括目录本身
[root@centos6 ~]# mkdir /data/iso
[root@centos6 ~]# cp -r /misc/cd/isolinux/ /data/iso
[root@centos6 ~]# tree /data/iso/
/data/iso/
├── isolinux
│   ├── boot.cat
│   ├── boot.msg
│   ├── grub.conf
│   ├── initrd.img
│   ├── isolinux.bin
│   ├── isolinux.cfg
│   ├── memtest
│   ├── splash.jpg
│   ├── TRANS.TBL
│   ├── vesamenu.c32
│   └── vmlinuz
└── ks
    └── ks.cfg 应答文件

第二步：修改启动菜单
[root@centos6 iso]# vim isolinux/isolinux.cfg 

label linux
  menu label ^Install an system
  menu default
  kernel vmlinuz
  append initrd=initrd.img ks=cdrom:/ks/ks.cfg

label local
  menu label Boot from ^local drive
  localboot 0xffff

第三步：变成iso文件
[root@centos6 iso]# mkisofs -R -J -T -v --no-emul-boot --boot-load-size 4 --boot-info-table -V "CentOS 6.10 x86_64 boot" -b isolinux/isolinux.bin -c isolinux/boot.cat -o /root/boot.iso  /data/iso 
I: -input-charset not specified, using utf-8 (detected in locale settings)
genisoimage 1.1.9 (Linux)
Scanning /data/iso
Scanning /data/iso/ks
Scanning /data/iso/isolinux
Excluded: /data/iso/isolinux/TRANS.TBL
Excluded by match: /data/iso/isolinux/boot.cat
Writing:   Initial Padblock                        Start Block 0
Done with: Initial Padblock                        Block(s)    16
Writing:   Primary Volume Descriptor               Start Block 16
Done with: Primary Volume Descriptor               Block(s)    1
Writing:   Eltorito Volume Descriptor              Start Block 17
Size of boot image is 4 sectors -> No emulation
Done with: Eltorito Volume Descriptor              Block(s)    1
Writing:   Joliet Volume Descriptor                Start Block 18
Done with: Joliet Volume Descriptor                Block(s)    1
Writing:   End Volume Descriptor                   Start Block 19
Done with: End Volume Descriptor                   Block(s)    1
Writing:   Version block                           Start Block 20
Done with: Version block                           Block(s)    1
Writing:   Path table                              Start Block 21
Done with: Path table                              Block(s)    4
Writing:   Joliet path table                       Start Block 25
Done with: Joliet path table                       Block(s)    4
Writing:   Directory tree                          Start Block 29
Done with: Directory tree                          Block(s)    3
Writing:   Joliet directory tree                   Start Block 32
Done with: Joliet directory tree                   Block(s)    3
Writing:   Directory tree cleanup                  Start Block 35
Done with: Directory tree cleanup                  Block(s)    0
Writing:   Extension record                        Start Block 35
Done with: Extension record                        Block(s)    1
Writing:   The File(s)                             Start Block 36
 21.95% done, estimate finish Mon Nov 19 03:20:28 2018
 43.80% done, estimate finish Mon Nov 19 03:20:28 2018
 65.72% done, estimate finish Mon Nov 19 03:20:28 2018
 87.57% done, estimate finish Mon Nov 19 03:20:28 2018
Total translation table size: 4915
Total rockridge attributes bytes: 1727
Total directory bytes: 4096
Path table size(bytes): 36
Done with: The File(s)                             Block(s)    22660
Writing:   Ending Padblock                         Start Block 22696
Done with: Ending Padblock                         Block(s)    150
Max brk space used 1a000
22846 extents written (44 MB)

[root@centos6 iso]# ll /root
-rw-r--r--  1 root root 46788608 Nov 19 03:20 boot.iso

此时可以生成的iso文件传到weindows 创建新的虚拟机，完成开机使命，其他安装还要基于服务端共享的httpd服务
```
![半自动化安装](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E8%87%AA%E5%BB%BA.png)

```bash
可以在linux上将上面的iso文件刻录至u盘中

添加一块硬盘模拟u盘刻录
[root@centos6 ~]# lsblk
sdb      8:16   0   10G  0 disk 

[root@centos6 ~]# isohybrid boot.iso 
[root@centos6 ~]# dd if=/root/boot.iso of=/dev/sdb
91384+0 records in
91384+0 records out
46788608 bytes (47 MB) copied, 1.27735 s, 36.6 MB/s
[root@centos6 ~]# sync
```






