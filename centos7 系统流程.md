---
title: centos 7 操作系统
tags: linux
categories: linux
---
## centos 7 操作系统

## systemd

init: 

CentOS 5: SysV init


CentOS 6: Upstart


CentOS 7: Systemd


Systemd：系统启动和服务器守护进程管理器，负责在系统启动或运行时，激
活系统资源，服务器进程和其它进程

Systemd新特性：

&ensp;&ensp;系统引导时实现服务并行启动

&ensp;&ensp;按需启动守护进程

&ensp;&ensp;自动化的服务依赖关系管理

&ensp;&ensp;同时采用socket式与D-Bus总线式激活服务

&ensp;&ensp;系统状态快照

核心概念：unit

&ensp;&ensp;unit表示不同类型的systemd对象，通过配置文件进行标识和配置；文件中
主要包含了系统服务、监听socket、保存的系统快照以及其它与init相关的信息

配置文件：

&ensp;&ensp;/usr/lib/systemd/system:每个服务最主要的启动脚本设置，类似于之前的/etc/init.d/

&ensp;&ensp;/run/systemd/system：系统执行过程中所产生的服务脚本，比上面目录
优先运行

&ensp;&ensp;/etc/systemd/system：管理员建立的执行脚本，类似于
/etc/rc.d/rcN.d/Sxx类的功能，比上面目录优先运行

## Unit类型

Systemctl –t help 查看unit类型

Service unit: 文件扩展名为.service, 用于定义系统服务

Target unit: 文件扩展名为.target，用于模拟实现运行级别

Device unit: .device, 用于定义内核识别的设备

Mount unit: .mount, 定义文件系统挂载点

Socket unit: .socket, 用于标识进程间通信用的socket文件，也可在系统启动时，
延迟启动服务，实现按需启动

Snapshot unit: .snapshot, 管理系统快照

Swap unit: .swap, 用于标识swap设备

Automount unit: .automount，文件系统的自动挂载点

Path unit: .path，用于定义文件系统中的一个文件或目录使用,常用于当文件系
统变化时，延迟激活服务，如：spool 目录

## 特性

关键特性：

&ensp;&ensp;基于socket的激活机制：socket与服务程序分离


&ensp;&ensp;基于d-bus的激活机制：


&ensp;&ensp;基于device的激活机制：


&ensp;&ensp;基于path的激活机制：


&ensp;&ensp;系统快照：保存各unit的当前状态信息于持久存储设备中


&ensp;&ensp;向后兼容sysv init脚本

不兼容：


&ensp;&ensp;systemctl命令固定不变，不可扩展


&ensp;&ensp;非由systemd启动的服务，systemctl无法与之通

## 管理服务

管理系统服务：

&ensp;&ensp;CentOS 7: service unit

&ensp;&ensp;注意：能兼容早期的服务脚本

&ensp;&ensp;命令：systemctl COMMAND name.service

&ensp;&ensp;启动：service name start ==> systemctl start name.service

&ensp;&ensp;停止：service name stop ==> systemctl stop name.service

&ensp;&ensp;重启：service name restart ==> systemctl restart name.service

&ensp;&ensp;状态：service name status ==> systemctl status name.service

条件式重启：已启动才重启，否则不做操作

&ensp;&ensp;service name condrestart ==> systemctl try-restart name.service

重载或重启服务：先加载，再启动

&ensp;&ensp;systemctl reload-or-restart name.service

重载或条件式重启服务：

&ensp;&ensp;systemctl reload-or-try-restart name.service

禁止自动和手动启动：

&ensp;&ensp;systemctl mask name.service

取消禁止：

&ensp;&ensp;systemctl unmask name.service

## 服务查看

查看某服务当前激活与否的状态：

&ensp;&ensp;systemctl is-active name.service

查看所有已经激活的服务：

&ensp;&ensp;systemctl list-units --type|-t service 

查看所有服务：

&ensp;&ensp;systemctl list-units --type service --all|-a


chkconfig命令的对应关系：


设定某服务开机自启：

&ensp;&ensp;chkconfig name on ==> systemctl enable name.service


设定某服务开机禁止启动：

&ensp;&ensp;chkconfig name off ==> systemctl disable name.service

## 服务查看

查看所有服务的开机自启状态：

&ensp;&ensp;chkconfig --list ==> systemctl list-unit-files --type service 

用来列出该服务在哪些运行级别下启用和禁用

&ensp;&ensp;chkconfig sshd –list ==>

&ensp;&ensp;ls/etc/systemd/system/*.wants/sshd.service

查看服务是否开机自启：
systemctl is-enabled name.service

其它命令：

查看服务的依赖关系：

&ensp;&ensp;systemctl list-dependencies name.service

杀掉进程：

&ensp;&ensp;systemctl kill unitname

## 服务状态

systemctl list-unit-files --type service --all显示状态


loaded:Unit配置文件已处理


active(running):一次或多次持续处理的运行


active(exited):成功完成一次性的配置


active(waiting):运行中，等待一个事件

inactive:不运行


enabled:开机启动

disabled:开机不启动

static:开机不启动，但可被另一个启用的服务激活

## systemctl 命令示例

显示所有单元状态
systemctl
或 systemctl list
-units


只显示服务单元的状态
systemctl --type=service


显示ssh
d服务单元
systemctl
–l status sshd.service

验证sshd服务当前是否活动
systemctl is
-active sshd


启动，停止和重启sshd服务

systemctl start sshd.service

systemctl stop sshd.service

systemctl restart sshd.service

## systemctl 命令示例

重新加载配置  

&ensp;&ensp;systemctl reload sshd.service 

列出活动状态的所有服务单元  

&ensp;&ensp;systemctl list-units --type=service 

列出所有服务单元  

&ensp;&ensp;systemctl list-units --type=service --all 

查看服务单元的启用和禁用状态  

&ensp;&ensp;systemctl list-unit-files  --type=service 

列出失败的服务  

&ensp;&ensp;systemctl --failed --type=service

列出依赖的单元  

&ensp;&ensp;systemctl list-dependencies sshd 

验证sshd服务是否开机启动  

&ensp;&ensp;systemctl is-enabled sshd 

禁用network，使之不能自动启动,但手动可以  

&ensp;&ensp;systemctl disable network 

启用network  

&ensp;&ensp;systemctl enable  network 

禁用network，使之不能手动或自动启动  

&ensp;&ensp;systemctl mask network 

启用network  

&ensp;&ensp;systemctl unmask network 

## service unit文件格式 

/etc/systemd/system：系统管理员和用户使用
/usr/lib/systemd/system：发 行版打包者使用 

以 “#” 开头的行后面的内容会被认为是注释 

相关布尔值，1、yes、on、true 都是开启，0、no、off、false 都是关闭 

时间单位默认是秒，所以要用毫秒（ms）分钟（m）等须显式说明 


service unit file文件通常由三部分组成： 

&ensp;&ensp;[Unit]：定义与Unit类型无关的通用选项；用于提供unit的描述信息、unit行 为及依赖关系等 

&ensp;&ensp;[Service]：与特定类型相关的专用选项；此处为Service类型 

&ensp;&ensp;[Install]：定义由“systemctl  enable”以及"systemctl  disable“命令在 实现服务启用或禁用时用到的一些选项   

Unit段的常用选项： 

Description：描述信息 

After：定义unit的启动次序，表示当前unit应该晚于哪些unit启动，其功能与 Before相反 

Requires：依赖到的其它units，强依赖，被依赖的units无法激活时，当前unit 也无法激活 

Wants：依赖到的其它units，弱依赖 

Conflicts：定义units间的冲突关系 

Service段的常用选项： 

&ensp;&ensp;Type：定义影响ExecStart及相关参数的功能的unit进程启动类型 

&ensp;&ensp;simple：默认值，这个daemon主要由ExecStart接的指令串来启动，启动后常 驻于内存中 

&ensp;&ensp;forking：由ExecStart启动的程序透过spawns延伸出其他子程序来作为此 daemon的主要服务。原生父程序在启动结束后就会终止 

&ensp;&ensp;oneshot：与simple类似，不过这个程序在工作完毕后就结束了，不会常驻在 内存中 

&ensp;&ensp;dbus：与simple类似，但这个daemon必须要在取得一个D-Bus的名称后，才 会继续运作.因此通常也要同时设定BusNname= 才行 •

&ensp;&ensp;notify：在启动完成后会发送一个通知消息。还需要配合 NotifyAccess 来让 Systemd 接收消息 

&ensp;&ensp;idle：与simple类似，要执行这个daemon必须要所有的工作都顺利执行完毕后 才会执行。这类的daemon通常是开机到最后才执行即可的服务  

EnvironmentFile：环境配置文件 

ExecStart：指明启动unit要运行命令或脚本的绝对路径 

ExecStartPre： ExecStart前运行 

ExecStartPost： ExecStart后运行 

ExecStop：指明停止unit要运行的命令或脚本 

Restart：当设定Restart=1 时，则当次daemon服务意外终止后，会再次自动 启动此服务 

Install段的常用选项： 

&ensp;&ensp;Alias：别名，可使用systemctl command Alias.service  

&ensp;&ensp;RequiredBy：被哪些units所依赖，强依赖 

&ensp;&ensp;WantedBy：被哪些units所依赖，弱依赖 •

&ensp;&ensp;Also：安装本服务的时候还要安装别的相关服务 

注意：对于新创建的unit文件，或者修改了的unit文件，要通知systemd重载此 配置文件,而后可以选择重启  

&ensp;&ensp;systemctl  daemon-reload 

## 服务Unit文件示例： 
vim /etc/systemd/system/bak.service  

&ensp;&ensp;[Unit] 

&ensp;&ensp;Description=backup  /etc 

&ensp;&ensp;Requires=atd.service 

&ensp;&ensp;[Service] 

&ensp;&ensp;Type=simple 

&ensp;&ensp;ExecStart=/bin/bash -c "echo /testdir/bak.sh|at now" 

&ensp;&ensp;[Install] 

&ensp;&ensp;WantedBy=multi-user.target 

systemctl daemon-reload 

systemctl start bak 

## 运行级别 

target units：  

&ensp;&ensp;unit配置文件：.target  

&ensp;&ensp;ls /usr/lib/systemd/system/*.target  

&ensp;&ensp;systemctl list-unit-files --type target  --all 

运行级别：  

&ensp;&ensp;0  ==> runlevel0.target, poweroff.target  

&ensp;&ensp;1  ==> runlevel1.target, rescue.target  

&ensp;&ensp;2  ==> runlevel2.target, multi-user.target  

&ensp;&ensp;3  ==> runlevel3.target, multi-user.target  

&ensp;&ensp;4  ==> runlevel4.target, multi-user.target  

&ensp;&ensp;5  ==> runlevel5.target, graphical.target  

&ensp;&ensp;6  ==> runlevel6.target, reboot.target 

查看依赖性：  

&ensp;&ensp;systemctl list-dependencies graphical.target 

级别切换：init N ==> systemctl isolate name.target  

&ensp;&ensp;systemctl isolate multi-user.target  

&ensp;&ensp;注：只有/lib/systemd/system/*.target文件中AllowIsolate=yes 才能切换 (修改文件需执行systemctl daemon-reload才能生效) 

查看target：  

&ensp;&ensp;runlevel ;who -r  

&ensp;&ensp;systemctl list-units --type target  

获取默认运行级别：  

&ensp;&ensp;/etc/inittab ==> systemctl get-default 

修改默认级别：    

&ensp;&ensp;/etc/inittab ==> systemctl set-default name.target  

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;systemctl set-default multi-user.target  

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;ls –l /etc/systemd/system/default.target 

## 其它命令 


切换至紧急救援模式：  

&ensp;&ensp;systemctl rescue 

切换至emergency模式：  

&ensp;&ensp;systemctl emergency 

其它常用命令：  

&ensp;&ensp;传统命令init，poweroff，halt，reboot都成为   

&ensp;&ensp;&ensp;&ensp;systemctl的软链接  

&ensp;&ensp;关机：systemctl halt、systemctl poweroff  

&ensp;&ensp;重启：systemctl reboot  

&ensp;&ensp;挂起：systemctl suspend 

&ensp;&ensp;休眠：systemctl hibernate  

&ensp;&ensp;休眠并挂起：systemctl hybrid-sleep 

## CentOS7引导顺序 


UEFi或BIOS初始化，运行POST开机自检 

选择启动设备 

引导装载程序, centos7是grub2 

加载装载程序的配置文件：  

&ensp;&ensp;/etc/grub.d/   

&ensp;&ensp;/etc/default/grub   

&ensp;&ensp;/boot/grub2/grub.cfg 

加载initramfs驱动模块 

加载内核选项 

内核初始化，centos7使用systemd代替init  

执行initrd.target所有单元，包括挂载/etc/fstab 

从initramfs根文件系统切换到磁盘根目录 

systemd执行默认target配置，配置文件/etc/systemd/system/default.target 

## CentOS7引导顺序


&ensp;&ensp;systemd执行sysinit.target
初始化系统及basic.target准备操作系统 

&ensp;&ensp;systemd启动multi-user.target下的本机与服务器服务 

&ensp;&ensp;systemd执行multi-user.target下的/etc/rc.d/rc.local 

&ensp;&ensp;Systemd执行multi-user.target下的getty.target及登录服务 

&ensp;&ensp;systemd执行graphical需要的服务 


## 设置内核参数

设置内核参数，只影响当次启动 

&ensp;&ensp;启动时，在linux16行后添加systemd.unit=desired.target 

&ensp;&ensp;systemd.unit=emergency.target  

&ensp;&ensp;systemd.unit=rescue.target 

&ensp;&ensp;rescue.target 比emergency 支持更多的功能，例如日志等 

&ensp;&ensp;systemctl  default  进入默认target 

## 启动排错

文件系统损坏 

&ensp;&ensp;先尝试自动修复，失败则进入emergency shell，提示用户修复 

在/etc/fstab不存在对应的设备和UUID 

&ensp;&ensp;等一段时间，如不可用，进入emergency shell 

在/etc/fstab不存在对应挂载点 

&ensp;&ensp;systemd 尝试创建挂载点，否则提示进入emergency shell. 

在/etc/fstab不正确的挂载选项 

&ensp;&ensp;提示进入emergency shell 
 
## 破解CentOS7的root口令方法一 

方法1：

&ensp;&ensp;启动时任意键暂停启动 

&ensp;&ensp;按e键进入编辑模式 

&ensp;&ensp;将光标移动linux16开始的行，添加内核参数rd.break 

&ensp;&ensp;按ctrl-x启动  

&ensp;&ensp;mount –o remount,rw  /sysroot 

&ensp;&ensp;chroot /sysroot 

&ensp;&ensp;passwd root 

&ensp;&ensp;touch /.autorelabel 

&ensp;&ensp;exit 

&ensp;&ensp;reboot 
 
 方法2：

&ensp;&ensp;启动时任意键暂停启动 

&ensp;&ensp;按e键进入编辑模式 

&ensp;&ensp;将光标移动linux16开始的行，改为rw init=/sysroot/bin/sh 

&ensp;&ensp;按ctrl-x启动  

&ensp;&ensp;chroot /sysroot 

&ensp;&ensp;passwd root 

&ensp;&ensp;touch /.autorelabel 

&ensp;&ensp;exit 

&ensp;&ensp;reboot 
 
## 修复GRUB2 

GRUB“the Grand Unified Bootloader” 

&ensp;&ensp;引导提示时可以使用命令行界面 

&ensp;&ensp;可从文件系统引导 

主要配置文件 /boot/grub2/grub.cfg 

修复配置文件 

&ensp;&ensp;grub2-mkconfig > /boot/grub2/grub.cfg 

&ensp;&ensp;grub2-o mkconfi -o /boot/grub2/grub.cfg于上一条命令等价

修复grub 

&ensp;&ensp;grub2-install /dev/sda  BIOS环境 

&ensp;&ensp;grub2-install  UEFI环境 

调整默认启动内核  

&ensp;&ensp;vim /etc/default/grub  

&ensp;&ensp;GRUB_DEFAULT=0 