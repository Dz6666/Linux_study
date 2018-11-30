---
title: AWK精讲
tags: linux
categories: linux
---

## 文本处理三剑客之AWK

## 本章内容 


### awk介绍 

### awk基本用法 

### awk变量 

### awk格式化 

### awk操作符 

### awk条件判断 

### awk循环 

### awk数组 

### awk函数 

### 调用系统命令 

## `awk介绍 `

awk：Aho, Weinberger, Kernighan，报告生成器，格式化文本输出 （模式扫描和处理语言）

有多种版本：New awk（nawk），GNU awk（ gawk） 
```bash
[root@centos7 ~]# which awk
/usr/bin/awk
[root@centos7 ~]# ll /usr/bin/awk 
lrwxrwxrwx. 1 root root 4 Oct 14 14:34 /usr/bin/awk -> gawk
```

gawk：模式扫描和处理语言 

`基本用法： `

`option :选项`

program:awk自己的程序



&ensp;&ensp;awk [options]   'program'  var=value   file…  

&ensp;&ensp;awk [options]   -f programfile    var=value  file…  

&ensp;&ensp;awk [options]  'BEGIN{action;… }pattern{action;… }END{action;… }'  file ...  

&ensp;&ensp;awk 程序可由：BEGIN语句块、能够使用模式匹配的通用语句块、END语句 块，共3部分组成  

&ensp;&ensp;program 通常是被放在单引号中

&ensp;&ensp;系统默认记录默认以换行符做分隔

`选项：  `

&ensp;&ensp;`-F `“分隔符” 指明输入时用到的字段分隔符  

&ensp;&ensp;`-v ` var=value 变量赋值   

## `awk语言 `  
`基本格式：awk [options] 'program' file… `   
Program：pattern{action statements;..}   
pattern和action   

&ensp;&ensp;pattern部分决定动作语句何时触发及触发事件   

&ensp;&ensp;&ensp;&ensp;BEGIN（处理整个文件前做的动作）,END（整个文件处理完的动作）  

&ensp;&ensp;action statements对数据进行处理，放在{}内指明    

&ensp;&ensp;&ensp;&ensp;print, printf   

`分割符、域和记录`     

&ensp;&ensp;awk执行时，由分隔符分隔的字段（域）标记$1,$2...$n称为域标识。$0 为所有域，注意：此时和shell中变量$符含义不同   

&ensp;&ensp;文件的每一行称为记录 （默认分隔为空白符） 

&ensp;&ensp;省略action，则默认执行 print $0 的操作 

## `awk工作原理`

&ensp;&ensp;第一步：执行BEGIN{action;… }语句块中的语句 

&ensp;&ensp;第二步：从文件或标准输入(stdin)读取一行，然后执行pattern{ action;… }语句块， 它逐行扫描文件，从第一行到最后一行重复这个过程，直到文件全部被读取完毕。  

&ensp;&ensp;第三步：当读至输入流末尾时，执行END{action;…}语句块 

&ensp;&ensp;`BEGIN`语句块在awk开始从输入流中读取行之前被执行，这是一个可选的语句块， 比如变量初始化、打印输出表格的表头等语句通常可以写在BEGIN语句块中 

&ensp;&ensp;`END`语句块在awk从输入流中读取完所有的行之后即被执行，比如打印所有行的 分析结果这类信息汇总都是在END语句块中完成，它也是一个可选语句块  

&ensp;&ensp;&ensp;&ensp;BEGIN（处理整个文件前做的动作）,END（整个文件处理完的动作）

&ensp;&ensp;`pattern`语句块中的通用命令是最重要的部分，也是可选的。如果没有提供 pattern语句块，则默认执行{ print }，即打印每一个读取到的行，awk读取的每 一行都会执行该语句块

范例：省略动作，相当于执行了{print $0}
```bash
[root@centos7 ~]# awk '{print $0}'
aa
aa
bb
bb
```
## `awk简单是使用`

`print格式：print item1, item2, ... `

要点：   
(1) 逗号分隔符   
(2) 输出item可以字符串，也可是数值；当前记录的字段、变量或awk的表达式   
(3) 如省略item，相当于print $0   
示例：        
&ensp;&ensp;awk  '{print "hello,awk"}'        
&ensp;&ensp;awk –F:   '{print}'    /etc/passwd       
&ensp;&ensp;awk –F:  ‘{print “wang”}’   /etc/passwd   
&ensp;&ensp;awk  –F:  ‘{print $1}’   /etc/passwd    awk  –F:  ‘{print $0}’  /etc/passwd   
&ensp;&ensp;awk  –F:  ‘{print $1”\t”$3}’  /etc/passwd    
&ensp;&ensp;grep “^UUID”/etc/fstab  |  awk ‘{print $2,$4}’

范例：awk的简单使用
```bash
可以添加自己定义的字符串，$0 代表打印文件（文件有多少行，就会打印出多少行）
[root@centos7 ~]# awk '{print "abc"$0}' /etc/fstab
abcUUID=3affb1c0-896b-4e6b-b39c-af94a31f529b /data                   xfs     defaults        0 0
abcUUID=71bd0da8-45dc-4d3b-a5bd-5ec2590e2395 swap                    swap    defaults        0 0

不添加""意思为将abc 当作了变量，系统没有abc变量，则为空
[root@centos7 ~]# awk '{print abc$0}' /etc/fstab
UUID=3affb1c0-896b-4e6b-b39c-af94a31f529b /data                   xfs     defaults        0 0
UUID=71bd0da8-45dc-4d3b-a5bd-5ec2590e2395 swap                    swap    defaults        0 0

awk也支持数字运算
[root@centos7 ~]# awk '{print 1+2}' /etc/fstab
3  文件有多少行则打印多少行
3

如果只想打印一遍，BEGIN开始文件前执行一次
[root@centos7 ~]# awk BEGIN'{print 1+3}' /etc/fstab
4

开始文件前处理动作+文件本身+文件结束处理动作
[root@centos7 ~]# awk BEGIN'{print 1+3}{print $0}END{print "end"}' /etc/fstab
4

#
# /etc/fstab
# Created by anaconda on Sun Oct 14 14:34:07 2018
UUID=3affb1c0-896b-4e6b-b39c-af94a31f529b /data                   xfs     defaults        0 0
UUID=71bd0da8-45dc-4d3b-a5bd-5ec2590e2395 swap                    swap    defaults        0 0
end


总结：
在Awk 中如果是字符则要加引号，如果是变量则不需要添加引号，变量也无需添加$符号，数字运算也不需加引号
```

范例：取出分区的设备名和利用率(默认分隔符为空白符)
```bash
[root@centos7 ~]# df | awk '{print $1,$5}'
Filesystem Use%
/dev/sda2 20%
devtmpfs 0%
tmpfs 0%
tmpfs 2%
tmpfs 0%
```

## `awk变量 `  
`变量：内置和自定义变量  ` 

#### `以下为系统自带的变量`

`FS：输入字段分隔符，默认为空白字符 ` 

&ensp;&ensp;awk -v FS=':'  '{print $1,FS,$3}’ /etc/passwd  

&ensp;&ensp;awk  –F:   '{print $1,$3,$7}’ /etc/passwd 

````bash
范例：使用FS 定义变量，变量的好处可以下次调用
[root@centos7 ~]# awk -v FS=":" '{print $1FS$2}' /etc/passwd
root:x
bin:x
daemon:x
[root@centos7 ~]# awk -v FS=":" '{print $1FS FS$2}' /etc/passwd
root::x
bin::x

也支持调用shell中的变量
[root@centos7 ~]# fs=#
[root@centos7 ~]# awk -v FS=: -v AA=$fs '{print $1AA$2}' /etc/passwd

````
`OFS：输出字段分隔符，默认为空白字符 ` 

&ensp;&ensp;awk  -v FS=‘:’  -v OFS=‘:’ '{print $1,$3,$7}’ /etc/passwd 
```bash
以：为分隔符隔断域，输出以!隔断域
[root@centos7 ~]# awk -v FS=":" -v OFS="!"'{print $1,$2}' /etc/passwd
root!x
bin!x

调用shell变量
[root@centos7 ~]# fs=:;awk -v FS=$fs -v OFS=$fs '{print $1,$2}' /etc/passwd
root:x
bin:x
```

`RS：输入记录分隔符，指定输入时的换行符 ` 

&ensp;&ensp;awk -v RS=' ' ‘{print }’ /etc/passwd 

```bash
field 域 列 字段
record 记录 行 默认\n

(空白符：tab、/n、空格)
```
```bash
范例：
[root@centos7 ~]# cat a.txt
aaa;xxx:bb;bbbbddd
ddd:eee;cccc:xxxxx
[root@centos7 ~]# awk -v FS=";" -v RS=":" '{print $0}' a.txt
aaa;xxx
bb;bbbbddd
ddd
eee;cccc
xxxxx
```

`ORS：输出记录分隔符，输出时用指定符号代替换行符`  

awk -v RS=' ' -v ORS='###'‘{print }’ /etc/passwd 
```bash
[root@centos7 ~]# cat a.txt
aaa;xxx:bb;bbbbddd
ddd:eee;cccc:xxxxx
[root@centos7 ~]# awk -v FS=";" -v RS=":" -v ORS="***" '{print $0}' a.txt
aaa;xxx***bb;bbbbddd
ddd***eee;cccc***xxxxx

```
`NF：字段数量 (显示其中的每一行有多少个记录)` 

awk  -F：‘{print NF}’  /etc/fstab 引用变量时，变量前不需加$  

awk  -F：‘{print $(NF-1)}'  /etc/passwd 
```bash
定义字段的分隔符为冒号：显示/etc/passwd文件中一行有多少条记录
[root@centos7 ~]# awk -F ":" '{print NF}' /etc/passwd
7
7
7
7
7
7

$NF代表每行中的最后一个字段
[root@centos7 ~]# awk -F ":" '{print $NF}' /etc/passwd
/bin/bash
/sbin/nologin
/sbin/nologin
/sbin/nologin
/sbin/nologin

$(NF-1)倒数第2列个记录
[root@centos7 ~]# awk -F ":" '{print $(NF-1)}' /etc/passwd
/root
/bin
/sbin
```
`NR：记录号（记录的编号）`       

awk ‘{print NR}’  /etc/fstab ; awk END‘{print NR}’  /etc/fstab 

```bash
以：为分隔符，RS=":"
显示记录的标号
[root@centos7 ~]# awk -v RS=":" '{print NR,$0}' a.txt
1 aaa;xxx
2 bb;bbbbddd
ddd
3 eee;cccc
4 xxxxx
```
`FNR：各文件分别计数,记录号`  

awk '{print FNR}'  /etc/fstab /etc/inittab 

````bash
以:为分隔符取两个文件的第一个字段

NR默认两个文件的记录编号放在一起
[root@centos7 ~]# awk -F : '{print NR,$1}' /etc/passwd /etc/group
1 root
2 bin
3 daemon
...
...
100 pulse
101 gdm
102 gnome-initial-setup
103 sshd

使用FNR
[root@centos7 ~]# awk -F : '{print FNR,$1}' /etc/passwd /etc/group
1 root
2 bin
...
...
1 root
2 bin
````

`FILENAME：当前文件名`  

awk '{print FILENAME}’  /etc/fstab 

```bash
[root@centos7 ~]# awk -F : '{print FNR,FILENAME,$1}' /etc/passwd /etc/group
1 /etc/passwd root
2 /etc/passwd bin
...
...
1 /etc/group root
2 /etc/group bin
```

`ARGC：命令行参数的个数`  

awk '{print ARGC}’  /etc/fstab /etc/inittab  awk ‘BEGIN {print ARGC}’  /etc/fstab /etc/inittab 

```bash
代表整条命令有几个参数
[root@centos7 ~]# awk -F : '{print ARGC}' /etc/passwd /etc/group
3
3
3
...
...
```

`ARGV：数组，保存的是命令行所给定的各参数` 

awk ‘BEGIN {print ARGV[0]}’  /etc/fstab /etc/inittab  

awk ‘BEGIN {print ARGV[1]}’  /etc/fstab /etc/inittab 
 
```bash
显示参数都是谁
[root@centos7 ~]# awk -F : '{print ARGV[0]}' /etc/passwd /etc/group
awk
awk
awk
...
...
[root@centos7 ~]# awk -F : '{print ARGV[1]}' /etc/passwd /etc/group
/etc/passwd
/etc/passwd
/etc/passwd
...
...
[root@centos7 ~]# awk -F : '{print ARGV[2]}' /etc/passwd /etc/group
/etc/group
/etc/group
/etc/group
...
...
```
一次截取范例：

范例：awk取ss-nt的ip地址
```bash
[root@centos7 ~]# ss -nt | awk '{print $4}' | awk -F : '{print $1}'
Local
192.168.131.175
```
范例：使用两个符号作为分隔符截取
```bash
使用中括号或者空格符即空格和中括号都为分隔符
[root@centos7 ~]# awk -F "[\[ ]" '{print $5}' /var/log/httpd/access_log 
```
范例：查看分区使用率，取出利用率和名称取出
```bash
[root@centos7 ~]# df
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sda2       20961280 4047880  16913400  20% /
devtmpfs          916724       0    916724   0% /dev
tmpfs             932652       0    932652   0% /dev/shm
[root@centos7 ~]# df | awk -F "[[:space:]]+|%" '{print $5}'
Use
20
0
0
```



## `自定义变量(区分字符大小写) `

&ensp;&ensp;(1) -v var=value    

&ensp;&ensp;(2) 在program中直接定义 

示例：   

&ensp;&ensp;awk  -v test='hello gawk' '{print test}' /etc/fstab   

&ensp;&ensp;awk  -v test='hello gawk' 'BEGIN{print test}'   

&ensp;&ensp;awk  'BEGIN{test="hello,gawk";print test}'   

&ensp;&ensp;awk  -F:‘{sex=“male”;print $1,sex,age;age=18}’ /etc/passwd  

&ensp;&ensp;cat awkscript  

&ensp;&ensp;{print script,$1,$2}  

&ensp;&ensp;awk  -F: -f awkscript script=“awk” /etc/passwd 


范例：自己定义变量打印
```bash
[root@centos7 ~]# awk -v name="haha" '{print name}' /etc/fstab
haha
haha
haha
...
[root@centos7 ~]# awk -v name="haha" -F : '{print $1,name}' /etc/passwd
root haha
bin haha
daemon haha
adm haha
lp haha
sync haha
...
...
也可以内部定义变量(必须先定义后使用)
[root@centos7 ~]# awk -F : '{name="xixi";print $1,name}' /etc/passwd
root xixi
bin xixi
daemon xixi
adm xixi
lp xixi
...
...
```
范例：也可以放进文件调用

&ensp;&ensp;awk [options]   -f programfile    var=value  file… 
```bash
[root@centos7 ~]# cat awk.txt 
{print $1}
[root@centos7 ~]# awk -F : -f awk.txt /etc/passwd
root
bin
daemon
adm
...
...
```
### `printf命令` 


`格式化输出：printf “FORMAT(定义匹配到的变量的格式)”, item1(匹配到的第一个变量), item2（匹配到的第二个变量）, ...  `

&ensp;&ensp;(1) 必须指定FORMAT  

&ensp;&ensp;(2) 不会自动换行，需要显式给出换行控制符，\n  

&ensp;&ensp;(3) FORMAT中需要分别为后面每个item指定格式符 

`格式符：与item一一对应 ` 

&ensp;&ensp;%c：显示字符的ASCII码  

&ensp;&ensp;%d, %i：显示十进制整数  

&ensp;&ensp;%e, %E：显示科学计数法数值   

&ensp;&ensp;%f：显示为浮点数  

&ensp;&ensp;%g, %G：以科学计数法或浮点形式显示数值  

&ensp;&ensp;`%s：显示字符串 ` 

&ensp;&ensp;%u：无符号整数  

&ensp;&ensp;%%：显示%自身 

`修饰符  `

&ensp;&ensp;#[.#] 第一个数字控制显示的宽度；第二个#表示小数点后精度，%3.1f  

&ensp;&ensp;- 左对齐（默认右对齐） %-15s 

&ensp;&ensp; + 显示数值的正负符号 %+d 


范例：
```bash
[root@centos7 ~]# awk -F : '{printf "%20s  %5s\n",$1,$3}' /etc/passwd
                root      0
                 bin      1
              daemon      2
                 adm      3
                  lp      4
                sync      5
            shutdown      6

[root@centos7 ~]# awk -F : '{printf "%-20s  %-5s\n",$1,$3}' /etc/passwd
root                  0    
bin                   1    
daemon                2    
adm                   3    
lp                    4    
sync                  5    
shutdown              6    

[root@centos7 ~]# awk -F : 'BEGIN{print "__________________________________\nusername                   |uid\n__________________________________|"}{printf "%-20s   |%-5d|\n__________________________________\n",$1,$3}' /etc/passwd
__________________________________
username                   |uid
__________________________________|
root                   |0    |
__________________________________
bin                    |1    |
__________________________________
daemon                 |2    |
__________________________________
adm                    |3    |
__________________________________
lp                     |4    |
__________________________________
sync                   |5    |
__________________________________
shutdown               |6    |
__________________________________
halt                   |7    |
__________________________________
mail                   |8    |
__________________________________
operator               |11   |
__________________________________
```


printf示例 

awk -F: ‘{printf "%s",$1}’ /etc/passwd 

awk -F: ‘{printf "%s\n",$1}’ /etc/passwd 

awk -F:   '{printf "%-20s %10d\n",$1,$3}' /etc/passwd 

awk -F:‘ {printf "Username: %s\n",$1}’  /etc/passwd 

awk -F: ‘{printf “Username: %s,UID:%d\n",$1,$3}’ /etc/passwd 

awk -F: ‘{printf "Username: %15s,UID:%d\n",$1,$3}’ /etc/passwd 

awk -F: ‘{printf "Username: %-15s,UID:%d\n",$1,$3}’  /etc/passwd 
 
## `操作符` 

`算术操作符： ` 

&ensp;&ensp;x+y, x-y, x*y, x/y, x^y, x%y  

&ensp;&ensp;&ensp;&ensp;\- x：转换为负数  

&ensp;&ensp;&ensp;&ensp;+x：将字符串转换为数值 
```bash
[root@centos7 ~]# awk 'BEGIN{print 2^3}'
8
```
`字符串操作符：没有符号的操作符，字符串连接 `


`赋值操作符： `

&ensp;&ensp;=, +=, -=, *=, /=, %=, ^=，++, -- 

下面两语句有何不同 

&ensp;&ensp;awk  ‘BEGIN{i=0;print ++i,i}’ 

&ensp;&ensp;awk  ‘BEGIN{i=0;print i++,i}’ 

```bash
[root@centos7 ~]# awk -v i=100 'BEGIN{print i++}'
100
[root@centos7 ~]# awk -v i=100 'BEGIN{print ++i}'
101
[root@centos7 ~]# awk -v i=100 'BEGIN{print i++,i}'
100 101
[root@centos7 ~]# awk -v i=100 'BEGIN{print ++i,i}'
101 101

[root@centos7 ~]# i=200;let j=i++;echo $j
200
[root@centos7 ~]# i=200;let j=++i;echo $j
201
```

范例：新浪运维面试题  
[root@centos7 ~]# cat a.txt  
1 test.sina.com.cn  
2 music.sina.com.cn  
3 sports.sina.com.cn    
从此文件中取出test、music、sports并重新写入此文件  

```bash
[root@centos7 ~]# awk -F "[ .]" '{print $2}' a.txt
test
music
sports
[root@centos7 ~]# awk -F "[ .]" '{print $2}' a.txt >> a.txt
[root@centos7 ~]# cat a.txt 
1 test.sina.com.cn
2 music.sina.com.cn
3 sports.sina.com.cn
test
music
sports
```

`比较操作符： `  

&ensp;&ensp;==, !=, >, >=, <, <= 

`模式匹配符：   `     

&ensp;&ensp;~：左边是否和右边匹配包含 !~：是否不匹配 

示例：  

&ensp;&ensp;awk  -F: '$0 ~ /root/{print $1}‘  /etc/passwd  

&ensp;&ensp;awk  '$0~“^root"'     /etc/passwd   awk  '$0  !~ /root/‘   /etc/passwd  

&ensp;&ensp;awk  -F: ‘$3==0’     /etc/passwd

`逻辑操作符：与&&(并且)，或||，非! `

示例： 取反时要加（）

&ensp;&ensp;awk –F: '$3>=0 && $3<=1000 {print $1}' /etc/passwd 

&ensp;&ensp;awk -F: '$3==0 || $3>=1000 {print $1}' /etc/passwd  

&ensp;&ensp;awk -F: ‘!($3==0) {print $1}' /etc/passwd 

&ensp;&ensp;awk -F: ‘!($3>=500) {print $3}’ /etc/passwd 


`条件表达式（三目表达式）   `  

&ensp;&ensp;selector（选择条件）?if-true-expression:if-false-expression 
```bash
表达式：条件判断
判断第三列是否大于等于1000，如果大于则命名为普通用户，如果不大于则命名为系统用户
[root@centos7 ~]# awk -F : '{$3>=1000?name="user":name="system user";print name,$1,$3}' /etc/passwd
system user root 0
system user bin 1
system user daemon 2
system user adm 3
system user lp 4
system user sync 5
user daizhe 1000
user user1 1001
system user apache 48
```

示例：     

&ensp;&ensp;awk -F: '{$3>=1000?usertype="Common User":usertype=" SysUser";printf "%15s:%-s\n",$1,usertype}' /etc/passwd 
 
### `awk PATTERN `

`PATTERN:根据pattern条件，过滤匹配的行，再做处理`    
`(1)如果未指定：空模式，匹配每一行 `    

`(2) /regular expression/：仅处理能够模式匹配到的行，需要用/  /括起来 ` 

&ensp;&ensp;awk '/^UUID/{print $1}' /etc/fstab  

&ensp;&ensp;awk '!/^UUID/{print $1}' /etc/fstab 

`(3) relational expression: 关系表达式，结果为“真”才会被处理  `

&ensp;&ensp;真：结果为非0值，非空字符串  

&ensp;&ensp;假：结果为空字符串或0值  

```bash
[root@centos7 ~]# awk '0{print $0}' /etc/passwd                            不作任何显示
[root@centos7 ~]# awk '""{print $0}' /etc/passwd                           不作任何显示
[root@centos7 ~]# awk '" "{print $0}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
[root@centos7 ~]# awk 'aaa{print $0}' /etc/passwd                          不作任何显示，因为认为aaa为变量
[root@centos7 ~]# awk '"aaa"{print $0}' /etc/passwd      
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
[root@centos7 ~]# awk -v aaa=haha 'aaa{print $0}' /etc/passwd              只要不为空则为真
daizhe:x:1000:1000:daizhe:/home/daizhe:/bin/bash
user1:x:1001:1001::/home/user1:/bin/bash

取反范例：
[root@centos7 ~]# awk -v aaa=""! 'aaa{print $0}' /etc/passwd
daizhe:x:1000:1000:daizhe:/home/daizhe:/bin/bash
user1:x:1001:1001::/home/user1:/bin/bash

[root@centos7 ~]# awk -v aaa=" "! 0'{print $0}' /etc/passwd               不显示

[root@centos7 ~]# awk -v aaa=" "! 1'{print $0}' /etc/passwd
daizhe:x:1000:1000:daizhe:/home/daizhe:/bin/bash
user1:x:1001:1001::/home/user1:/bin/bash
```

示例： 

&ensp;&ensp;awk   -F:  'i=1;j=1{print i,j}' /etc/passwd 

&ensp;&ensp;awk  ‘!0’  /etc/passwd ;  awk  ‘!1’   /etc/passwd 

&ensp;&ensp;awk  -F: '$3>=1000{print $1,$3}'  /etc/passwd 

&ensp;&ensp;awk  -F: '$3<1000{print $1,$3}'  /etc/passwd 

&ensp;&ensp;awk  -F: '$NF=="/bin/bash"{print $1,$NF}' /etc/passwd 

&ensp;&ensp;awk  -F: '$NF ~ /bash$/{print $1,$NF}' /etc/passwd   
`4) line ranges：行范围（匹配的第一个到第二个匹配的中间的行） ` 

&ensp;&ensp;startline,endline：/pat1/,/pat2/ 不支持直接给出数字格式  

&ensp;&ensp;awk -F: ‘/^root\>/,/^nobody\>/{print $1}' /etc/passwd  

&ensp;&ensp;awk -F: ‘(NR>=10&&NR<=20){print NR,$1}' /etc/passwd 

`(5) BEGIN/END模式`  

&ensp;&ensp;BEGIN{}: 仅在开始处理文件中的文本之前执行一次  

&ensp;&ensp;END{}：仅在文本处理完成之后执行一次 

`正则表达式范例：`

范例：仅取分区利用率和设备的名称
```bash
[root@centos7 ~]# df | awk -F "[[[:space:]]+|%]" '/^\/dev\/sd/{print $1,$5}'
/dev/sda2 20%
/dev/sda3 1%
/dev/sda1 1%
```
范例：ss -nt 仅取建立连接的ip地址
```bash
正数
[root@centos7 ~]# ss -tn | awk -F "[[:space:]]+|:" '/^ESTAB/{print $6}' 
192.168.131.1
倒数
[root@centos7 ~]# ss -tn | awk -F "[[:space:]]+|:" '/^ESTAB/{print $(NF-2)}'
192.168.131.1
```

`关系表达式范例：`

范例：lastb 取连接失败的ip
```bash
连接失败的ip
第三列是包含此正则表达式的行才显示
[root@centos7 ~]# lastb | awk '$3 ~ /^[[:digit:]]/{print $3}' 

显示连接次数大于三的才显示
[root@centos7 ~]# lastb | awk '$3 ~ /^[[:digit:]]/{print $3}' | sort | uniq -c | awk '$1 >=3{print $1,$3}'
```

范例：显示文件中第10行到第20行
```bash
[root@centos7 ~]# awk 'NR>=10 && NR<=20{print NR,$0}' /etc/passwd
10 operator:x:11:0:operator:/root:/sbin/nologin
...
...
20 saslauth:x:996:76:Saslauthd user:/run/saslauthd:/sbin/nologin
```



示例

&ensp;&ensp;awk -F : 'BEGIN {print "USER USERID"} {print $1":"$3} END{print "end file"}' /etc/passwd 

&ensp;&ensp;awk -F : '{print "USER USERID“;print $1":"$3} END{print "end file"}' /etc/passwd 

&ensp;&ensp;awk -F: 'BEGIN{print "  USER  UID  \n--------------- "}{print $1,$3}' /etc/passwd 

&ensp;&ensp;awk -F: 'BEGIN{print "  USER UID  \n--------------- "}{print $1,$3}'END{print "=============="} /etc/passwd 

```bash
[root@centos7 ~]# seq 10 | awk 'i=0'                               不作显示
[root@centos7 ~]# seq 10 | awk 'i=1'
1
2
3
4
5
6
7
8
9
10
[root@centos7 ~]# seq 10 | awk 'i=!i'
1
3
5
7
9
[root@centos7 ~]# seq 10 | awk '{i=!i;print i}'
1
0
1
0
1
0
1
0
1
0  
[root@centos7 ~]# seq 10 | awk '!(i=!i)'
2
4
6
8
10
[root@centos7 ~]# seq 10 | awk -v i=1 'i=!i'
2
4
6
8
10
```
## `awk action `

常用的action分类 

&ensp;&ensp;(1) Expressions:算术，比较表达式等 

&ensp;&ensp;(2) Control statements：if, while等 

&ensp;&ensp;(3) Compound statements：组合语句 

&ensp;&ensp;(4) input statements 

&ensp;&ensp;(5) output statements：print等 


## `awk控制语句 `


&ensp;&ensp;{ statements;… } 组合语句 

&ensp;&ensp;if(condition) {statements;…}  

&ensp;&ensp;if(condition) {statements;…} else {statements;…} 

&ensp;&ensp;while(conditon) {statments;…} 

&ensp;&ensp;do {statements;…} while(condition) 

&ensp;&ensp;for(expr1;expr2;expr3) {statements;…} 

&ensp;&ensp;break 

&ensp;&ensp;continue 

&ensp;&ensp;delete array[index] 

&ensp;&ensp;delete array 

&ensp;&ensp;exit  

## `awk控制语句if-else `



语法：

&ensp;&ensp;`if(condition条件){条件成立的话执行的语句statement;…}[不成立执行的操作else statement]  `

&ensp;&ensp;`if(condition1){statement1}else if(condition2){statement2}else{statement3} `

`使用场景：对awk取得的整行或某个字段做条件判断 `

示例： 

&ensp;&ensp;awk -F: '{if($3>=1000)print $1,$3}' /etc/passwd 

&ensp;&ensp;awk -F: '{if($NF=="/bin/bash") print $1}' /etc/passwd 

&ensp;&ensp;awk '{if(NF>5) print $0}' /etc/fstab 

&ensp;&ensp;awk -F: '{if($3>=1000) {printf "Common user: %s\n",$1} else {printf "root or Sysuser: %s\n",$1}}' /etc/passwd 

&ensp;&ensp;awk -F: '{if($3>=1000) printf "Common user: %s\n",$1; else printf "root or 

&ensp;&ensp;Sysuser: %s\n",$1}' /etc/passwd 

&ensp;&ensp;df -h|awk -F% '/^\/dev/{print $1}'|awk '$NF>=80{print $1,$5}‘ 

&ensp;&ensp;awk ‘BEGIN{ test=100;if(test>90){print “very good“}  


&ensp;&ensp;&ensp;&ensp;else if(test>60){ print ”good”}else{print “no pass”}}’ 


范例：awk考试成绩的判断
```bash
[root@centos7 ~]# awk -v nmu=66 'BEGIN{if(nmu <60){print "yiban"}else if (nmu <=80){print "soso"}else{print "good"}}'
soso
```
## `awk控制语句`

`while循环 `

相同行的不同字段循环

`语法：while(condition){statement;…} `

条件“真”，进入循环；条件“假”，退出循环 

使用场景：  

&ensp;&ensp;&ensp;&ensp;对一行内的多个字段逐一类似处理时使用  

&ensp;&ensp;&ensp;&ensp;对数组中的各元素逐一处理时使用 

示例： 

&ensp;&ensp;awk '/^[[:space:]]*linux16/{i=1;while(i<=NF)   {print $i,length($i); i++}}' /etc/grub2.cfg 
```bash
[root@centos7 ~]# awk '/^[[:space:]]*linux16/{i=1;while(i<=NF)   {print $i,length($i); i++}}' /etc/grub2.cfg 
linux16 7
/vmlinuz-3.10.0-862.el7.x86_64 30
root=UUID=30d5e97d-c328-4412-bf78-f2629c44ea5e 46
ro 2
crashkernel=auto 16
rhgb 4
quiet 5
LANG=en_US.UTF-8 16
linux16 7
/vmlinuz-0-rescue-7f3c989023b74ce1ae1d3236af101009 50
root=UUID=30d5e97d-c328-4412-bf78-f2629c44ea5e 46
ro 2
crashkernel=auto 16
rhgb 4
quiet 5
```

&ensp;&ensp;awk ‘/^[[:space:]]*linux16/{i=1;while(i<=NF) {if(length($i)>=10) {print $i,length($i)}; i++}}’ /etc/grub2.cfg 
````bash
[root@centos7 ~]# awk '/^[[:space:]]*linux16/{i=1;while(i<=NF) {if(length($i)>=10) {print $i,length($i)}; i++}}' /etc/grub2.cfg
/vmlinuz-3.10.0-862.el7.x86_64 30
root=UUID=30d5e97d-c328-4412-bf78-f2629c44ea5e 46
crashkernel=auto 16
LANG=en_US.UTF-8 16
/vmlinuz-0-rescue-7f3c989023b74ce1ae1d3236af101009 50
root=UUID=30d5e97d-c328-4412-bf78-f2629c44ea5e 46
crashkernel=auto 16
````
范例：系统自带函数，统计字段的长度
```bash
[root@centos7 ~]# awk 'BEGIN{print length("aaa")}'
3
```
范例：统计/etc/passwd 文件的第一个字段的长度
```bash
[root@centos7 ~]# awk -F : '{print length($1)}' /etc/passwd
4
3
6
3
[root@centos7 ~]# awk -F : '{print length($1)$1}' /etc/passwd
4root
3bin
6daemon
```

范例：文件内存放1000个随机数字，取出最大值和最小值
```bash
脚本生成1000个随机数字，中间以，间隔
[root@centos7 ~]# for i in {1..1000};do if [ $i -eq 1 ];then echo -e "$RANDOM\c" >> a.txt;else echo -e ",$RANDOM\c" >> a.txt ;fi;done
[root@centos7 ~]# cat a.txt 
5882,1704,14807,21821,7666,8275,21657,15753,30310,9421,17625,4974,12633,20424,25120,10418,32144,8700,22350,25971,26146,19410,23849,12552,3757,3768,30365,28272,32641,1151,23986,25811,1234,11879,8182,22815,16989,5564,30265,12377,24848,30761,1145,17399,5723,22376,5417,25747,96........

取出文件中的最大值和最小值
[root@centos7 ~]# awk -F , '{i=2;max=$1;min=$1;while(i<=NF){if($i > max){max=$i}else if($i < min){min=$i};i++}}END{print "max="max,"min="min}' a.txt
max=32743 min=20

```

`do-while循环 `

`语法：do {statement;…}while(condition) `

意义：无论真假，至少执行一次循环体 
 
示例：      

&ensp;&ensp;awk 'BEGIN{ total=0;i=0;do{ total+=i;i++;}while(i<=100);print total}’

范例：从1加到100
```bash
[root@centos7 ~]# awk 'BEGIN{ total=0;i=0;do{ total+=i;i++;}while
5050
[root@centos7 ~]# awk 'BEGIN{for(i=1;i<=100;i++)total+=i;print total}'
5050
[root@centos7 ~]# awk 'BEGIN{total=0;for(i=1;i<=100;i++)total+=i;print total}'
5050
```

`for循环` 

`语法：for(expr1;expr2;expr3) {statement;…}` 

常见用法：  

&ensp;&ensp;for(variable assignment;condition;iteration process)    

&ensp;&ensp;&ensp;&ensp;{for-body} 

特殊用法：能够遍历数组中的元素  

&ensp;&ensp;语法：for(var in array) {for-body} 

示例：   

&ensp;&ensp;awk '/^[[:space:]]*linux16/{for(i=1;i<=NF;i++) {print $i,length($i)}}' /etc/grub2.cfg 

范例：对/etc/passwd 文件做循环逐行处理
```bash
[root@centos7 ~]# awk -F : 'NR==1{i=1;while(i<=NF){print $i,length($i);i++}}' /etc/passwd
root 4
x 1
0 1
0 1
root 4
/root 5
/bin/bash 9
```

### `性能比较 `


time (awk 'BEGIN{ total=0;for(i=0;i<=10000;i++){total+=i;};print total;}') 

time(total=0;for i in {1..10000};do total=$(($total+i));done;echo $total) 

time(for ((i=0;i<=10000;i++));do let total+=i;done;echo $total) 

time(seq –s ”+” 10000|bc) 

`awk控制语句 `

`switch语句` 

`语法：switch(expression) {case VALUE1 or /REGEXP/: statement1; case VALUE2 or /REGEXP2/: statement2; ...; default: statementn} `
 

`break和continue `    

awk ‘BEGIN{sum=0;for(i=1;i<=100;i++)  

&ensp;&ensp;&ensp;&ensp;{if(i%2==0)continue;sum+=i}print sum}‘     

awk ‘BEGIN{sum=0;for(i=1;i<=100;i++) 

&ensp;&ensp;&ensp;&ensp;{if(i==66)break;sum+=i}print sum}‘ 


`break [n]` 

`continue [n] `
 
`next: `  

&ensp;&ensp;提前结束对本行处理而直接进入下一行处理（awk自身循环）   

&ensp;&ensp;&ensp;&ensp;awk -F: '{if($3%2!=0) next; print $1,$3}' /etc/passwd 
 
### `awk数组 `


`关联数组：array[index-expression] `

index-expression: 

&ensp;&ensp;(1) 可使用任意字符串；字符串要使用双引号括起来 

&ensp;&ensp;(2) 如果某数组元素事先不存在，在引用时，awk会自动创建此元素，并将其值 初始化为“空串” 

&ensp;&ensp;(3) 若要判断数组中是否存在某元素，要使用“index in array”格式进行遍历 

示例： 

&ensp;&ensp;weekdays[“mon”]="Monday“ 

&ensp;&ensp;awk 'BEGIN{weekdays["mon"]="Monday"; 
weekdays["tue"]="Tuesday";print weekdays["mon"]}‘ 

&ensp;&ensp;awk ‘!arr[$0]++’ file  &ensp;&ensp;实现去重功能

&ensp;&ensp;awk '{!arr[$0]++;print $0, arr[$0]}'  file 

```bash
[root@centos7 ~]# awk '{!arr[$0]++;print $0, arr[$0]}'  aa.txt
aaaa 1
aaaa 2
bbb 1
ccc 1
ddd 1
eee 1
eee 2
eee 3
```

`若要遍历数组中的每个元素，要使用for循环 (作用：关联数组，下标不确定)`

for(var in array) {for-body} 

注意：var会遍历array的每个索引 

示例：     

&ensp;&ensp;awk 'BEGIN{weekdays["mon"]="Monday";weekdays["tue"]  ="Tuesday";for(i in weekdays) {print weekdays[i]}}‘    

&ensp;&ensp;netstat -tan | awk '/^tcp/{state[$NF]++}END  {for(i in state) { print i,state[i]}}'     

&ensp;&ensp;awk '{ip[$1]++}END{for(i in ip) {print i,ip[i]}}' /var/log/httpd/access_log 

范例：遍历数组
```bash
for(var in array) {for-body}   显示无序
[root@centos7 ~]# awk 'BEGIN{title["ceo"]="daizhe";title["coo"]="xiaodaizhe";for(i in title){print title[i]}}'
xiaodaizhe
daizhe
[root@centos7 ~]# awk 'BEGIN{title["ceo"]="daizhe";title["coo"]="xiaodaizhe";for(i in title){print i,title[i]}}'
coo xiaodaizhe
ceo daizhe
```

范例：awk关联数组
```bash
[root@centos7 ~]# awk 'BEGIN{title["ceo"]="daizhe";title["coo"]="xiaodaizhe";print title["ceo"]}'
daizhe
```
范例:awk数组，实现文件去重的功能
```bash
[root@centos7 ~]# cat aa.txt 
aaaa
aaaa
bbb
ccc
ddd
eee
eee
[root@centos7 ~]# awk '!arr[$0]++' aa.txt
aaaa
bbb
ccc
ddd
eee
```
范例：显示ss -tan 的连接状态的个数
```bash
[root@centos7 ~]# netstat -tan | awk '/^tcp/{state[$NF]++}END  {for(i in state) { print i,state[i]}}'   
LISTEN 9
ESTABLISHED 1
```
范例：显示ss -nt的连接的ip次数
```bash
[root@centos7 ~]# ss -tn
State       Recv-Q Send-Q        Local Address:Port                     Peer Address:Port              
ESTAB       0      52            192.168.131.176:22                     192.168.131.1:52184           
[root@centos7 ~]# ss -nt | awk -F"[[:space:]]+|:" '/ESTAB/{ip[$(NF-2)]++}END{for(i in ip){print i,ip[i]}}'
192.168.131.1 1
```

范例：显示http应用程序日志中ip出现的次数
```bash
[root@centos7 ~]# awk '{ip[$1]++}END{for(i in ip){print i,ip[i]}}' /var/log/httpd/access_log
```
范例：显示/etc/fstab文件系统的类型出现的次数
```bash
[root@centos7 ~]# cat /etc/fstab
UUID=30d5e97d-c328-4412-bf78-f2629c44ea5e /                       xfs     defaults        0 0
UUID=19a71b9d-1573-433a-a53b-532d86a39918 /boot                   xfs     defaults        0 0
UUID=3affb1c0-896b-4e6b-b39c-af94a31f529b /data                   xfs     defaults        0 0
UUID=71bd0da8-45dc-4d3b-a5bd-5ec2590e2395 swap                    swap    defaults        0 0
[root@centos7 ~]# awk '/^UUID/{filesystem[$3]++}END{for (i in filesystem){print i,filesystem[i]}}' /etc/fstab
swap 1
xfs 3
```
范例：计算男女平均成绩
```bash
[root@centos7 ~]# cat test.txt 
name sex score
a    m   90
b    f   80
c    f   99
d    m   88

男女总数
[root@centos7 ~]# awk '!/name/{num[$2]+=$3}END{for (i in num){print i,num[i]}}' test.txt 
m 178
f 179
[root@centos7 ~]# awk '!/name/{num[$2]+=$3;chu[$2]++}END{for (i in num){print i,num[i]/chu[i]}}' test.txt 
m 89
f 89.5
```
## `awk函数`

`数值处理： ` 

&ensp;&ensp;rand()：返回0和1之间一个随机数   

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;awk  'BEGIN{srand(); for (i=1;i<=10;i++)print int(rand()*100) }'  

`字符串处理： `

&ensp;&ensp;length([s])：返回指定字符串的长度 

&ensp;&ensp;sub(r,s,[t])：对t字符串搜索r表示模式匹配的内容，并将第一个匹配内容替换为s  

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;echo "2008:08:08 08:08:08" | awk 'sub(/:/,“-",$1)' 
```bash
[root@centos7 ~]# echo "2008:08:08 08:08:08" | awk 'sub(/:/,"-",$1)' 
2008-08:08 08:08:08
```

&ensp;&ensp;gsub(r,s,[t])：对t字符串进行搜索r表示的模式匹配的内容，并全部替换为s所表 示的内容  

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;echo "2008:08:08 08:08:08" | awk ‘gsub(/:/,“-",$0)' 

&ensp;&ensp;split(s,array,[r])：以r为分隔符，切割字符串s，并将切割后的结果保存至array所 表示的数组中，第一个索引值为1,第二个索引值为2,…  

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;netstat -tn | awk '/^tcp\>/{split($5,ip,":");count[ip[1]]++} END{for (i in count) {print i,count[i]}}’ 
```bash
ip
[root@centos7 ~]# netstat -tn | awk '/^tcp\>/{split($5,ip,":");count[ip[1]]++} END{for (i in count) {print i,count[i]}}' 
192.168.131.1 1
端口
[root@centos7 ~]# netstat -tn | awk '/^tcp\>/{split($5,ip,":");count[ip[2]]++} END{for (i in count) {print i,count[i]}}' 
52184 1
```

`自定义函数格式：`  

&ensp;&ensp;function name ( parameter, parameter,  ... ) {                         
  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;statements                         

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;return expression  

&ensp;&ensp;} 

范例：awk生成随机数
```bash
[root@centos7 ~]# awk 'BEGIN{srand();print rand()}'
0.0539655
[root@centos7 ~]# awk 'BEGIN{srand();print rand()*100}'
96.6134

去除小数点随机1~100
[root@centos7 ~]# awk 'BEGIN{srand();print int(rand()*100)}'
57
```

示例：  

&ensp;&ensp;cat fun.awk  

&ensp;&ensp;function max(x,y) {   
  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;x>y?var=x:var=y   

&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;return var  

&ensp;&ensp;}  

&ensp;&ensp;BEGIN{a=3;b=2;print max(a,b)}             

&ensp;&ensp;awk –f fun.awk  



## `awk中调用shell命令`

`system命令` 

&ensp;&ensp;空格是awk中的字符串连接符，如果system中需要使用awk中的变量可以使用 空格分隔，或者说除了awk的变量外其他一律用""引用起来  

&ensp;&ensp;awk‘BEGIN'{system("hostname") }'   

&ensp;&ensp;awk‘BEGIN{score=100; system("echo  your score is " score) }' 

### `awk脚本 `

`将awk程序写成脚本，直接调用或执行 `

示例：       

&ensp;&ensp;cat f1.awk  

&ensp;&ensp;&ensp;&ensp;{if($3>=1000)print $1,$3}     &ensp;&ensp;awk -F: -f f1.awk /etc/passwd  

---

cat f2.awk   

&ensp;&ensp;#!/bin/awk –f  

&ensp;&ensp;#this is a awk script  

&ensp;&ensp;{if($3>=1000)print $1,$3}    

&ensp;&ensp;chmod +x f2.awk   

&ensp;&ensp;f2.awk –F:  /etc/passwd 

### 向awk脚本传递参数 

`格式： ` 

&ensp;&ensp;`awkfile  var=value var2=value2... Inputfile `

&ensp;&ensp;注意：在BEGIN过程中不可用。直到首行输入完成以后，变量才可用。可以通 过-v 参数，让awk在执行BEGIN之前得到变量的值。命令行中每一个指定的变 量都需要一个-v参数 

示例：  

&ensp;&ensp;cat  test.awk       

&ensp;&ensp;&ensp;&ensp;#!/bin/awk –f    

&ensp;&ensp;&ensp;&ensp;{if($3 >=min && $3<=max)print $1,$3}   

&ensp;&ensp;&ensp;&ensp;chmod +x test.awk  

&ensp;&ensp;&ensp;&ensp;test.awk -F: min=100 max=200  /etc/passwd 

