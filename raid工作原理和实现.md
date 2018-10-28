---
title: raid工作原理和实现
tags: linux
categories: linux
---
## 什么是RAID

RAID:Redundant Arrays of Inexpensive（Independent） Disks 

1988年由加利福尼亚大学伯克利分校（University of CaliforniaBerkeley） “A Case for Redundant Arrays of Inexpensive Disks” 

多个磁盘合成一个“阵列”来提供更好的性能、冗余，或者两者都提供

## RAID

提高IO能力 

&ensp;&ensp;磁盘并行读写 

提高耐用性 

&ensp;&ensp;磁盘冗余来实现 

级别：多块磁盘组织在一起的工作方式有所不同 

RAID实现的方式 

&ensp;&ensp;外接式磁盘阵列：通过扩展卡提供适配能力 

&ensp;&ensp;内接式RAID：主板集成RAID控制器，安装OS前在BIOS里配置 

&ensp;&ensp;软件RAID：通过OS实现

## RAID级别

&ensp;&ensp;RAID-0：条带卷，strip

&ensp;&ensp;RAID-1：镜像卷，mirror

&ensp;&ensp;RAID-2 .. 

&ensp;&ensp;RAID-5

&ensp;&ensp;RAID-6

&ensp;&ensp;RAID-10

&ensp;&ensp;RAID-01

## `RAID级别`

组合raid的成员大小一致

`RAID-0： `


读、写性能提升 

可用空间：N*min(S1,S2,...) 

无容错能力 

最少磁盘数：2, 2+

`RAID-1：`

读性能提升、写性能略有下降 

可用空间：1*min(S1,S2,...) 

有冗余能力 

最少磁盘数：2, 2N 

`RAID-4：`

 多块数据盘异或运算值存于专用校验盘

`RAID-5：`

读、写性能提升 

可用空间：(N-1)*min(S1,S2,...) 

有容错能力：允许最多1块磁盘损坏 

最少磁盘数：3, 3+

`RAID-6：` 

读、写性能提升 

可用空间：(N-2)*min(S1,S2,...) 

有容错能力：允许最多2块磁盘损坏 

最少磁盘数：4, 4+

`RAID-10：` 

读、写性能提升 

可用空间：N*min(S1,S2,...)/2 

有容错能力：每组镜像最多只能坏一块 

最少磁盘数：4, 4+

`RAID-01:` 

多块磁盘先实现RAID0,再组合成RAID1

`RAID-50:` 

多块磁盘先实现RAID5,再组合成RAID0

`JBOD：`Just a Bunch Of Disks 

功能：将多块磁盘的空间合并一个大的连续空间使用 

可用空间：sum(S1,S2,...)

`RAID7:` 

可以理解为一个独立存储计算机，自身带有操作系统和管理工具，可以独立 运行，理论上性能最高的RAID模式 

## 软RAID

组合raid的成员必须相同的容量，可以磁盘和分区创建

mdadm：为软RAID提供管理界面

为空余磁盘添加冗余 

结合内核中的md(multi devices) 

RAID设备可命名为/dev/md0、/dev/md1、/dev/md2、/dev/md3等

## 软件RAID的实现

mdadm：模式化的工具

&ensp;&ensp;命令的语法格式：mdadm [mode] <raiddevice> [options] <componentdevices> 

&ensp;&ensp;支持的RAID级别：LINEAR, RAID0, RAID1, RAID4, RAID5, RAID6, RAID10 

模式： 

&ensp;&ensp;创建：-C 

&ensp;&ensp;装配：-A 

&ensp;&ensp;监控：-F 

&ensp;&ensp;禁用：-S

&ensp;&ensp;管理：-f, -r, -a 

\<raiddevice>: /dev/md# 

\<component-devices>: 任意块设备

-C: 创建模式 

&ensp;&ensp;-n #: 使用#个块设备来创建此RAID 

&ensp;&ensp;-l #：指明要创建的RAID的级别 

&ensp;&ensp;-a {yes|no}：自动创建目标RAID设备的设备文件 

&ensp;&ensp;-c CHUNK_SIZE: 指明块大小,单位k 

&ensp;&ensp;-x #: 指明空闲盘的个数 

-D：显示raid的详细信息

&ensp;&ensp;mdadm -D /dev/md# 

管理模式： 

&ensp;&ensp;-f: 标记指定磁盘为损坏 

&ensp;&ensp;-a: 添加磁盘 

&ensp;&ensp;-r: 移除磁盘 

观察md的状态： cat /proc/mdstat

软RAID配置示例


使用mdadm创建并定义RAID设备 

&ensp;&ensp;mdadm -C  /dev/md0 -a yes -l 5 -n 3 -x 1  /dev/sd{b,c,d,e}1 

用文件系统对每个RAID设备进行格式化 

&ensp;&ensp;mkfs.xfs /dev/md0 

测试RAID设备 

使用mdadm检查RAID设备的状况 

&ensp;&ensp;mdadm --detail|D /dev/md0 

增加新的成员 

&ensp;&ensp;mdadm –G /dev/md0 –n4  -a /dev/sdf1

## 软RAID测试和修复


模拟磁盘故障 

&ensp;&ensp;mdadm /dev/md0  -f  /dev/sda1 

移除磁盘 

&ensp;&ensp;mdadm /dev/md0 –r /dev/sda1 

从软件RAID磁盘修复磁盘故障 

&ensp;&ensp;• 替换出故障的磁盘然后开机 

&ensp;&ensp;• 在备用驱动器上重建分区 

&ensp;&ensp;• mdadm /dev/md0  -a  /dev/sda1 

mdadm、/proc/mdstat及系统日志信息

## 软RAID管理

生成配置文件：mdadm –D –s  >> /etc/mdadm.conf 

停止设备：mdadm –S /dev/md0 

激活设备：mdadm –A –s /dev/md0 激活 

强制启动：mdadm –R /dev/md0 

删除raid信息：mdadm --zero-superblock /dev/sdb1


### `raid 的文件系统类型为fd `

cat /proc/mdstat 查看创建raid的信息

范例：创建一个raid 0
```bash
分区文件类型都是fd格式
[root@centos7 ~]# lsblk
sdc      8:32   0  100G  0 disk 
└─sdc1   8:33   0    2G  0 part 
sdd      8:48   0   50G  0 disk 
└─sdd1   8:49   0    2G  0 part 
三块分区创建raid 0 一个闲置
[root@centos7 ~]# mdadm -C -a yes /dev/md0 -l 0 -n 3 /dev/sdb2 /dev/sdc1 /dev/sdd1 
查看创建好的信息
[root@centos7 ~]# mdadm -D /dev/md0
创建文件系统
[root@centos7 ~]# mkfs.ext4 /dev/md0
挂载
[root@centos7 ~]# mkdir /raid
[root@centos7 ~]# mount /dev/md0 /raid
[root@centos7 ~]# df
/dev/sdb1        1998538    9236   1979014   1% /test
读写测试
[root@centos7 ~]# dd if=/dev/zero of=/raid/f1 bs=1G count=1 
1+0 records in
1+0 records out
1073741824 bytes (1.1 GB) copied, 8.44665 s, 127 MB/s
[root@centos7 ~]# dd if=/dev/zero of=/data/f1 bs=1G count=1    
1+0 records in
1+0 records out
1073741824 bytes (1.1 GB) copied, 37.3134 s, 28.8 MB/s
```
raid5 

mdadm -C -a yes /dev/md1  -l 5 -n 3 -x 1 /dev/device {4}

不用raid的步骤：

1.取消挂载

2.禁用raid   -S 

3.blkid 查看所使用的分区设备还是属于raid 成员

mdadm --zero-superblock /dev/device分区  清除超级块