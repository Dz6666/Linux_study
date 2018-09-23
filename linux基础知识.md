---
title: Linux基础知识
tags: linux
categories: linux
---
![image](https://pic.qqtn.com/up/2018-2/15196972876014951.gif)


## 计算机系统
&ensp;&ensp;计算机系统由`硬件(Hardware)系统`和`软件(Software)系统`两大部分


## linux哲学思想

1.&ensp;`一切皆文件`

&ensp;&ensp;所用的文件，包括设备等在linux当中都被视为文件，这样便于统一管理和定义

2.&ensp;`短小，且目的单一的程序组成`

&ensp;&ensp;程序和可执行文件不要太复杂，这样才能保证了linux内核的高效运行

3.&ensp;`串联多个小程序完成复杂任务`

 &ensp;&ensp;复杂的任务可以通过连接多个简单的程序综合实现复杂的任务。

4.&ensp;`通过文本文件保存软件的配置信息`

 &ensp;&ensp;linux所有的配置文件都存放在文本配置文件当中，无论什么配置修改都只需修改其配置文件

## 显示当前系统使用的SHELL
&ensp;&ensp;`shell`被称为命令解释器,是一种高级语言，他可以把我们输入的命令转换为机器可以识别的语言，（0和1）也可用`cat`命令查看当前系统装了哪些shell&ensp;放置文件`/bin/bash`

<!--more-->
```bash
[root@centos7 ~]#ehco $SHELL
[root@centos7 ~]#/bin/bash
[root@centos7 ~]#cat /etc/shell
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
```
## 命令提示符(id -u)(PS1)
&ensp;&ensp;"#" 为管理员  "$" 为普通用户
也可输入命令来查看，显示为“0” 就是管理员，不是“0”的皆为普通用户
```bash
[root@centons6 ~]#
[lihf@centons6 ~]$
[root@centos7 ~]#id -u
0
```
&ensp;&ensp;命令提示符可以通过修改PS1变量来改变字体颜色、亮度等，例：
```bash
[root@centos7 ~]#PS1="\[\e[1;5;41;33m\][\u@\h \W]\\$\[\e[0m\]"
```
&ensp;&ensp;数字1为显示高亮；数字5为为闪烁；数字41为填充颜色；数字33为字体颜色 
\u显示当前用户；\h显示主机名简称；\w显示当前工作目录；\W显示当前工作目录基名
\t 显示24小时时间格式；\T显示12小时时间格式；\!显示命令历史数；\# 开机后命令历史
若想保存下来可在`/etc/profile.d/`  下建立一个 `.sh`后缀的文件把修改过的PS1变量写进去保存，可用`nano`修改
## 命令的分类：(type)(enable)
&ensp;&ensp;在linux中有两种命令，一种为内部命令，一种为外部命令：
内部命令：由shell自带的,可用`type`命令查看, 显示`shell builtin`为内部命令，直接显示出路径的为外部命令。
```bash
[root@centos7 ~]#type cd
cd is a shell builtin
[root@centos7 ~]#type nano
nano is /usr/bin/nano
```

&ensp;&ensp;内部命令可用`enable`来禁用/开启内部命令
```bash
[root@centos7 ~]#enable -n cd (禁用pwd命令)
[root@centos7 ~]#enable cd (启用命令)
[root@centos7 ~]#enable -n (查看所有禁用的命令)
[root@centos7 ~]#enable (查看内部命令)
```

&ensp;&ensp;可用命令`echo $PASH`来查看外部命令
```bash
[root@centos7 ~]#echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```
## 别名： (alias)
&ensp;&ensp;别名可以把很长的一串命令用很短的几个单词来表达，想当于定义一个变量。
查看shell里可以使用的别名命令：
```bash
[root@centos7 ~]#alias 
alias cp='cp -i'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```
&ensp;&ensp;定义别名格式为：alias 名字='长命令'
```bash
[root@centos7 ~]#alias cdpk="cd /run/media/root/CentOS\ 7\ x86_64/Packages/"
[root@centos7 ~]#cdpk
[root@centos7 Packages]#
```
&ensp;&ensp;取消别名
```
[root@centos7 ~]#unalias cdpk
```
&ensp;&ensp;只对当前登录有效，退出不保存，若想保存可在`/.bashrc`下添加(只对当前用户)，全局保存在`/etc/bashrc`下添加。
```bash
[root@centos7 ~]#nano .bashrc
.bashrc

 User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```
## 显示变量：转意符、常见的变量
echo "$VAR_NAME” 变量会替换，弱引用，变量被替换 聪明
echo '$VAR_NAME’ 变量不会，强引用，变量不被替换  傻
echo $($VAR_NAME )用于一个命令去调用另一个变量 可以识别变量和命令  最聪明    `ls -l  $(echo #PATH)`

&ensp;&ensp;`echo $UID`        表示当前用户的用户名，该变量的值与”id-u”命令的结果一致

&ensp;&ensp; `echo $SHEL`       表示当前用户的登录Shell，值与”passwd”文件中的Shell字段一致

&ensp;&ensp;`echo $HOME`   表示当前用户的登录目录（宿主目录），值与”psaawd”文件中home字段一致

&ensp;&ensp;`echo $PWD`    表示用户当前所在的目录，值与pwd命令的结果一致

&ensp;&ensp;`echo $PATH`    表示当前用户的命令搜索路径，即用户不指定全路径名执行命令时，Shell程
序将在哪些目录以及按照何种顺序进行命令的搜索

&ensp;&ensp;环境变量配置文件   `/etc/bashrc`

&ensp;&ensp;系统中用户登陆是默认加载的变量
`/etc/profile`

 &ensp;&ensp;用户个人环境变量 为隐藏文件位于属主目下`/home/~/.bash_profile`&ensp; `/home/~/.bashrc`

 
   





## hash 缓存
&ensp;&ensp;系统初始hash表为空，当外部命令执行时，默认会从PATH路径下寻找该命令，找到后会将这条命令的路径记录到hash表中，当再次使用该命令时，shell解释器首先会查看hash表，存在的话就执行，如果不存在，将会去PATH路径下寻找。利用hash缓存表可大大提高命令的调用速率。
`hash -d +name` 清除缓存
`hash -r` 清除所有的缓存
查看`hash`表已执行过并保存的命令
```bash
[root@centos7 ~]#hash
hits    command
1    /usr/bin/nano
```

## 命令优先级：
&ensp;&ensp;alias - 内部 — hash - $PATH - 报错（type  -a  commond 查看内外部路径）
## 命令格式
&ensp;&ensp;COMMAND（命令） [OPTIONS...]（选项 -a -t） [ARGUMENTS...](对应参数)
取消和结束命令执行：Ctrl+c，Ctrl+d
多个选项以及多参数和命令之间使用空白字符分隔 `;`
```bash
[root@centos7 ~]#ls;pwd
anaconda-ks.cfg  Desktop  Documents  Downloads  initial-setup-ks.cfg  Music  Pictures  Public  Templates  Videos
/root
```


## 日期和时间
系统时间：linux安装时默认的时间
硬件时间：主机BIOS里的时间
显示当前linux里的系统时间/硬件时间
```bash
[root@centos7 ~]#date 
Thu Sep 20 21:47:24 CST 2018
[root@centos7 ~]#clock
Thu 20 Sep 2018 11:30:33 PM CST  -0.711035 seconds
```
硬件和系统时间都不正确可自己手动修改,命令格式为 `date 月日时分年.秒 `
```bash
[root@centos7 ~]#date 092015402018.30
Thu Sep 20 15:40:30 CST 2018
```
如果系统或者硬件时间某一个不正常，可用命令同步。
`clock -w`以系统时间为准 覆盖硬件时间
`clock -s`以硬件时间为准 覆盖系统时间
查看当月的日历：
```bash
[root@centos7 ~]#cal
   September 2018   
Su Mo Tu We Th Fr Sa
                   1
 2  3  4  5  6  7  8
 9 10 11 12 13 14 15
16 17 18 19 20 21 22
23 24 25 26 27 28 29
30
```

## 关机/重启命令
&ensp;&ensp;`poweroff` 关机断电 ；`halt` 关机但不断电 ；`reboot` 重启 
shutdown + 时间 +h 指定时间之后关机 shutdown -r 重启 shutdown -c 取消关机  不指定时间默认是1分钟后关机；shutdown -now 立刻关机。

## whoami命令
&ensp;&ensp;whoami  查看当前是谁在登录操作系统，显示用户名；
who am i 可以查看哪个用户登录的，什么模式登录的
who  查看有多少人在登录
```bash
[root@centos7 ~]#whoami
root
[root@centos7 ~]#who am i
root     pts/2        2018-09-20 16:14 (192.168.100.1)
[root@centos7 ~]#who
root     :0           2018-09-20 16:55 (:0)
root     pts/1        2018-09-20 20:32 (192.168.100.1)
root     pts/0        2018-09-20 16:07 (:0)
root     pts/2        2018-09-20 16:14 (192.168.100.1)
```
## screen命令：
&ensp;&ensp;screen命令可用在退出远程终端或者断网之后仍然可以继续运行一个耗时很长程序，不会因为退出终端或者断网而停止运行。
输入命令`screen` 建立，然后运行一个耗时很长的任务。（实验后期补上）

&ensp;&ensp;`screen`也可以远程帮助别人，两人在同一个网络，假如一个人需要帮助，寻求帮助的人可以建立一个screen会话，另一人加入进去。
`screen -S +name` 创建一个会话（会话名字为linux）`screen -x linux` 即可加入会话。
自己临时单方面的退出快捷键`ctrl+a，b`，`exit` 关闭 查看已建立的会话 `screen-ls`
-------
## &ensp;&ensp;有没有领会linux基础知识______点个星星鼓励一下吧！
![学习使你快乐](https://pic.qqtn.com/up/2018-2/15196972876229460.gif)