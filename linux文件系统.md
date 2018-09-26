---
title: Linux文件系统及基础命令
tags: linux
categories: linux
---
![image](https://pic.qqtn.com/up/2015-12/2015122916441714265.jpg)
## linux文件系统

`1.`&ensp;文件和目录被组织成一个单根倒置树结构

`2.`&ensp;文件系统从根目录下开始，用“/”表示

`3.`&ensp;根文件系统（rootfs）:root filesystem

`4.`&ensp;文件称区分大小写

`5.`&ensp;以.开头的文件为隐藏文件

`6.`&ensp;路径分隔的/

`7.`&ensp;文件有两大数据：
          数据：data、元数据metadata

`8.`&ensp;文件系统分层结构：LSB linux Standard Base

`9.`&ensp;文件系统层次标准结构：FHS Filesystem Hierarchy Standard

![文件系统及目录结构](https://i04picsos.sogoucdn.com/eec1672eeec507f1)

## 文件名规则
`1.`&ensp;文件名最长255个字节

`2.`&ensp;包括路径在内的文件名称最长4095个字节

`3.`&ensp;蓝色-->目录&ensp;绿色-->可执行文件&ensp;红色-->压缩文件&ensp;浅蓝色-->链接文件&ensp;灰色-->其他文件

`4.`&ensp;除了斜杠和NUL，所有字符都有效，但是使用特殊字符的目录名和文件不推荐使用，有些字符需要用引号用它们

`5.`&ensp;标准linux文件系统（如ext4），文件名称的大小写敏感
例如：MAIL,Mail,mail，mAil

## 文件系统结构
` /boot:`引导文件存放目录，内核文件（vmlinuz),引导加载器（bootloader,grub）都存放与此目录

`/bin:`供所有用户使用的基本命令；不能关联至独立分区，OS启动即会用到的程序

`/sbin:`管理类的基本命令，不能关联至独立的分区，OS启动 即会用到的程序

`/lib :`基本共享库文件，以及内核模块文件（/lib/modules）

`/lib64:`专用于X86_64系统上的辅助文件存放的位置

`/etc :`配置文件目录（纯文本文件）

`/home/username：`普通用户家目录

`/root:`管理员家目录

`/media:`便携式移动设备挂载点  cdroom、usb

`/mnt :`临时文件系统挂载点

`/dev :`设备文件及特殊文件存储位置    b:block device 随机访问   c:character device 线性访问

`/opt :`第三方应用程序的安装位置

`/srv :`系统上运行的服务用到的数据

`/tmp ：`临时文件存储的位置

` /usr ：`

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`bin ：`保证系统拥有完整功能而提供的应用程序

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`sbin：`管理类的基本命令，不能关联至独立的分区，OS启动 即会用到的程序

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`lib ：`基本共享库文件，以及内核模块文件（/lib/modules）

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`lib64:`专用于X86_64系统上的辅助文件存放的位置

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`indude:`C程序的头文件。

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`share:`结构化独立的数据  例：doc、man 等

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`local:`第三方应用程序的安装位置  bin、sbin、lib、lib64、etc、share

`/var :`日志数据文件

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`cache:`应用程序缓存的数据目录

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`lib :`应用程序状态信息的数据

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`local:`专用于为/usr/local下的应用程序存储可变的数据

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`lock:`锁文件

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`log ：`日志目录及文件

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`opt :`专用于为/opt下的应用程序的存储可变的数据

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`run :`运行中的进程相关的数据；通常用于存储进程的pid文件

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`spool:`应用程序的数据池

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;`tmp :`保存系统两次重启之间产生的临时数据

`/proc:`用于输出内核与进程信息相关的虚拟文件系统

`/sys ：`用于输出当前系统上硬件设备相关的信息的虚拟文件系统。   大小为0 ，内存文件

`/selinx:`security enhanched linux、selinux相关的安全策略信息存储位置。 大小为0，内存文件

## Linux上的应用程序的组成

`二进制程序：`/bin /sbin /usr/bin /usr/sbin /usr/local/bin /usr/local/sb

`库文件：`/lib, /lib64, /usr/lib, /usr/lib64, /usr/local/lib, /usr/local/lib

`配置文件：`/etc /etc/DIRECTORY /usr/local/e

`帮助文件：`/usr/share/man, /usr/share/doc, /usr/local/share/man, 
/usr/local/share/doc


## Linux下的文件类型
&ensp;&ensp;查看linux下的文件类型 可以用&ensp;&ensp;ls&ensp;-&ensp;l&ensp;文件&ensp;&ensp;查看属性开头第一个 例如：
```bash
[root@centos7 ~]#ls -l /
total 28
-rw-r--r--.   1 root root    0 Sep 24 11:38 app
lrwxrwxrwx.   1 root root    7 Sep 19 20:57 bin -> usr/bin
dr-xr-xr-x.   5 root root 4096 Sep 20 08:47 boot
drwxr-xr-x.   5 root root   53 Sep 25 14:35 data
drwxr-xr-x.   2 root root 4096 Sep 24 17:48 date
drwxr-xr-x.  19 root root 3320 Sep 25 20:27 dev
drwxr-xr-x. 134 root root 8192 Sep 25 20:27 etc
drwxr-xr-x.   3 root root   19 Sep 19 21:20 home
lrwxrwxrwx.   1 root root    7 Sep 19 20:57 lib -> usr/lib
lrwxrwxrwx.   1 root root    9 Sep 19 20:57 lib64 -> usr/lib64
drwxr-xr-x.   2 root root    6 Apr 11 12:59 media
drwxr-xr-x.   2 root root    6 Apr 11 12:59 mnt
drwxr-xr-x.   3 root root   16 Sep 19 21:10 opt
dr-xr-xr-x. 197 root root    0 Sep 25 20:27 proc
````
&ensp;`-：`普通文件

&ensp;`d:` 目录文件

&ensp;`b:`块设备

&ensp;`c:` 字符设备

&ensp;`l:` 符号链接文件

&ensp;`p:` 管道文件pipe

&ensp;`s:` 套接字文件sock

&ensp;&ensp;文件名最长255个字节包括路径在内文件名称最长4095个字节蓝色-->目录 绿色-->可执行文件 红色-->压缩文件 浅蓝色-->链接文件 灰色-->其他文件除了斜杠和NUL,所有字符都有效.但使用特殊字符的目录名和文件不推荐使用，有些字符需要用引号来引用它们标准Linux文件系统（如ext4），文件名称大小写敏感例如：MAIL, Mail, mail, mAil

![学习使我快乐](https://pic.qqtn.com/up/2016-8/14720010336501371.gif)

## &ensp;&ensp;有没有领会linux文件系统了么______点个星星鼓励一下吧！

![学习使我快乐](https://pic.qqtn.com/up/2016-8/2016082409143614155.gif)