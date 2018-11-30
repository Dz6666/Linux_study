---
title: pam模块
tags: linux服务
categories: linux服务
---
## PAM认证机制 

PAM:Pluggable Authentication Modules 

&ensp;&ensp;认证库：文本文件，MySQL，NIS，LDAP等 

&ensp;&ensp;Sun公司于1995 年开发的一种与认证相关的通用框架机制 

&ensp;&ensp;PAM 是关注如何为服务验证用户的 API，通过提供一些动态链接库和一套统 一的API，将系统提供的服务和该服务的认证方式分开 

&ensp;&ensp;使得系统管理员可以灵活地根据需要给不同的服务配置不同的认证方式而无 需更改服务程序 

&ensp;&ensp;一种认证框架，自身不做认证

&ensp;&ensp;它提供了对所有服务进行认证的中央机制，适用于login，远程登录 （telnet,rlogin,fsh,ftp,点对点协议（PPP）），su等应用程序中。系统管理员 通过PAM配置文件来制定不同应用程序的不同认证策略；应用程序开发者通过 在服务程序中使用PAM API(pam_xxxx( ))来实现对认证方法的调用；而PAM服 务模块的开发者则利用PAM SPI来编写模块（主要调用函数pam_sm_xxxx( )供 PAM接口库调用，将不同的认证机制加入到系统中；PAM接口库（libpam）则 读取配置文件，将应用程序和相应的PAM服务模块联系起来 

## PAM架构
![pam架构](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/PAM%E6%9E%B6%E6%9E%84.png)

`PAM相关文件 `

&ensp;&ensp;`模块文件目录：/lib64/security/*.so  `

&ensp;&ensp;`环境相关的设置：/etc/security/ `复杂程序的配置文件

&ensp;&ensp;主配置文件:/etc/pam.conf，默认不存在 

&ensp;&ensp;`为每种应用模块提供一个专用的配置文件：/etc/pam.d/APP_NAME `简单程序的配置文件

&ensp;&ensp;注意：如/etc/pam.d存在，/etc/pam.conf将失效 

## pam认证原理 

`PAM认证一般遵循这样的顺序：Service(服务)→PAM(配置文件)→pam_*.so `

`PAM认证首先要确定那一项服务，然后加载相应的PAM的配置文件(位于 /etc/pam.d下)，最后调用认证文件(位于/lib64/security下)进行安全认证 `
![pam认证原理](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/PAM%E8%AE%A4%E8%AF%81%E5%8E%9F%E7%90%86.png)

`PAM认证过程：` 

1.使用者执行/usr/bin/passwd 程序，并输入密码 

2.passwd开始调用PAM模块，PAM模块会搜寻passwd程序的PAM相关设置文 件，这个设置文件一般是在/etc/pam.d/里边的与程序同名的文件，即PAM会 搜寻/etc/pam.d/passwd此设置文件 

3.经由/etc/pam.d/passwd设定文件的数据，取用PAM所提供的相关模块来进 行验证 

4.将验证结果回传给passwd这个程序，而passwd这个程序会根据PAM回传的 结果决定下一个动作（重新输入密码或者通过验证） 

通用配置文件/etc/pam.conf格式   所有软件调用pam模块的信息

&ensp;&ensp;application  type  control  module-path  arguments 

`专用配置文件/etc/pam.d/* 列格式  ` 只用想单个程序调用pam的情况

&ensp;&ensp;`type control  module-path  arguments `  
&ensp;&ensp;`类型、控制、模块名、模块的参数选项`

说明： 

&ensp;&ensp;服务名（application） 

&ensp;&ensp;&ensp;&ensp;telnet、login、ftp等，服务名字“OTHER”代表所有没有在该文件中明确配 置的其它服务 

&ensp;&ensp;模块类型（module-type） 

&ensp;&ensp;control  PAM库该如何处理与该服务相关的PAM模块的成功或失败情况 

&ensp;&ensp;module-path 用来指明本模块对应的程序文件的路径名 

&ensp;&ensp;Arguments  用来传递给该模块的参数 

`模块类型（module-type）` 

&ensp;&ensp;Auth 账号的认证和授权 

&ensp;&ensp;Account 与账号管理相关的非认证类的功能，如：用来限制/允许用户对某 个服务的访问时间，当前有效的系统资源(最多可以有多少个用户)，限制用 户的位置(例如：root用户只能从控制台登录) （验证账户登陆的有效性）

&ensp;&ensp;Password 用户修改密码时密码复杂度检查机制等功能 （主要针对普通用户口令复杂度检查）

&ensp;&ensp;Session 用户获取到服务之前或使用服务完成之后需要进行一些附加的操作， 

&ensp;&ensp;&ensp;&ensp;如：记录打开/关闭数据的信息，监视目录等 

&ensp;&ensp;-type 表示因为缺失而不能加载的模块将不记录到系统日志,对于那些不总是 安装在系统上的模块有用 

`Control:  控制`  

&ensp;&ensp;PAM库如何处理与该服务相关的PAM模块成功或失败情况 

两种方式实现：  

&ensp;&ensp;简单和复杂（用不到） 

&ensp;&ensp;`简单方式实现：一个关健词实现 `

&ensp;&ensp;&ensp;&ensp;`required ：`一票否决，表示本模块必须返回成功才能通过认证，但是如果该 模块返回失败，失败结果也不会立即通知用户，而是要等到同一type中的所 有模块全部执行完毕再将失败结果返回给应用程序。即为必要条件 

&ensp;&ensp;&ensp;&ensp;`requisite ：`一票否决，该模块必须返回成功才能通过认证，但是一旦该模块返 回失败，将不再执行同一type内的任何模块，而是直接将控制权返回给应用程 序。是一个必要条件 

&ensp;&ensp;&ensp;&ensp;`sufficient ：`一票通过，表明本模块返回成功则通过身份认证的要求，不必再执 行同一type内的其它模块，但如果本模块返回失败可忽略，即为充分条件 

&ensp;&ensp;&ensp;&ensp;`optional ：`表明本模块是可选的，它的成功与否不会对身份认证起关键作用， 其返回值一般被忽略 

&ensp;&ensp;&ensp;&ensp;`include：` 调用其他的配置文件中定义的配置信息 

&ensp;&ensp;复杂详细实现(用不到)：使用一个或多个“status=action” 

&ensp;&ensp;&ensp;&ensp;[status1=action1 status2=action …]  

&ensp;&ensp;&ensp;&ensp;Status:检查结果的返回状态  

&ensp;&ensp;&ensp;&ensp;Action:采取行为 ok，done，die，bad，ignore，reset 

&ensp;&ensp;&ensp;&ensp;ok  模块通过，继续检查 

&ensp;&ensp;&ensp;&ensp;done 模块通过，返回最后结果给应用 

&ensp;&ensp;&ensp;&ensp;bad 结果失败，继续检查 

&ensp;&ensp;&ensp;&ensp;die 结果失败，返回失败结果给应用 

&ensp;&ensp;&ensp;&ensp;ignore 结果忽略，不影响最后结果 

&ensp;&ensp;&ensp;&ensp;reset 忽略已经得到的结果 

`module-path: 模块路径` 

&ensp;&ensp;`相对路径：  ` 

&ensp;&ensp;&ensp;&ensp;`/lib64/security目录下的模块可使用相对路径   `

&ensp;&ensp;&ensp;&ensp;如：pam_shells.so、pam_limits.so 

&ensp;&ensp;绝对路径： 

&ensp;&ensp;模块通过读取配置文件完成用户对系统资源的使用控制  

&ensp;&ensp;&ensp;&ensp;/etc/security/*.conf 

&ensp;&ensp;注意：修改PAM配置文件将马上生效 

&ensp;&ensp;建议：编辑pam规则时，保持至少打开一个root会话，以防止root身份验证错误 

`Arguments  用来传递给该模块的参数 `

## pam文档说明 

/user/share/doc/pam-* 

rpm -qd pam 

man –k pam_ 

man 模块名 如man rootok 

《The Linux-PAM System  Administrators' Guide》 
```bash
[root@centos7 pam.d]# rpm -q pam
pam-1.1.8-22.el7.x86_64
[root@centos7 pam.d]# rpm -qi pam
Name        : pam
Version     : 1.1.8
Release     : 22.el7
Architecture: x86_64
Install Date: Sun 14 Oct 2018 02:35:19 PM CST
Group       : System Environment/Base
Size        : 2630324
License     : BSD and GPLv2+
Signature   : RSA/SHA256, Wed 25 Apr 2018 07:33:49 PM CST, Key ID 24c6a8a7f4a80eb5
Source RPM  : pam-1.1.8-22.el7.src.rpm
Build Date  : Wed 11 Apr 2018 11:22:15 AM CST
Build Host  : x86-01.bsys.centos.org
Relocations : (not relocatable)
Packager    : CentOS BuildSystem <http://bugs.centos.org>
Vendor      : CentOS
URL         : http://www.linux-pam.org/
Summary     : An extensible library which provides authentication for applications
Description :
PAM (Pluggable Authentication Modules) is a system security tool that
allows system administrators to set authentication policy without
having to recompile programs that handle authentication.
```
查看给个模块的帮助手册
```bash
[root@centos7 pam.d]# man -k pam_
group.conf (5)       - configuration file for the pam_group module
limits.conf (5)      - configuration file for the pam_limits module
pam_access (8)       - PAM module for logdaemon style login access c...
pam_console (8)      - determine user owning the system console
pam_console_apply (8) - set or revoke permissions for users at t
........
```
## PAM模块示例

`模块：pam_shells  `

功能：检查有效shell 

man pam_shells 

示例：不允许使用/bin/csh的用户本地登录 

&ensp;&ensp;vim /etc/pam.d/login     

&ensp;&ensp;&ensp;&ensp;auth required pam_shells.so  

&ensp;&ensp;vim /etc/shells    

&ensp;&ensp;&ensp;&ensp;去掉 /bin/csh 

&ensp;&ensp;useradd –s /bin/csh testuser 

&ensp;&ensp;testuser将不可登录 

&ensp;&ensp;tail /var/log/secure 

范例：pam_shells模块
```bash
查看哪些服务调用了shells PAM模块
[root@centos7 ~]# cd /etc/pam.d/
[root@centos7 pam.d]# grep pam_shells *
vmtoolsd:auth       required         pam_shells.so
vmtoolsd:account    required         pam_shells.so

实现限制特定的shell类型才可以登陆

建立一个用户，修改shell类型
[root@centos7 pam.d]# useradd username1
[root@centos7 pam.d]# chsh -s /bin/csh username1
Changing shell for username1.
Shell changed.
[root@centos7 pam.d]# getent passwd username1
username1:x:1002:1002::/home/username1:/bin/csh
将shell文件中的此shell类型注释掉
[root@centos7 pam.d]# vim /etc/shells 
#/bin/csh
使得su程序执行调用pam_shells.so模块（/etc/security）限制登陆的用户，是否符合shell类型
[root@centos7 pam.d]# vim su
#%PAM-1.0
auth            required        pam_shells.so
                直接一票否决
[root@centos7 pam.d]# su - username1 
Password: 
su: Authentication failure

[root@centos7 pam.d]# vim /etc/shells 
/bin/csh
[root@centos7 pam.d]# su - username1 
Last failed login: Sun Nov 18 10:34:53 CST 2018 on pts/1
There was 1 failed login attempt since the last successful login.
[username1@centos7 ~]$ 
````
范例：使得ssh程序也调用shell.so模块，实现显示shell类型的登陆直接否决
```bash
[root@centos7 pam.d]# getent passwd username1
username1:x:1002:1002::/home/username1:/bin/csh

[root@centos7 pam.d]# cat /etc/shells 
#/bin/csh

[root@centos7 pam.d]# cat sshd 
#%PAM-1.0
auth	   requisite	shells.so

[root@centos6 etc]# ssh username1@192.168.131.180
hahahahahahahahh
username1@192.168.131.180's password: 
Permission denied, please try again.
```
`模块：pam_securetty.so `

功能：只允许root用户在/etc/securetty列出的安全终端上登陆 

示例：允许root在telnet登陆 

vi /etc/pam.d/remote 

#auth required pam_securetty.so #将这一行加上注释 
 
或者/etc/securetty文件中加入  

pts/0,pts/1…pts/n 

范例：pam_securetty模块
```bash
查看哪些程序调用了此模块
[root@centos7 pam.d]# grep pam_securetty *
login:auth [user_unknown=ignore success=ok ignore=ignore default=bad] pam_securetty.so
（所有远程登陆）remote:auth       required     pam_securetty.so
```
`模块：pam_nologin.so `

功能： 

&ensp;&ensp;如果/etc/nologin文件存在,将导致非root用户不能登陆 

&ensp;&ensp;如果用户shell是/sbin/nologin 时，当该用户登陆时，会显示/etc/nologin 文件内容，并拒绝登陆

```bash
[root@centos7 pam.d]# touch /etc/nologin(可以写入信息，可以出现登陆失败的普通用户提示界面)

[root@centos7 pam.d]# grep pam_nologin *
gdm-autologin:account    required    pam_nologin.so
gdm-fingerprint:account     required      pam_nologin.so
gdm-password:account     required      pam_nologin.so
gdm-pin:account     required      pam_nologin.so
gdm-smartcard:account     required      pam_nologin.so
login:account    required     pam_nologin.so
pluto:account required pam_nologin.so
ppp:account    required	pam_nologin.so
remote:account    required     pam_nologin.so
sshd:account    required     pam_nologin.so

[root@centos6 ~]# ssh daizhe@192.168.131.180
ssh: connect to host 192.168.131.180 port 22: No route to host
[root@centos6 ~]# 
```

`模块：pam_limits.so` 

功能：在用户级别实现对其可使用的资源的限制，例如：可打开的文件数量， 可运行的进程数量，可用内存空间 （限制用户使用的资源）

修改限制的实现方式： 

(1) ulimit命令，立即生效，但无法保存  

&ensp;&ensp;-n   最多的打开的文件描述符个数  

&ensp;&ensp;-u   最大用户进程数  

&ensp;&ensp;-S   使用 soft（软）资源限制  

&ensp;&ensp;-H   使用 hard（硬）资源限制 

(2) 配置文件：/etc/security/limits.conf, /etc/security/limits.d/*.conf 

配置文件：每行一个定义； <domain>        <type>  <item>  <value> 
 
范例：pam_limits.so 模块
```bash
查看哪些程序调用了此模块（用户登陆成功，通讯过程中进行控制使用的资源）
[root@centos7 pam.d]# pwd
/etc/pam.d
[root@centos7 pam.d]# grep pam_limits.so *
fingerprint-auth:session     required      pam_limits.so
fingerprint-auth-ac:session     required      pam_limits.so
password-auth:session     required      pam_limits.so
password-auth-ac:session     required      pam_limits.so
runuser:session		required	pam_limits.so
smartcard-auth:session     required      pam_limits.so
smartcard-auth-ac:session     required      pam_limits.so
sudo:session    required     pam_limits.so
sudo-i:session    required     pam_limits.so
system-auth:session     required      pam_limits.so
system-auth-ac:session     required      pam_limits.so

查看用户的限制
[root@centos7 ~]# ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 7161
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 7161
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```
![pam认证机制](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/PAM%E8%AE%A4%E8%AF%81%E6%9C%BA%E5%88%B6.png)



测试性能
```bash
[root@centos7 ~]# ab -C 100 -n 2000 http://
-C 同时打开100个
-n 最多打开2000个
```
`<domain> 应用于哪些对象 `

&ensp;&ensp;Username 单个用户 

&ensp;&ensp;@group 组内所有用户 

&ensp;&ensp;*  所有用户 

`<type> 限制的类型 `

&ensp;&ensp;Soft 软限制,普通用户自己可以修改 

&ensp;&ensp;Hard 硬限制,由root用户设定，且通过kernel强制生效 

&ensp;&ensp;-  二者同时限定 

`<item> 限制的资源 `

&ensp;&ensp;nofile 所能够同时打开的最大文件数量,默认为1024 

&ensp;&ensp;nproc 所能够同时运行的进程的最大数量,默认为1024 

`<value> 指定具体值 `

示例：pam_limits.so 

限制用户最多打开的文件数和运行进程数 

/etc/pam.d/system-auth   

&ensp;&ensp;session &ensp;&ensp;    required   &ensp;&ensp;   pam_limits.so 

vim /etc/security/limits.conf           a

&ensp;&ensp;pache  –  nofile 10240  用户apache可打开10240个文件  

&ensp;&ensp;student  hard nproc 20 用户student不能运行超过20个进程 

