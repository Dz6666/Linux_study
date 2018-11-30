---
title: centos6启动流程
tags: linux
categories: linux
---
## Linux组成 

Linux: kernel(内核)+rootfs（根文件系统）  

&ensp;&ensp;kernel: 进程管理、内存管理、网络管理、驱动程序、文件系统、安全功能  

&ensp;&ensp;rootfs:程序和glibc  

&ensp;&ensp;库：函数集合, function, 调用接口（头文件负责描述） ldd 命令 库是被二进制程序所依赖的   

&ensp;&ensp;&ensp;&ensp;过程调用：procedure，无返回值   

&ensp;&ensp;&ensp;&ensp;函数调用：function  

&ensp;&ensp;程序：二进制执行文件 

内核设计流派：  

&ensp;&ensp;单内核(monolithic kernel)：Linux   把所有功能集成于同一个程序  （核心文件放置在/boot目录下）

微内核(micro kernel)：Windows, Solaris   每种功能使用一个单独子系统实现 

## 内核 

Linux内核特点：  

&ensp;&ensp;支持模块化：.ko（内核对象） 
&ensp;&ensp;&ensp;&ensp;如：文件系统，硬件驱动，网络协议等  

&ensp;&ensp;支持内核模块的动态装载和卸载


组成部分：      

&ensp;&ensp;`核心文件：`/boot/vmlinuz-VERSION-release 

&ensp;&ensp;&ensp;&ensp;`ramdisk：辅助的伪根系统 `

(centos6 &ensp;&ensp;initramfs-2.6.32-696.el6.x86_64.img )  

(centos7&ensp;&ensp;initramfs-3.10.0-862.el7.x86_64kdump.img) 

（centos5 &ensp;&ensp;initrd-26.18-164.e15.img）   

&ensp;&ensp;&ensp;&ensp;CentOS 5: /boot/initrd-VERSION-release.img         

&ensp;&ensp;&ensp;&ensp;CentOS 6,7: /boot/initramfs-VERSION-release.img      

&ensp;&ensp;&ensp;&ensp;`模块文件：`/lib/modules/VERSION-release 可以动态加载和卸载
```bash
模块
[root@centos6 2.6.32-696.el6.x86_64]# pwd
/lib/modules/2.6.32-696.el6.x86_64
[root@centos6 2.6.32-696.el6.x86_64]# ls
build              modules.drm          modules.seriomap
extra              modules.ieee1394map  modules.softdep
kernel             modules.inputmap     modules.symbols
modules.alias      modules.isapnpmap    modules.symbols.bin
modules.alias.bin  modules.modesetting  modules.usbmap
modules.block      modules.networking   source
modules.ccwmap     modules.ofmap        updates
modules.dep        modules.order        vdso
modules.dep.bin    modules.pcimap       weak-updates
```
## centos6 启动流程

![流程图](https://img-blog.csdn.net/20140924140356612?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvempmMjgwNDQxNTg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

&ensp;&ensp;1.加载BIOS的硬件信息，获取第一个启动设备 

&ensp;&ensp;2.读取第一个启动设备MBR的引导加载程序(grub)的启动信息 

&ensp;&ensp;3.加载核心操作系统的核心信息，核心开始解压缩，并尝试驱动所有的硬件设备 

&ensp;&ensp;4.核心执行init程序，并获取默认的运行信息 

&ensp;&ensp;5.init程序执行/etc/rc.d/rc.sysinit文件 

&ensp;&ensp;6.启动核心的外挂模块 

&ensp;&ensp;7.init执行运行的各个批处理文件(scripts) 

&ensp;&ensp;8.init执行/etc/rc.d/rc.local 

&ensp;&ensp;9.执行/bin/login程序，等待用户登录 

&ensp;&ensp;10.登录之后开始以Shell控制主机 

## 启动流程 

&ensp;&ensp;`POST：`Power-On-Self-Test，加电自检，是BIOS功能的一个主要部分。负责完成对 CPU、主板、内存、硬盘子系统、显示子系统、串并行接口、键盘等硬件情况的检测  

&ensp;&ensp;ROM：`BIOS，Basic Input and Output System(基本输入输出系统)，保存着有关计算机系统最重要 的基本输入输出程序，系统信息设置、开机加电自检程序和系统启动自举程序等  

&ensp;&ensp;RAM：CMOS互补金属氧化物半导体，保存各项参数的设定  

&ensp;&ensp;按次序查找引导设备，第一个有引导程序的设备为本次启动设备

&ensp;&ensp;`bootloader: `引导加载器，引导程序  

&ensp;&ensp;&ensp;&ensp;windows: ntloader，仅是启动OS  

&ensp;&ensp;&ensp;&ensp;Linux：功能丰富，提供菜单，允许用户选择要启动系统或不同的内核版本；把用 户选定的内核装载到内存中的特定空间中，解压、展开，并把系统控制权移交给内核   

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;LILO：LInux LOader （旧的）

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;GRUB: GRand Unified Bootloader    

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`GRUB 0.97: GRUB Legacy， GRUB2 `版本

```bash
centos 6
[root@centos6 boot]# rpm -qi grub
Name        : grub
Version     : 0.97 

centos7 
[root@centos7 ~]# rpm -qi grub2
Name        : grub2
Epoch       : 1
Version     : 2.02
[root@centos7 ~]# rpm -qi grub2
Name        : grub2
Epoch       : 1
Version     : 2.02

```
`MBR:第一个扇区`

&ensp;&ensp;前446字节: bootloader

&ensp;&ensp;中间64: 分区表

&ensp;&ensp;最后2字节: 55AA 标记位

`GRUB:  `  

&ensp;&ensp;primary boot loader : 1st stage，1.5 stage 磁盘扇区的二进制数据


&ensp;&ensp;secondary boot loader ：2nd stage，分区文件  /boot/grub/文件

`kernel：  `   

自身初始化：  

&ensp;&ensp;探测可识别到的所有硬件设备  

&ensp;&ensp;加载硬件驱动程序（借助于ramdisk加载驱动）  

&ensp;&ensp;以只读方式挂载根文件系统 (刚开始挂载是只读，启动慢慢的为读写) 怎么挂载根，/boot/grub/grub.conf写着

&ensp;&ensp;运行用户空间的第一个应用程序：/sbin/init （系统中的第一个进程centos7 为systemd）

范例：

`如果删除/boot下的伪文件系统怎么手动生成`
```bash
[root@centos6 boot]# rm -rf initramfs-2.6.32-696.el6.x86_64.img 
[root@centos6 boot]# mkinitrd /boot/initramfs-`uname -r`.img `uname -r`


如果误删了伪文件系统并且重启了，现象表现为开机起不来黑色屏幕，检测不到文件系统

快速ESC进入紧急救援模式
切换根
sh-4]#chroot /mnt/sysimage
sh-4]# mkinitrd /boot/initramfs-`uname -r`.img `uname -r`
sh-4]#sync同步
```
## `ramdisk管理 `
ramdisk文件的制作：    

(1) mkinitrd命令     

&ensp;&ensp;为当前正在使用的内核重新制作ramdisk文件  

`mkinitrd /boot/initramfs-$(uname -r).img $(uname -r) `  

(2) dracut命令 

为当前正在使用的内核重新制作ramdisk文件     `dracut /boot/initramfs-$(uname -r).img $(uname -r) `


