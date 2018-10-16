---
title: 软件运行和编译
tags: linux
categories: linux
---

## 软件运行和编译


`ABI：`Application Binary Interface
 
&ensp;&ensp;Windows与Linux不兼容

&ensp;&ensp;&ensp;&ensp;ELF(Executable and Linkable Format)
 
&ensp;&ensp;&ensp;&ensp;PE（Portable Executable）

&ensp;&ensp;库级别的虚拟化：

&ensp;&ensp;&ensp;&ensp;Linux: WINE 

&ensp;&ensp;&ensp;&ensp;Windows: Cygwin

`API：`Application Programming Interface

&ensp;&ensp;POSIX：Portable OS

程序源代码 --> 预处理 --> 编译 --> 汇编 --> 链接

&ensp;&ensp;静态编译：.a 
&ensp;&ensp;动态编译：.so

## 静态和动态链接


&ensp;&ensp;链接主要作用是把各个模块之间相互引用的部分处理好，使得各个模块之间能 够正确地衔接，分为静态链接和动态链接

`静态链接`

&ensp;&ensp;把程序对应的依赖库复制一份到包

&ensp;&ensp;libxxx.a

&ensp;&ensp;嵌入程序包

&ensp;&ensp;升级难，需重新编译

&ensp;&ensp;占用较多空间，迁移容易

`动态链接`

&ensp;&ensp;只把依赖加做一个动态链接

&ensp;&ensp;libxxx.so

&ensp;&ensp;连接指向

&ensp;&ensp;占用较少空间，升级方便

## 开发语言

系统级开发

&ensp;&ensp;C 

&ensp;&ensp;C++ 

应用级开发

&ensp;&ensp;java

&ensp;&ensp;Python

&ensp;&ensp;go

&ensp;&ensp;php 

&ensp;&ensp;perl

&ensp;&ensp;delphi

&ensp;&ensp;ruby

## `包管理器`

二进制应用程序的组成部分：

&ensp;&ensp;二进制文件、库文件、配置文件、帮助文件

程序包管理器：

&ensp;&ensp;debian： deb文件, dpkg包管理器

&ensp;&ensp;redhat： rpm文件, rpm包管理器

&ensp;&ensp;rpm： Redhat Package Manager RPM  Package Manager

## `包命名

源代码：name-VERSION.tar.gz|bz2|xz 

&ensp;&ensp;VERSION: major.minor.release

rpm包命名方式： 

&ensp;&ensp;name-VERSION-release.arch.rpm 

&ensp;&ensp;例：bash-4.2.46-19.el7.x86_64.rpm 

&ensp;&ensp;VERSION: major.minor.release 

&ensp;&ensp;release：release.OS 

常见的arch：

&ensp;&ensp;x86: i386, i486, i586, i686 

&ensp;&ensp;x86_64: x64, x86_64, amd64

&ensp;&ensp;powerpc: ppc （非intel）

跟平台无关：noarch

## 包命名和工具

包：分类和拆包 

&ensp;&ensp;Application-VERSION-ARCH.rpm: 主包 

&ensp;&ensp;Application-devel-VERSION-ARCH.rpm 开发子
包

&ensp;&ensp;Application-utils-VERSION-ARHC.rpm 其它子包 

&ensp;&ensp;Application-libs-VERSION-ARHC.rpm 其它子包

包之间：可能存在依赖关系，甚至循环依赖

解决依赖包管理工具：

&ensp;&ensp;yum：rpm包管理器的前端工具
&ensp;&ensp;（rpm仅可以装单个程序，如果想要解决安装包的依赖就要指定多个程序的名称，yum可以解决安装包的依赖关系）

&ensp;&ensp;apt-get：deb包管理器前端工具

&ensp;&ensp;zypper: suse上的rpm前端管理工具

dnf: Fedora 18+ rpm包管理器前端管理工具

## `库文件`

`查看二进制程序所依赖的库文件` 

&ensp;&ensp;ldd /PATH/TO/BINARY_FILE

`管理及查看本机装载的库文件` 

&ensp;&ensp;ldconfig 加载库文件 

&ensp;&ensp;/sbin/ldconfig -p: 显示本机已经缓存的所有可用库文件名及文件路径 

`映射关系`

&ensp;&ensp;配置文件：(库加载的主配置文件)/etc/ld.so.conf, /etc/ld.so.conf.d/*.conf

&ensp;&ensp;缓存文件：/etc/ld.so.cache

范例：如果误删了应用程序二进制文件依赖的库
(许多程序所依赖的库都有相同的库)
```bash
[root@centos7 ~]# mv /lib64/libc.so.6 /root/（删除库）
重置虚拟机
快速ESC
Troubleshooting 排错
Rescue a CentOS system 救援模式
 1 继续
 2 只读挂在
 3 跳过shell
 4 推出
选1

sh-4.2# ls  
/            此处的/ 不是原来的根
sh-4.2# cd /mnt/sysimage/    这个才是真正的根文件系统
sh-4.2# ls 
...........里面的文件才是真正根的文件...
sh-4.2# cd /mnt/sysimage/root/
sh-4.2# ls   查看里面是否有一个libc.so.6 的文件
sh-4.2# mv /mnt/sysimage/root/libc.so.6 /mnt/sysimage/lib64/
        再移动回去
sh-4.2# exit 推出并自动重启
```
## `包管理器`

程序包管理器：

&ensp;&ensp;功能：将编译好的应用程序的各组成文件打包一个或几个程序包文件，从而 方便快捷地实现程序包的安装、卸载、查询、升级和校验等管理操作 

包文件组成 (每个包独有)

&ensp;&ensp;RPM包内的文件 

&ensp;&ensp;RPM的元数据，如名称，版本，依赖性，描述等 

&ensp;&ensp;安装或卸载时运行的脚本

数据库(公共)：`/var/lib/rpm `（记录安装包的信息.索引）

&ensp;&ensp;程序包名称及版本 

&ensp;&ensp;依赖关系 

&ensp;&ensp;功能说明 

&ensp;&ensp;包安装后生成的各文件路径及校验码信息

## 程序包的来源

管理程序包的方式：

&ensp;&ensp;使用包管理器：rpm

&ensp;&ensp;使用前端工具：yum, dnf

获取程序包的途径：

&ensp;&ensp;(1) 系统发版的光盘或官方的服务器
&ensp;&ensp;CentOS镜像： 

&ensp;&ensp;&ensp;&ensp;https://www.centos.org/download/ 

&ensp;&ensp;&ensp;&ensp;http://mirrors.aliyun.com 

&ensp;&ensp;&ensp;&ensp;http://mirrors.sohu.com 

&ensp;&ensp;&ensp;&ensp;http://mirrors.163.com

&ensp;&ensp;(2) 项目官方站点

&ensp;&ensp;(3) 第三方组织：

&ensp;&ensp;&ensp;&ensp;Fedora-EPEL： 

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;Extra Packages for Enterprise Linux 

&ensp;&ensp;&ensp;&ensp;Rpmforge:RHEL推荐，包很全 

&ensp;&ensp;搜索引擎： 

&ensp;&ensp;&ensp;&ensp;http://pkgs.org 

&ensp;&ensp;&ensp;&ensp;http://rpmfind.net 

&ensp;&ensp;&ensp;&ensp;http://rpm.pbone.net 

&ensp;&ensp;&ensp;&ensp;https://sourceforge.net/ 

&ensp;&ensp;(4) 自己制作

&ensp;&ensp;注意：第三方包建议要检查其合法性 来源合法性,程序包的完整性

## `rpm包管理`

CentOS系统上使用rpm命令管理程序包：(红帽系列版本才可以用的工具)

&ensp;&ensp;`安装、卸载、升级、查询、校验、数据库维护 `

`安装：` rpm装程序必须指定路径，要从光盘查找路径安装

&ensp;&ensp;rpm {-i|--install} [install-options安装相关的辅助选项] PACKAGE_FILE… 

&ensp;&ensp;&ensp;&ensp;-i &ensp; --install 安装

&ensp;&ensp;&ensp;&ensp;-v: 过程

&ensp;&ensp;&ensp;&ensp;-vv: 详细过程

&ensp;&ensp;&ensp;&ensp;-h: 以#显示程序包管理执行进度 

&ensp;&ensp;rpm -ivh PACKAGE_FILE ...


##`rpm包安装`

[install-options] 安装相关的辅助选项 



&ensp;&ensp;--test: 测试安装，但不真正执行安装，即dry run模式 

&ensp;&ensp;--nodeps：忽略依赖关系 

&ensp;&ensp;--replacepkgs ：覆盖包/覆盖安装(用于破坏了程序的某个文件导致程序不可用，重新装显示已有该程序，覆盖包可以找回损坏和丢失的包)

&ensp;&ensp;--replacefiles ：覆盖文件
(覆盖特定的几个文件)

解释：
```bash
--replacepkgs
p1包
file1 文件
file2 文件

--replacefiles
p1
file1
file2
p2
file2
file3
```


&ensp;&ensp;--nosignature: 不检查来源合法性 

&ensp;&ensp;--nodigest：不检查包完整性 

&ensp;&ensp;--noscripts：不执行程序包脚本 

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;%pre: 安装前脚本 --nopre

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;%post: 安装后脚本 --nopost 

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;%preun: 卸载前脚本 --nopreun 

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;%postun: 卸载后脚本 --nopostun



-q &ensp;包名&ensp;(query)查询
范例:查询这个包是否带有脚本
```bash
[root@centos7 ~]# rpm -q bash --scripts bash
postinstall scriptlet (using <lua>):
nl        = '\n'
sh        = '/bin/sh'..nl
bash      = '/bin/bash'..nl
f = io.open('/etc/shells', 'a+')
```

-e &ensp;包名&ensp;卸载



范例：
```bash
[root@centos7 rpm]# rpm -e httpd
error: package httpd is not installed
[root@centos7 rpm]# rpm -q ssh
package ssh is not installed
[root@centos7 rpm]# rpm -q autofs
autofs-5.0.7-83.el7.x86_64
```
范例： 移走数据库（公共）的后果
       说明这个公共数据库不可移走
```bash
[root@centos7 data]# mkdir rpm
[root@centos7 data]# cd -
/var/lib/rpm
[root@centos7 rpm]# mv * /data/rpm
[root@centos7 rpm]# rpm -ivh /misc/cd/Packages/tree-1.6.0-10.el7.x86_64.rpm 
warning: /misc/cd/Packages/tree-1.6.0-10.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID f4a80eb5: NOKEY
error: Failed dependencies:
        libc.so.6()(64bit) is needed by tree-1.6.0-10.el7.x86_64
        libc.so.6(GLIBC_2.14)(64bit) is needed by tree-1.6.0-10.el7.x86_64
        libc.so.6(GLIBC_2.2.5)(64bit) is needed by tree-1.6.0-10.el7.x86_64
        libc.so.6(GLIBC_2.3)(64bit) is needed by tree-1.6.0-10.el7.x86_64
        libc.so.6(GLIBC_2.3.4)(64bit) is needed by tree-1.6.0-10.el7.x86_64
        libc.so.6(GLIBC_2.4)(64bit) is needed by tree-1.6.0-10.el7.x86_64
        rtld(GNU_HASH) is needed by tree-1.6.0-10.el7.x86_64

```

## rpm包升级

升级：慎用 升级覆盖原版本

内核文件支持多版本并存，主要文件放置在/boot目录下

rpm {-U|--upgrade} [install-options] PACKAGE_FILE... 

rpm {-F|--freshen} [install-options] PACKAGE_FILE... 

&ensp;&ensp;upgrade：安装有旧版程序包，则“升级” 

&ensp;&ensp;&ensp;&ensp;如果不存在旧版程序包，则“安装” 

&ensp;&ensp;freshen：安装有旧版程序包，则“升级” 

&ensp;&ensp;&ensp;&ensp;如果不存在旧版程序包，则不执行升级操作 

rpm -Uvh PACKAGE_FILE ... 

rpm -Fvh PACKAGE_FILE ... 

&ensp;&ensp;--oldpackage：降级

&ensp;&ensp;--force: 强制安装(如果一个程序的文件磨损，再次安装显示此文件已经存在，可以用此选项强制安装)


注意： 

&ensp;&ensp;(1) 不要对内核做升级操作；Linux支持多内核版本并存，因此，对直接安装新版 本内核 

&ensp;&ensp;(2) 如果原程序包的配置文件安装后曾被修改，升级时，新版本的提供的同一个配 置文件并不会直接覆盖老版本的配置文件，而把新版本的文件重命名 (FILENAME.rpmnew)后保留

（当多版本内核并存时，卸载内核需要指定版本号 rpm -e kernel.....卸载完内核，uname -r 查看时 还是以前版本，为重启运行的还是缓存中 。升级内核直接安装新内核请勿升级内核）

centos6.2 内核bug 开机达到208.5天自动重启


范例： 查看内核版本
```bash
[root@centos7 ~]# rpm -q kernel
kernel-3.10.0-862.el7.x86_64
```
范例：查看系统版本 6系统
```bash
lsb_release
```

## `包查询`

rpm {-q|--query} [select-options] [query-options]

[select-options] 

&ensp;&ensp;-a: 所有包 

&ensp;&ensp;-f: 查看指定的文件由哪个程序包安装生成 

&ensp;&ensp;-p rpmfile：针对尚未安装的程序包文件做查询操作 

&ensp;&ensp;--whatprovides CAPABILITY：查询指定的关键字由哪个包所提供

&ensp;&ensp;--whatrequires CAPABILITY：查询指定的关键字被哪个包所依赖

&ensp;&ensp;rpm2cpio 包文件|cpio –itv 预览包内文件 

&ensp;&ensp;rpm2cpio 包文件|cpio –id  “*.conf” 释放包内文件

[query-options] 

&ensp;&ensp;--changelog：查询rpm包的changelog 

&ensp;&ensp;-c: 查询程序的配置文件 

&ensp;&ensp;-d: 查询程序的文档 

&ensp;&ensp;-i: information 

&ensp;&ensp;-l: 查看指定的程序包安装后生成的所有文件 

&ensp;&ensp;--scripts：程序包自带的脚本 

&ensp;&ensp;--provides: 列出指定程序包所提供的CAPABILITY 

&ensp;&ensp;-R: 查询指定的程序包所依赖的CAPABILITY

常用查询用法：
-qi PACKAGE, -qf FILE, -qc PACKAGE, -ql PACKAGE, -qd PACKAGE 

-qpi PACKAGE_FILE, -qpl PACKAGE_FILE, ... -qa 

包卸载： 包的卸载也存在依赖性

rpm {-e|--erase} [--allmatches] [--nodeps] [--noscripts] [--notriggers] [--test] PACKAGE_NAME ...

 
rpm -qf &ensp;文件路径&ensp;&ensp;查询指定文件由哪个程序包安装生成

rpm -qa &ensp;&ensp;查询所有包

rpm -qi&ensp;服务名&ensp;&ensp;查询安装包详细信息

rpm -qip&ensp;rpm包名&ensp;&ensp;查询未安装的包的位置

rpm -ql &ensp;rpm包名&ensp;&ensp;查询已安装的安装包的位置

rpm -v &ensp;&ensp校验安装包

rpm -qpl &ensp;&ensp;包名所在的绝对路径包名

rpm -qc &ensp;&ensp;查询这个程序的配置文件

rpm -qd &ensp;&ensp;查询这个包的配置文档





范例：如果tree程序rpm包中丢失了一个文件，我怎么只找回丢失的包内的一个文件

```bash
[root@centos7 Packages]# rpm -qf /usr/bin/tree  （例如此包丢失）
tree-1.6.0-10.el7.x86_64
[root@centos7 Packages]# cp /misc/cd/Packages/tree-1.6.0-10.el7.x86_64.rpm /data
[root@centos7 data]# rpm2cpio tree-1.6.0-10.el7.x86_64.rpm | cpio -tv   （浏览包内文件）
-rwxr-xr-x   1 root     root        62768 Jun 10  2014 ./usr/bin/tree
drwxr-xr-x   2 root     root            0 Jun 10  2014 ./usr/share/doc/tree-1.6.0
-rw-r--r--   1 root     root        18009 Aug 13  2004 ./usr/share/doc/tree-1.6.0/LICENSE
-rw-r--r--   1 root     root         4628 Jun 24  2011 ./usr/share/doc/tree-1.6.0/README
-rw-r--r--   1 root     root         4100 Jun 24  2011 ./usr/share/man/man1/tree.1.gz
177 blocks
[root@centos7 data]# rpm2cpio tree-1.6.0-10.el7.x86_64.rpm | cpio -id ./usr/bin/tree  
（解开此包cp丢失的文件）
177 blocks
[root@centos7 data]# mv ./usr/bin/tree /usr/bin/tree  
移动回去就可以用了,弊端就是丢失文件属性的丢失，建议解包前记好文件的属性

```

查询都是在/var/lib/rpm公共数据库内查找到的
范例：查询tree的所有包
```bash
[root@centos7 ~]# rpm -qa tree
tree-1.6.0-10.el7.x86_64
```
范例： 查看未安装的包内包含的文件 (也是从数据库内查询)
```bash
[root@centos7 data]# rpm -qpl /data/tree-1.6.0-10.el7.x86_64.rpm 
/usr/bin/tree
/usr/share/doc/tree-1.6.0
/usr/share/doc/tree-1.6.0/LICENSE
/usr/share/doc/tree-1.6.0/README
/usr/share/man/man1/tree.1.gz
```
范例：查询bash这个功能是哪个包提供的
```bash
[root@centos7 data]# rpm -q --whatprovides bash
bash-4.2.46-30.el7.x86_64
```
范例： 强制卸载内核怎么办centos6
```bash
[root@centos7 data]# rpm -e kernel --nodeps 
[root@centos7 data]# reboot
ESC
进入救援模式
3  CD -ROM DIRIVE

Rescue installed system

语言
键盘布局
网络

contimue
ok
shell start shell  ok

sh-4.2# mkdir /mnt
sh-4.2# mount /dev/sr0 /mnt  挂在光盘
sh-4.2# df
sh-4.2# rpm -ivh /mnt/cdrom/Packages/kernel-2.6.32-754.e.x86_64.rpm  --root=/mnt/sysimage/
(把kernel安装到真实的根上)

sh-4.2# ls /mnt/sysimage/boot
sh-4.2# exit
reboot

c
grub> kernel /vmlinuz-2.6.32-754.e16x86_64  root=/dev/sda2
grub> initrd  /initramfs-2.6.32-754.e16x86_64.img
grub> boot

```

## 包校验

rpm {-V|--verify} [select-options] [verify-options] 

S file Size differs 

M Mode differs (includes permissions and file type) 

5 digest (formerly MD5 sum) differs 

D Device major/minor number mismatch

L readLink(2) path mismatch U User ownership differs 

G Group ownership differs 

T mTime differs 

P capabilities differ


范例：查询此包包含的文件那些发生了变化
```bash
rpm -V 软件包名
[root@centos7 data]# 
[root@centos7 data]# rpm -V centos-release  
`S`.`5`....`T`.  c /etc/issue
```
## `包校验`

包来源合法性验正及完整性验证 

&ensp;&ensp;完整性验证：SHA256 

&ensp;&ensp;来源合法性验证：RSA 

公钥加密 

&ensp;&ensp;对称加密：加密、解密使用同一密钥 

&ensp;&ensp;非对称加密：密钥是成对儿的 

&ensp;&ensp;&ensp;&ensp;public key: 公钥，公开所有人 

&ensp;&ensp;&ensp;&ensp;secret key: 私钥, 不能公开 

导入所需要公钥 

&ensp;&ensp;rpm  -K|checksig rpmfile 检查包的完整性和签名 

&ensp;&ensp;rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 

&ensp;&ensp;CentOS 7发行版光盘提供：

&ensp;&ensp;&ensp;&ensp;RPM-GPG-KEY-CentOS-7 

&ensp;&ensp;&ensp;&ensp;rpm -qa “gpg-pubkey*”
s

## `rpm数据库`

数据库重建： 

&ensp;&ensp;/var/lib/rpm

rpm {--initdb|--rebuilddb} 

&ensp;&ensp;initdb: 初始化 

&ensp;&ensp;&ensp;&ensp;如果事先不存在数据库，则新建之 ,新建的只是数据结构，而不是丢失的数据

&ensp;&ensp;&ensp;&ensp;否则，不执行任何操作

&ensp;&ensp;rebuilddb：重建已安装的包头的数据库索引目录


