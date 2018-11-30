---
title:脚本进阶字符串处理和expect
tags: linux
categories: linux
---
## 字符串切片

&ensp;&ensp;echo ${#var}:返回字符串变量var的长度

&ensp;&ensp;echo ${var:offset}:返回字符串变量var中从第offset个字符后（不包括第offset个字符）的字 符开始，到最后的部分，offset的取值在0 到 ${#var}-1 之间(bash4.2后，允许为负值) 

&ensp;&ensp;echo ${var:offset:number}：返回字符串变量var中从第offset个字符后（不包括第offset个 字符）的字符开始，长度为number的部分 

&ensp;&ensp;echo ${var: -length}：取字符串的最右侧几个字符 注意：冒号后必须有一空白字符 

&ensp;&ensp;echo ${var:offset:-length}：从最左侧跳过offset字符，一直向右取到距离最右侧lengh个字 符之前的内容 

&ensp;&ensp;echo ${var:  -length:-offset}：先从最右侧向左取到length个字符开始，再向右取到距离最 右侧offset个字符之间的内容 

注意：-length前空格

## 字符串处理

### `基于模式取子串 `

`${var#*word}：`其中word可以是指定的任意字符 
&ensp;&ensp;功能：自左而右，查找var变量所存储的字符串中，第一次出现的word, 删 除字符串开头至第一次出现word字符串（含）之间的所有字符 

```bash
范例：
${变量#*关键字}
自左至右，删除第一个匹配到的字符
[root@centos6 ~]# rootinfo=`getent passwd root`
[root@centos6 ~]# echo $rootinfo
root:x:0:0:root:/root:/bin/bash
[root@centos6 ~]# echo ${rootinfo#*root}
:x:0:0:root:/root:/bin/bash

```

`${var##*word}：`同上，贪婪模式，不同的是，删除的是字符串开头至最后 一次由word指定的字符之间的所有内容 

```bash
范例：
${变量##*关键字}
自左至右贪婪模式删除匹配到的字符
[root@centos6 ~]# echo $rootinfo
root:x:0:0:root:/root:/bin/bash
[root@centos6 ~]# echo ${rootinfo##*root}
:/bin/bash
```

示例： 

file=“var/log/messages” 

${file#*/}: log/messages 

${file##*/}: messages


`${var%word*}：`其中word可以是指定的任意字符 &ensp;&ensp;功能：自右而左，查找var变量所存储的字符串中，第一次出现的word, 删 除字符串最后一个字符向左至第一次出现word字符串（含）之间的所有字符 

&ensp;&ensp;file="/var/log/messages" 

&ensp;&ensp;${file%/*}:  /var/log 
```bash
范例：
${变量%关键字*}：
自右向左，删除第一个匹配关键字
[root@centos6 ~]# echo $rootinfo
root:x:0:0:root:/root:/bin/bash
[root@centos6 ~]# echo ${rootinfo%root*}
root:x:0:0:root:/

````
`${var%%word*}：`同上，贪婪模式，只不过删除字符串最右侧的字符向左至最后一次出现 word字符之间的所有字符 

```bash
范例：
${变量%%关键字*}
自友至左贪婪模式删除匹配到的字符
[root@centos6 ~]# echo $rootinfo
root:x:0:0:root:/root:/bin/bash
[root@centos6 ~]# echo ${rootinfo%%root*}


```
示例： 

url=http://www.magedu.com:80 

${url##*:} 80 

${url%%:*} http

```bash
上述内容全部范例：
[root@centos6 ~]# url=http://www.daizhe.com:80

取协议名
[root@centos6 ~]# echo ${url%%:*}
http

取端口号
[root@centos6 ~]# echo ${url##*:}
80

取全程域名FQDN
[root@centos6 ~]# echo ${url##*/}
www.daizhe.com:80

使用sed取全程域名
[root@centos6 ~]# echo $url | sed -nr 's/.*\/(.*).*/\1/p'
www.daizhe.com:80

[root@centos6 ~]# echo $url | sed -nr 's/.*\/(.*):.*/\1/p'
www.daizhe.com

```
### `查找替换 `

`${var/pattern/substr}：`查找var所表示的字符串中，第一次被pattern所匹 配到的字符串，以substr替换之 
```bash
范例：
${变量/pattern匹配/匹配到的替换为}
匹配到的第一个字符串被替换
[root@centos6 ~]# echo $url
http://www.daizhe.com:80
[root@centos6 ~]# echo ${url/http/ftp}
ftp://www.daizhe.com:80

```
`${var//pattern/substr}: `查找var所表示的字符串中，所有能被pattern所匹 配到的字符串，以substr替换之 
```bash
范例：
${变量//pattern匹配/匹配到的替换为}
匹配到的每一个都被替换
[root@centos6 ~]# echo $url
http://www.daizhe.com:80
[root@centos6 ~]# echo ${url//:/#}
http#//www.daizhe.com#80
```
`${var/#pattern/substr}：`查找var所表示的字符串中，行首被pattern所匹 配到的字符串，以substr替换之 
```bash
范例：
${变量/#pattern匹配/匹配到的替换为}
行首被匹配的替换
```


`${var/%pattern/substr}：`查找var所表示的字符串中，行尾被pattern所匹 配到的字符串，以substr替换之
```bash
范例：
${变量/%pattern匹配/匹配到的替换为}
行尾被匹配到的替换
```

### `查找并删除`

`${var/pattern}：`删除var表示的字符串中第一次被pattern匹配到的字符串 

`${var//pattern}：`删除var表示的字符串中所有被pattern匹配到的字符串 

`${var/#pattern}：`删除var表示的字符串中所有以pattern为行首匹配到的 字符串 

`${var/%pattern}：`删除var所表示的字符串中所有以pattern为行尾所匹配 到的字符串 


`字符大小写转换` 

`${var^^}：`把var中的所有小写字母转换为大写

` ${var,,}：`把var中的所有大写字母转换为小写

## 高级变量用法-有类型变量

&ensp;&ensp;Shell变量一般是无类型的，但是bash Shell提供了declare和typeset两个命令 用于指定变量的类型，两个命令是等价的 

`declare [选项] 变量名 `

&ensp;&ensp;-r 声明或显示只读变量 

&ensp;&ensp;-i 将变量定义为整型数 

&ensp;&ensp;-a 将变量定义为数组 

&ensp;&ensp;-A 将变量定义为关联数组 

&ensp;&ensp;-f 显示已定义的所有函数名及其内容

&ensp;&ensp;-F 仅显示已定义的所有函数名 

&ensp;&ensp;-x 声明或显示环境变量和函数 

&ensp;&ensp;-l  声明变量为小写字母 declare –l var=UPPER 

&ensp;&ensp;-u  声明变量为大写字母 declare –u var=lower

## `eval命令` (两次处理、第一次替换变量，第二次执行命令)

&ensp;&ensp;eval命令将会首先扫描命令行进行所有的置换，然后再执行该命令。该命令 适用于那些一次扫描无法实现其功能的变量.该命令对变量进行两次扫描 

示例： 

[root@server ~]# CMD=whoami 

[root@server ~]# echo  $CMD whoami 

[root@server ~]# eval $CMD root 

[root@server ~]# n=10        

[root@server ~]# echo {0..$n}     

&ensp;&ensp;&ensp;&ensp;{0..10} 

[root@server ~]# eval echo {0..$n} 

&ensp;&ensp;&ensp;&ensp;0 1 2 3 4 5 6 7 8 9 10

```bash
判断centos版本然后执行相应的命令
[root@centos6 data]# cat cmd.sh 
#!/bin/bash
version(){
	sed -nr "s/.*([0-9]+)\..*/\1/p" /etc/redhat-release
}
if [ `version` -eq 7 ];then
	CMD='systemctl restart network'
else
	CMD='service network restart'
fi
$CMD

```
## 间接变量引用

&ensp;&ensp;如果第一个变量的值是第二个变量的名字，从第一个变量引用第二个变量的值 就称为间接变量引用 

variable1的值是variable2，而variable2又是变量名，variable2的值为value， 间接变量引用是指通过variable1获得变量值value的行为

variable1=variable2 
 
variable2=value

第一个变量存的的是第一个变量的名字，而第二个变量是有值，借助第一个变量，取出第二个变量的值

范例：
```bash
bash Shell提供了两种格式实现间接变量引用 
eval tempvar=\$$variable1 
tempvar=${!variable1} 

[root@server ~]# N=NAME 
[root@server ~]# NAME=wangxiaochun 
[root@server ~]# N1=${!N} 
[root@server ~]# echo $N1 wangxiaochun 
[root@server ~]# eval N2=\$$N 
[root@server ~]# echo $N2 wangxiaochun

````
## 创建临时文件

`mktemp命令：`创建并显示临时文件，可避免冲突

`mktemp [OPTION]... [文件名称] `

`文件名称: `filenameXXX 

&ensp;&ensp;X至少要出现三个 ，X代表随机字符出现的次数

`OPTION： `

&ensp;&ensp;-d: 创建临时目录 

&ensp;&ensp;-p DIR或--tmpdir=DIR：指明临时文件所存放目录位置 
```bash
示例： 

mktemp /tmp/testXXX 

tmpdir=\`mktemp –d /tmp/testdirXXX` 

mktemp --tmpdir=/testdir testXXXXXX

范例：
[root@centos6 data]# mktemp /data/aaaXXXX
/data/aaaiBYO
[root@centos6 data]# ls
aaaiBYO  cmd.sh  lost+found

```
## 安装复制文件

`install命令：工具的集合`

&ensp;&ensp;install [OPTION]... [-T] SOURCE DEST 单文件 

&ensp;&ensp;install [OPTION]... SOURCE... DIRECTORY 

&ensp;&ensp;install [OPTION]... -t DIRECTORY SOURCE... 

&ensp;&ensp;install [OPTION]... -d DIRECTORY...创建空目录 

选项： 

&ensp;&ensp;-m MODE，默认755 权限

&ensp;&ensp;-o OWNER 所有者

&ensp;&ensp;-g GROUP 所属组

示例： 

install -m 700 -o wang -g admins srcfile desfile 

install –m 770 –d /testdir/installdir 
范例：
```bash
cp/etc/issue 到/data/issue并且修改权限、所有人为代哲、所属组为代哲
[root@centos6 data]# install -m600 -odaizhe -gdaizhe /etc/issue /data/issue
-rw-------. 1 daizhe daizhe    47 Nov  8 18:09 issue

```
## expect介绍

&ensp;&ensp;expect 是由Don Libes基于Tcl（ Tool Command Language ）语言开发的， 主要应用于自动化交互式操作的场景，借助Expect处理交互的命令，可以将交互 过程如：ssh登录，ftp登录等写在一个脚本上，使之自动化完成。尤其适用于需 要对多台服务器执行相同操作的环境中，可以大大提高系统管理人员的工作效率
(将交互式命令变成非交互式，无需人工干预)

`expect命令`

`expect 语法：`


expect [选项] [ -c cmds ] [ [ -[f|b] ] cmdfile ] [ args ]

`选项`

-c：从命令行执expect脚本，默认expect是交互地执行的 

&ensp;&ensp;示例：expect -c 'expect "\n" {send "pressed enter\n"} 

-d：可以输出输出调试信息 

&ensp;&ensp;示例：expect  -d ssh.exp 

`expect中相关命令`

&ensp;&ensp;spawn：启动新的进程 

&ensp;&ensp;send：用于向进程发送字符串 

&ensp;&ensp;expect：从进程接收字符串 

&ensp;&ensp;interact：允许用户交互 

&ensp;&ensp;exp_continue 匹配多个字符串在执行动作后加此命令


expect最常用的语法(tcl语言:模式-动作) 

`单一分支模式语法： `

&ensp;&ensp;expect “hi” {send “You said hi\n"} 

&ensp;&ensp;匹配到hi后，会输出“you said hi”，并换行 

`多分支模式语法：`

&ensp;&ensp;expect "hi" { send "You said hi\n" } \ 

&ensp;&ensp;&ensp;&ensp;"hehe" { send "Hehe yourself\n" } \ 

&ensp;&ensp;"bye" { send "Good bye\n" } 

匹配hi,hello,bye任意字符串时，执行相应输出。等同如下： 

expect { 
    
&ensp;&ensp;"hi" { send "You said hi\n"} 
    
&ensp;&ensp;"hehe" { send "Hehe yourself\n"} 
    
&ensp;&ensp;"bye" { send "Good bye\n"} 

}


范例：使用之前默认未安装
```bash
实现非交互式scp传文件
#!/usr/bin/expect 
spawn scp /etc/fstab 192.168.8.100:/app 

expect { 
    "yes/no" { send "yes\n";exp_continue } 
    
    "password" { send “magedu\n" } 
} 
expect eof
```
```bash
范例：实现非交互式ssh连接主机
#!/usr/bin/expect 

spawn ssh 192.168.8.100 expect { 
    
    "yes/no" { send "yes\n";exp_continue } 
    
    "password" { send “magedu\n" } 
} 

interact 
#expect eof
```

```bash
范例：变量
#!/usr/bin/expect 

set ip 192.168.8.100 
set user root 
set password magedu 
set timeout 10 
spawn ssh $user@$ip 
expect { 
    "yes/no" { send "yes\n";exp_continue } 
    
    "password" { send "$password\n" } 
}   
interact
```
```bash
范例：位置参数
#!/usr/bin/expect 

set ip [lindex $argv 0] 
set user [lindex $argv 1] 
set password [lindex $argv 2] 
spawn ssh $user@$ip 
expect { 
    "yes/no" { send "yes\n";exp_continue } 
    "password" { send "$password\n" } 
} 
interact 
#./ssh3.exp 192.168.8.100 root magedu
```
```bash
范例：执行多个命令
#!/usr/bin/expect 
set ip [lindex $argv 0] 
set user [lindex $argv 1] 
set password [lindex $argv 2] 
set timeout 10 
spawn ssh $user@$ip 
expect { 
    "yes/no" { send "yes\n";exp_continue } 
    "password" { send "$password\n" } 
} 
expect "]#" { send "useradd haha\n" } 
expect "]#" { send "echo magedu |passwd --stdin haha\n" } 
send "exit\n" 
expect eof 
#./ssh4.exp 192.168.8.100 root magedu
```
```bash
范例：shell脚本调用expect
#!/bin/bash 
ip=$1 
user=$2 password=$3 
expect <<EOF 
set timeout 10 
spawn ssh $user@$ip 
expect { 
    "yes/no" { send "yes\n";exp_continue } 
    "password" { send "$password\n" } 
} 
expect "]#" { send "useradd hehe\n" } 
expect "]#" { send "echo magedu |passwd --stdin hehe\n" } 
expect "]#" { send "exit\n" } 
expect eof 
EOF 
#./ssh5.sh 192.168.8.100 root magedu
```