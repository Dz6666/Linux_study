---
title: cobbler自动化安装
tags: linux服务
categories: linux服务
---

## cobbler 介绍 
Cobbler: 

快速网络安装linux操作系统的服务，支持众多的Linux发行版：Red Hat、 Fedora、CentOS、Debian、Ubuntu和SuSE，也可以支持网络安装windows 

PXE的二次封装，将多种安装参数封装到一个菜单 

Python编写 

提供了CLI和Web的管理形式 

![cobbler工作流程](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/cobbler%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png)

## cobbler 工作流程 

client裸机配置了从网络启动后，开机后会广播包请求DHCP服务器（cobbler server） 发送其分配好的一个IP 

DHCP服务器（cobbler server）收到请求后发送responese，包括其ip地址 

client裸机拿到ip后再向cobbler server发送请求OS引导文件的请求 

cobbler server告诉裸机OS引导文件的名字和TFTP server的ip和port 

client裸机通过上面告知的TFTP server地址通信，下载引导文件 

client裸机执行执行该引导文件，确定加载信息，选择要安装的os，期间会再向 cobbler server请求kickstart文件和os image 

cobbler server发送请求的kickstart和os iamge 

client裸机加载kickstart文件 

client裸机接收os image，安装该os image 

## cobbler 介绍 


安装包 cobbler 基于EPEL源 

cobbler 服务集成 

&ensp;&ensp;PXE 、DHCP 、rsync 、Http 、DNS 、Kickstart 、IPMI 电源管理 

检查cobbler环境 

&ensp;&ensp;cobbler check 

## cobbler 相关术语 

发行版： 

&ensp;&ensp;表示一个操作系统版本，它承载了内核和 initrd 的信息，以及内核参数等其他数据 

配置文件： 

&ensp;&ensp;包含一个发行版、一个 kickstart 文件以及可能的存储库，还包含更多特定的内核参 数等其他数据 

系统： 

&ensp;&ensp;表示要配置的主机，它包含一个配置文件或一个镜像，还包含 IP 和 MAC 地址、电源 管理（地址、凭据、类型）以及更为专业的数据等信息 

存储库： 

&ensp;&ensp;保存一个 yum 或 rsync 存储库的镜像信息 
 
## cobbler 各种配置目录说明 

`安装：yum install cobbler dhcp `

`配置文件目录 /etc/cobbler `

&ensp;&ensp;/etc/cobbler/settings : cobbler 主配置文件 

&ensp;&ensp;/etc/cobbler/iso/: iso模板配置文件 

&ensp;&ensp;/etc/cobbler/pxe: pxe模板文件 

&ensp;&ensp;/etc/cobbler/power: 电源配置文件 

&ensp;&ensp;/etc/cobbler/user.conf: web服务授权配置文件 

&ensp;&ensp;/etc/cobbler/users.digest: web访问的用户名密码配置文件 

&ensp;&ensp;/etc/cobbler/dhcp.template : dhcp服务器的的配置末班 

&ensp;&ensp;/etc/cobbler/dnsmasq.template : dns服务器的配置模板 

&ensp;&ensp;/etc/cobbler/tftpd.template : tftp服务的配置模板 

&ensp;&ensp;/etc/cobbler/modules.conf : 模块的配置文件 

`数据目录` 

&ensp;&ensp;/var/lib/cobbler/config/: 用于存放distros，system，profiles 等信息配置文件 

&ensp;&ensp;/var/lib/cobbler/triggers/: 用于存放用户定义的cobbler命令 

&ensp;&ensp;/var/lib/cobbler/kickstart/: 默认存放kickstart文件 

&ensp;&ensp;/var/lib/cobbler/loaders/: 存放各种引导程序 

`镜像目录 `

&ensp;&ensp;/var/www/cobbler/ks_mirror/: 导入的发行版系统的所有数据 

&ensp;&ensp;/var/www/cobbler/images/ : 导入发行版的kernel和initrd镜像用于远程网络启动 

&ensp;&ensp;/var/www/cobbler/repo_mirror/: yum 仓库存储目录 

`日志目录 `

&ensp;&ensp;/var/log/cobbler/installing: 客户端安装日志 

&ensp;&ensp;/var/log/cobbler/cobbler.log : cobbler日志 

## cobbler 命令介绍 


cobbler commands介绍 

cobbler check 核对当前设置是否有问题 

cobbler list 列出所有的cobbler元素 

cobbler report 列出元素的详细信息 

cobbler sync 同步配置到数据目录,更改配置最好都要执行下 

cobbler reposync 同步yum仓库 

cobbler distro 查看导入的发行版系统信息 

cobbler system 查看添加的系统信息 

cobbler profile 查看配置信息 

## cobbler 重要的参数 

/etc/cobbler/settings中重要的参数设置 

default_password_crypted: "$1$gEc7ilpP$pg5iSOj/mlxTxEslhRvyp/"  

manage_dhcp：1 

manage_tftpd：1 

pxe_just_once：1 

next_server：< tftp服务器的 IP 地址> 

server：<cobbler服务器的 IP 地址> 

## cobbler 环境检查 

## 执行Cobbler check命令会报如下异常 

1 : The ‘server’ field in /etc/cobbler/settings must be set to something other than localhost, or kickstarting features will not work. This should be a resolvable hostname or IP for the boot server as reachable by all machines that will use it. (设置cobbler服务器)

2 : For PXE to be functional, the ‘next_server’ field in /etc/cobbler/settings must be set to something other than 127.0.0.1, and should match the IP of the boot server on the PXE network. 

3 : some network boot-loaders are missing from /var/lib/cobbler/loaders, you may run ‘cobbler get-loaders’ to download them, or, if you only want to handle x86/x86_64 netbooting, you may ensure that you have installed a recent version of the syslinux package installed and can ignore this message entirely. Files in this directory, should you want to support all architectures, should include pxelinux.0, menu.c32, elilo.efi, and yaboot. The ‘cobbler get-loaders’ command is the easiest way to resolve these requirements. 

4 : change ‘disable’ to ‘no’ in /etc/xinetd.d/rsync 

5 : comment ‘dists’ on /etc/debmirror.conf for proper debian support 

6 : comment ‘arches’ on /etc/debmirror.conf for proper debian support 

7 : The default password used by the sample templates for newly installed machines (default_password_crypted in /etc/cobbler/settings) is still set to ‘cobbler’ and should be changed, try: “openssl passwd -1 -salt ‘random-phrase-here’ ‘your-password-here’” to generate new one 

8 : fencing tools were not found, and are required to use the (optional) power management features. install cman or fence-agents to use them 

## cobbler 报错解决 


执行Cobbler check报错解决方式 

&ensp;&ensp;修改/etc/cobbler/settings文件中的server参数的值为提供cobbler服务的主机相 应的IP地址或主机名

&ensp;&ensp;修改/etc/cobbler/settings文件中的next_server参数的值为提供PXE服务的主机 相应的IP地址 

&ensp;&ensp;如果当前节点可以访问互联网，执行“cobbler get-loaders”命令即可；否则， 需要安装syslinux程序包，而后复制/usr/share/syslinux/{pxelinux.0,memu.c32} 等文件至/var/lib/cobbler/loaders/目录中 

&ensp;&ensp;执行“chkconfig rsync on”命令即可 

&ensp;&ensp;执行“openssl passwd -1 生成密码，并用其替换/etc/cobbler/settings文件中 default_password_crypted参数的值 
 
## cobbler 相关管理 

下载启动菜单：      

&ensp;&ensp;联网：cobbler get-loaders     

&ensp;&ensp;不联网：cp /usr/share/syslinux/{pxelinux.0,menu.c32}  /var/lib/tftpboot 

管理distro      

&ensp;&ensp;cobbler import --name=centos-7.5-x86_64 --path=/media/cdrom  --arch=x86_64 

管理profile  

&ensp;&ensp;cobbler profile add --name=centos-7.5-x86_64-basic  --distro=centos-7.5-x86_64 --kickstart= /var/lib/cobbler/kickstarts/centos7_x86_64.cfg 

## cobbler 命令 


查看profiles 

&ensp;&ensp;cobbler profile list 

查看引导文件 

&ensp;&ensp;cat  /var/lib/tftpboot/pxelinux.cfg/default 

同步cobbler配置 

&ensp;&ensp;cobbler sync 

多系统引导方案 

&ensp;&ensp;cobbler import --name=CentOS-7-x86_64 --path=/media/cdrom 

&ensp;&ensp;cobbler distro list 

&ensp;&ensp;cobbler profile list 

&ensp;&ensp;cobbler sync  
 
## cobbler 实现步骤 


安装包，并设置服务 

检查配置 

根据上面提示修改配置 

下载启动相关文件菜单 

配置DHCP服务 

分别导入centos的安装源,并查看 

准备kickstart文件并导入cobbler 

测试 

## cobbler的web管理实现 

cobbler-web  

&ensp;&ensp;提供cobbler的基于web管理界面，epel源          

&ensp;&ensp;yum install cobbler-web  

认证方式 

&ensp;&ensp;认证方法配置文件：

&ensp;&ensp;/etc/cobbler/modules.conf 

&ensp;&ensp;支持多种认证方法：  

&ensp;&ensp;authn_configfile  

&ensp;&ensp;authn_pam   

## cobbler的web管理实现 

使用authn_configfile模块认证cobbler_web用户   

vim /etc/cobbler/modules.conf    

[authentication]   

module=authn_configfile 

创建其认证文件/etc/cobbler/users.digest，并添加所需的用户   htdigest -c /etc/cobbler/users.digest Cobbler admin    注意:添加第一个用户时,使用“-c”选项，后续添加其他用户时不要再使 用，cobbler_web的realm只能为Cobbler 

使用authn_pam模块认证cobbler_web用户  

vim /etc/cobbler/modules.conf    

[authentication]     

module = authn_pam 

创建cobbler用户：useradd cobbler 

vim  /etc/cobbler/users.conf    

[admins]  admin = "cobbler“ 

Web访问cobbler 

重启cobblerd服务 

通过https://cobblerserver/cobbler_web访问 

`范例：使用cobbler实现自动化安装`
```bash
实验前提：1为了确保次实验与其他实验不冲突，保证实验主机的干净
         2cobbler是epel源，所以使得虚拟机可以连接互联网
         3为了实验避免防火墙和selinux的干扰，关闭防火墙和selinux

第一步：安装cobbler程序
[root@centos7 ~]# yum install cobbler dhcp -y
 httpd               x86_64  2.4.6-80.el7.centos.1      
 httpd-tools         x86_64  2.4.6-80.el7.centos.1           188 k
 syslinux            x86_64  4.05-13.el7                base     989 k
 tftp-server         x86_64  5.2-22.el7                 base      47 k
顺便安装http tftp syslinux程序 需自己手动安装dhcp程序
安装好顺表设置所以的服务都加入开机启动项中

第二步：启动服务并查看未实现的清单
[root@centos7 ~]# systemctl restart cobblerd
[root@centos7 ~]# systemctl restart tftp httpd

[root@centos7 ~]# cobbler check
第一项：在/etc/cobbler/settings 文件中设置cobbler服务器(配置文件的354行)
      [root@centos7 ~]# vim /etc/cobbler/settings
                        server: 192.168.52.157（最好为企业内网ip）
       重启服务加载配置文件，表示已经根据检查已经改好
       [root@centos7 ~]# systemctl restart cobblerd
第二项：'next_server' （指定tftp服务器地址配置文件的272行）
       [root@centos7 ~]# vim /etc/cobbler/settings
        next_server: 192.168.52.157 （也指向自己）
第四项：启动tftp服务器
第四项：加载cobbler必要的文件 运行run 'cobbler get-loaders'自动加载，此时tftp工作目录已经有了对应的目录
        [root@centos7 ~]# tree /var/lib/tftpboot/
         /var/lib/tftpboot/
          ├── boot
          ├── etc
          ├── grub
          ├── images
          ├── images2
          ├── ppc
          ├── pxelinux.cfg
          └── s390x
        [root@centos7 ~]# cobbler get-loaders 执行结束会去互联网下载对应的配置文件，默认放置在/var/lib/cobbler/loaders
        [root@centos7 ~]# ls /var/lib/cobbler/loaders/
            COPYING.elilo     COPYING.yaboot  grub-x86_64.efi  menu.c32    README
            COPYING.syslinux  elilo-ia64.efi  grub-x86.efi     pxelinux.0  yaboot

第八项：修改默认的口令在配置文件(default_password_crypted in /etc/cobbler/settings)101行
        [root@centos7 ~]# openssl passwd -1
                Password: 
                Verifying - Password: 
                  $1$sYDE7/Ni$QRLD6gtC3YDV/OALRxvLB0
        [root@centos7 ~]# vim /etc/cobbler/settings
        default_password_crypted: "$1$sYDE7/Ni$QRLD6gtC3YDV/OALRxvLB0"

这一项提示信息中没有建议修改：dhcp自动生成模板（改成1，表示dhcp的配置文件由cobbler自动生成）
        [root@centos7 ~]# vim /etc/cobbler/settings
          manage_dhcp: 1
        [root@centos7 ~]# systemctl restart cobblerd

最后提示项：同步信息将第四项生成的信息系统自动同步到tftp的工作目录下
        [root@centos7 ~]# cobbler sync
        [root@centos7 ~]# tree /var/lib/tftpboot/
           /var/lib/tftpboot/
           ├── boot
           │   └── grub
           │       └── menu.lst
           ├── etc
           ├── grub
           │   ├── efidefault
           │   ├── grub-x86_64.efi
           │   ├── grub-x86.efi
           │   └── images -> ../images
           ├── images
           ├── images2
           ├── memdisk
           ├── menu.c32
           ├── ppc
           ├── pxelinux.0
           ├── pxelinux.cfg
           │   └── default
           ├── s390x
           │   └── profile_list
           └── yaboot
           10 directories, 10 files

第三步：准备DHCP服务，修改dhcp在cobbler模板文件（此时服务器上的dhcp配置文件也已经生成，但不不规范，直接去修稿dhcp模板文件，间接重新生成dhcp配置文件）
[root@centos7 ~]# rpm -ql cobbler | grep dhcp
/etc/cobbler/dhcp.template
[root@centos7 ~]# vim /etc/cobbler/dhcp.template
subnet 192.168.52.0 netmask 255.255.255.0 {
     option subnet-mask         255.255.255.0;
     range dynamic-bootp        192.168.52.100 192.168.52.254;
     default-lease-time         21600;
     max-lease-time             43200;
     next-server                $next_server;

根据模板重新同步
[root@centos7 ~]# cobbler sync
此时dhcp端口已经打开

第四步：导入安装光盘生成最小化安装界面（此时已经生成了本地安装）
[root@centos7 ~]# cobbler import --help  查看使用帮助
--path 指定光盘路径
--name 指定yum源名称
--arch 指定型号
不值当应答文件默认生成系统最小化安装
[root@centos7 ~]# cobbler import --path=/misc/cd/ --name=centos7.5-x88_64 --arch=x86_64


默认cp放置路径
[root@centos7 ~]# du -sh /var/www
842M	/var/www
[root@centos7 ~]# du -sh /var/www
887M	/var/www

[root@centos7 ~]# cd /var/www/cobbler/ks_mirror/centos7.5-x88_64-x86_64/
[root@centos7 centos7.5-x88_64-x86_64]# ls
CentOS_BuildTag  GPL       LiveOS    RPM-GPG-KEY-CentOS-7
EFI              images    Packages  RPM-GPG-KEY-CentOS-Testing-7
EULA             isolinux  repodata  TRANS.TBL

导入成功再次同步
[root@centos7 ~]# cobbler sync


[root@centos7 ~]# cat /var/lib/tftpboot/pxelinux.cfg/default 
DEFAULT menu
PROMPT 0
MENU TITLE Cobbler | http://cobbler.github.io/
TIMEOUT 200
TOTALTIMEOUT 6000
ONTIMEOUT local

LABEL local
        MENU LABEL (local)
        MENU DEFAULT
        LOCALBOOT -1
LABEL centos7.5-x88_64-x86_64
        kernel /images/centos7.5-x88_64-x86_64/vmlinuz
        MENU LABEL centos7.5-x88_64-x86_64
        append initrd=/images/centos7.5-x88_64-x86_64/initrd.img ksdevice=bootif lang=  kssendmac text  ks=http://192.168.52.157/cblr/svc/op/ks/profile/centos7.5-x88_64-x86_64
        ipappend 2
MENU end

(如果想实现centos7和centos6 都自动化，可以再添加一块centos6的iso文件，进行挂载，再次进行导入安装光盘即可)
```
测试：
![cobbler自动化安装centos7](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/cobbler%20centos7%20%E8%87%AA%E5%8A%A8%E5%8C%96%E5%AE%89%E8%A3%85.png)


基于上面实验进一步实现将自己原定义好的系统应答也基于cobbler实现自动化安装
```bash
查看cobbler自动安装的系统（系统）
[root@centos7 ~]# cobbler distro list
   centos7.5-x88_64-x86_64

操作系统对应的安装方法（启动菜单项）
[root@centos7 ~]# cobbler profile list
   centos7.5-x88_64-x86_64

将原有的应答文件放置在/var/lib/cobbler/kickstarts/
但是应答文件中的url需要修改（url --url=$tree）

新定义启动菜单和系统版本关联应答文件（映射关系）
--name 开机选项菜单
--distro 版本
--kickstart 应答文件
[root@centos7 ~]# cobbler profilr add --name=centos7xin --distro=centos7-x86_64 --kickstart=/var/lib/cobbler/kickstarts/ks.cfg

启动查看验证会多一个菜单项

实际是系统只有一个 ，profile为对应的安装方法

删除安装方法：
cobbler profile remove --name=*****

修改安装方法的名字
cobbler profile remane --name=laomingzi --newname=xinmingzi
```
## cobbler 基于web
 基于命令行的cobbler用起来有点困恼，可以安装一个cobbler的软件实现图形化
 ```bash
[root@centos7 ~]# yum install cobbler-web

配置文件
[root@centos7 ~]# rpm -ql cobbler-web
/etc/httpd/conf.d/cobbler_web.conf 

需要重启http服务 ：多一个443端口，走https加密
[root@centos7 ~]# ss -ntl
LISTEN     0      128      :::443                  :::*   
 ```
可以访问该网页
![cobbler图形化](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/cobbler%20%E5%9B%BE%E5%BD%A2%E5%8C%96%E7%95%8C%E9%9D%A2.png)

系统本身带的账号：cobbler

自带的密码：cobbler

系统自带的登陆验证方法

vim /etc/cobbler/modules.conf

基于安全，自己添加cobbler用户
```bash
[root@centos7 ~]# cat /etc/cobbler/users.digest 
cobbler:Cobbler:a2d6bae81669d707b72c0bd9806e01f3
[root@centos7 ~]# htdigest /etc/cobbler/users.digest Cobbler daizhe
Adding user daizhe in realm Cobbler
New password: 
Re-type new password: 
[root@centos7 ~]# cat /etc/cobbler/users.digest 
cobbler:Cobbler:a2d6bae81669d707b72c0bd9806e01f3
daizhe:Cobbler:9648a6fc30655f67f0be464aa05daca4
```
