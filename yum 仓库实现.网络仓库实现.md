---
title: yum 仓库实现
tags: linux
categories: linux
---
## yum

CentOS: yum, dnf

YUM: Yellowdog Update Modifier，rpm的前端程序，可解决软件包相关依 赖性，可在多个库之间定位软件包，up2date的替代工具 

&ensp;&ensp;yum repository: yum repo，存储了众多rpm包，以及包的相关的元数据 

文件（放置于特定目录repodata下） 

&ensp;&ensp;文件服务器： 

&ensp;&ensp;&ensp;&ensp;http:// 

&ensp;&ensp;&ensp;&ensp;https:// 

&ensp;&ensp;&ensp;&ensp;ftp:// 

&ensp;&ensp;&ensp;&ensp;file://


## `导入所需要公钥 `

rpm  -K|checksig rpmfile 检查包的完整性和签名 

rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 

CentOS 7发行版光盘提供：RPM-GPG-KEY-CentOS-7 

rpm -qa “gpg-pubkey*”

## yum配置文件


yum客户端配置文件： 

&ensp;&ensp;/etc/yum.conf：为所有仓库提供公共配置 

&ensp;&ensp;/etc/yum.repos.d/*.repo：为仓库的指向提供配置 

仓库指向的定义： 

&ensp;&ensp;[repositoryID] 名称

&ensp;&ensp;name=Some name for this repository 
描述
&ensp;&ensp;baseurl=url://path/to/repository/  yum源路径

&ensp;&ensp;enabled={1|0}
启用/禁用仓库

&ensp;&ensp;gpgcheck={1|0} 

&ensp;&ensp;gpgkey=URL （告知yum仓库密钥）

&ensp;&ensp;enablegroups={1|0} 启用组默认启用

failovermethod={roundrobin|priority} 

&ensp;&ensp;&ensp;&ensp;roundrobin：意为随机挑选，默认值 

&ensp;&ensp;&ensp;&ensp;priority:按顺序访问 

&ensp;&ensp;cost=  默认为1000

范例：不检查K光盘本地yum源
```bash
[root@centos7 ~]# vim /etc/yum.repos.d/haha.conf
[root@centos7 ~]# cat /etc/yum.repos.d/haha.conf
[haha]
name=haha
baseurl=file:///misc/cd/
enabled=1
#gpgcheck=0
```
范例：指定yum源检查K
```bash
[root@centos7 ~]# cat /etc/yum.repos.d/haha.conf 
[haha]
name=haha
baseurl=file:///misc/cd/
enabled=1
#gpgcheck=0
gpgkey=file:///misc/cd/RPM-GPG-KEY-CENTOS-7

```
## `yum仓库`

`yum的repo配置文件中可用的变量： `

&ensp;&ensp;`$releasever: `当前OS的发行版的主版本号 

&ensp;&ensp;$arch: 平台，i386,i486,i586,x86_64等 

&ensp;&ensp;`$basearch：`基础平台；i386, x86_64 

&ensp;&ensp;$YUM0-$YUM9:自定义变量 

实例: 

&ensp;&ensp;http://server/centos/$releasever/$basearch/ 

&ensp;&ensp;http://server/centos/7/x86_64 

&ensp;&ensp;http://server/centos/6/i384

## yum源


阿里云repo文件 http://mirrors.aliyun.com/epel/$releaserver/$basearch 

`CentOS系统的yum源`

`阿里云：`
http://mirrors.aliyun.com/centos/$releasever/$basearch 

`清华大学：`https://mirrors.tuna.tsinghua.edu.cn/epel/$releasever/$baserach

`EPEL的yum源`

阿里云：https://mirrors.aliyun.com/epel/$releasever/x86_64

阿里巴巴开源软件 https://opsx.alibaba.com/


## `yum命令`

`yum命令的用法：` 

&ensp;&ensp;yum [options] [command] [package ...] 

`显示仓库列表：` 

&ensp;&ensp;yum repolist [all|enabled|disabled] 

`显示程序包：` 

&ensp;&ensp;yum list 

&ensp;&ensp;yum list [all | glob_exp1] [glob_exp2] [...] 

&ensp;&ensp;yum list {available|installed|updates} [glob_exp1] [...] 

`安装程序包： `

&ensp;&ensp;yum install package1 [package2] [...] 

&ensp;&ensp;yum reinstall package1 [package2] [...]  (重新安装)

`升级程序包： `

&ensp;&ensp;yum update [package1] [package2] [...] 

&ensp;&ensp;yum downgrade package1 [package2] [...] (降级) 

`检查可用升级： `

&ensp;&ensp;yum check-update 

`卸载程序包：` 

&ensp;&ensp;yum remove | erase package1 [package2] [...]

`查看程序包information：` 查看仓库里的信息

&ensp;&ensp;yum info [...] 

`查看指定的特性(可以是某文件)是由哪个程序包所提供：`

&ensp;&ensp;yum provides | whatprovides feature1 [feature2] [...] 

`清理本地缓存：` 

`清除`


/var/cache/yum/$basearch/$releasever缓存 

&ensp;&ensp;yum clean [ packages | metadata | expire-cache | rpmdb | plugins | all ] 

`构建缓存： `

&ensp;&ensp;yum makecache


`搜索：`

&ensp;&ensp;`yum search string1 [string2] [...] `

&ensp;&ensp;以指定的关键字搜索程序包名及summary信息 

`查看指定包所依赖的capabilities： `

&ensp;&ensp;yum deplist package1 [package2] [...] 

`查看yum事务历史： `

&ensp;&ensp;yum history [info|list|packages-list|packages-info| 

&ensp;&ensp;summary|addon-info|redo|undo| 

&ensp;&ensp;rollback|new|sync|stats]

 `yum history `

&ensp;&ensp;yum history info 6 

&ensp;&ensp;yum history undo 6 撤销第几部操作先用yum history 查看

&ensp;&ensp;yum history redo 6 重做  撤销上次撤销的动作



`日志 ：/var/log/yum.log`

`安装及升级本地程序包：` 

&ensp;&ensp;yum localinstall rpmfile1 [rpmfile2] [...] (用install替代) 

&ensp;&ensp;yum localupdate rpmfile1 [rpmfile2] [...] (用update替代) 

`包组管理的相关命令：` 

&ensp;&ensp;yum groupinstall group1 [group2] [...] 

&ensp;&ensp;yum groupupdate group1 [group2] [...] 

&ensp;&ensp;yum grouplist [hidden] [groupwildcard] [...] 

&ensp;&ensp;yum groupremove group1 [group2] [...] 

&ensp;&ensp;yum groupinfo group1 [...]

`yum的命令行选项：`

 &ensp;&ensp;--nogpgcheck：禁止进行gpg check 
 
 &ensp;&ensp;-y: 自动回答为“yes” 
 
 &ensp;&ensp;-q：静默模式 
 
 &ensp;&ensp;--disablerepo=repoidglob：临时禁用此处指定的repo 
 
 &ensp;&ensp;--enablerepo=repoidglob：临时启用此处指定的
 repo 
 
 &ensp;&ensp;--noplugins：禁用所有插件

`命令定义yum仓库`


&ensp;&ensp; 生成172.16.0.1_cobbler_ks_mirror_CentOS-X-x86_64_.repo 
 
&ensp;&ensp;yum-config-manager   --add-repo= http://172.16.0.1/cobbler/ks_mirror/7/  
 
&ensp;&ensp;yum-config-manager --disable “仓库名"  禁用仓库 
 
&ensp;&ensp;yum-config-manager --enable  “仓库名” 启用仓库 

范例：查看仓库内的包
```bash
[root@centos7 ~]# yum list all
@开头的是已经安装的包
@anaconda 安装系统安装向导
@base 利用yum源安装的

``` 

范例：通过脚本实现配置yum配置文件和判断httpd是否安装，如果没安装的话安装服务
```bash
[root@centos7 ~]# cat httpd.sh 
#!/bin/bash
#
cat > /etc/yum.repos.d/haha.repo <<EOF
[haha]
name=haha
baseurl=file:///misc/cd/
enable=1
gpgcheck=0
EOF
http=package httpd is not installed
rpm -q httpd &> /dev/null || yum install httpd -y
```

分析/etc/yum.conf   (yum客户端的主配置文件)
```bash
[root@centos7 ~]# cat /etc/yum.conf 
[main]
cachedir=/var/cache/yum/$basearch/$releasever
（yum缓存路径，缓存yum仓库的元数据，装完程序自动清除缓存）
keepcache=0 （定义装完程序清理缓存）
debuglevel=2（调试模式）
logfile=/var/log/yum.log（日志信息）
exactarch=1（是否必须匹配架构）
obsoletes=1
gpgcheck=1（K是否检查）
plugins=1（是否支持插件）
installonly_limit=5
bugtracker_url=http://bugs.centos.org/set_project.php?project_id=23&ref=http://bugs.centos.org/bug_report_page.php?category=yum
distroverpkg=centos-release
```
## 实现网络仓库
范例：基于网络搭建一个内网的yum源(关闭防火墙和selinux)

centos7 关闭防火墙

systemctl &ensp;disbale&ensp;firewall

systemctl &ensp;stop&ensp;firewall

centos6 关闭防火墙

chkconfig &ensp;iptables&ensp;off

service &ensp;iptables&ensp;stop
```bash
前提yum配置文件已经有yum源
[root@centos7 ~]# yum install httpd
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile
Package matching httpd-2.4.6-80.el7.centos.x86_64 already installed. Checking for update.
Nothing to do

[root@centos7 html]# mkdir -p centos/{6,7}/os/yum
[root@centos7 html]# tree
.
├── centos6
│   └── os
│       └── yum
└── centos7
    └── os
        └── yum
http://192.168.131.171/centos/

[root@centos7 html]# mount /dev/sr0 /var/www/html/centos/7/os/yum/
mount: /dev/sr0 is write-protected, mounting read-only

http://192.168.131.171/centos/7/os/yum/

接下来就可以配置yum仓库了
如果想放centos6的yum 可以加一块光盘
可以用echo ---> /sysclass/sicsi_host/host2/scan命令开机扫描新加的光盘省的重启
然后让centos6的光盘挂在到/var/www/html/centos/6/os/yum

```
```bash
[root@centos7 html]# cat /etc/yum.repos.d/CentOS-Base.repo 
```


在阿帕奇数据主目录下写入yum配置文件的baseurl  文件


然后在yum配置文件中写入mirrorlist=http://ip /创建文件注释掉其他的baseurl

## 确定仓库路径的方法是查看repodata目录的位置的上一级

范例：定义yum要看repodata目录，要是使用key也要指定key的上级目录
```bash
[root@centos7 cd]# ls
CentOS_BuildTag  images    repodata
EFI              isolinux  RPM-GPG-KEY-CentOS-7
EULA             LiveOS    RPM-GPG-KEY-CentOS-Testing-7
GPL              Packages  TRANS.TBL
[root@centos7 cd]# pwd
/misc/cd
[root@centos7 cd]# cat /etc/yum.repos.d/haha.repo 
[haha]
name=haha
baseurl=file:///misc/cd
enabled=1
gpgkey=file:///misc/cd/RPM-GPG-KEY-CentOS-7
[root@centos7 cd]# 

```
范例：卸载这块网卡virbr0: 
```bash
[root@centos7 cd]# yum remove libvirt-daemon

reboot
```
范例：查看bash依赖哪些包

```bash
[root@centos7 cd]# yum deplist bash
Loaded plugins: fastestmirror, langpacks
Loading mirror speeds from cached hostfile


 * base: mirrors.tuna.tsinghua.edu.cn
 * extras: mirrors.huaweicloud.com
 * updates: mirrors.huaweicloud.com
package: bash.x86_64 4.2.46-30.el7
  dependency: libc.so.6(GLIBC_2.15)(64bit)
   provider: glibc.x86_64 2.17-222.el7
  dependency: libdl.so.2()(64bit)
   provider: glibc.x86_64 2.17-222.el7
  dependency: libdl.so.2(GLIBC_2.2.5)(64bit)
   provider: glibc.x86_64 2.17-222.el7
```
范例：自建rpm源  (例子为tree)
```bash
[root@centos7 ~]# yum remove tree

[root@centos7 data]# cp /misc/cd/Packages/tree-1.6.0-10.el7.x86_64.rpm /data/
[root@centos7 data]# ls
tree-1.6.0-10.el7.x86_64.rpm

[root@centos7 data]# createrepo /data/
[root@centos7 data]# ls
repodata  tree-1.6.0-10.el7.x86_64.rpm

修改配置文件修改路径
yum repolist all
```
## 确定仓库路径的方法是查看repodata目录的位置的上一级
















