## `文件查找`

在文件系统上查找符合条件的文件 

&ensp;&ensp;文件查找：locate, find

 &ensp;&ensp;非实时查找(数据库查找)：
 locate 
 
 实时查找：find

 ## locate

查询系统上预建的文件索引数据库 

&ensp;&ensp;/var/lib/mlocate/mlocate.db  数据库文件

 依赖于事先构建的索引

  &ensp;&ensp;索引的构建是在系统较为空闲时自动进行(周期性任务)，管理员手动更新数据库 (updatedb)
  
   索引构建过程需要遍历整个根文件系统，极消耗资源

    工作特点:
     • 查找速度快
     
      • 模糊查找 
      
      • 非实时查找 
      
      • 搜索的是文件的全路径，不仅仅是文件名 
      
      • 可能只搜索用户具备读取和执行权限的目录

范例：搜索所有以.sh结尾的文件
```bash
[root@centos7 ~]# locate "*.sh"
/boot/grub2/i386-pc/modinfo.sh
/etc/X11/xinit/xinitrc.d/00-start-message-bus.sh
/etc/X11/xinit/xinitrc.d/10-qt5-check-opengl2.sh
/etc/X11/xinit/xinitrc.d/50-xinput.sh
/etc/X11/xinit/xinitrc.d/localuser.sh
/etc/X11/xinit/xinitrc.d/zz-liveinst.sh
/etc/dhcp/dhclient-exit-hooks.d/azure-cloud.sh
/etc/dhcp/dhclient.d/chrony.sh
/etc/kernel/postinst.d/51-dracut-rescue-postinst.sh
....

```
locate命令

locate KEYWORD 有用的选项

 &ensp;&ensp;-i 不区分大小写的搜索

&ensp;&ensp;-n  N 只列举前N个匹配项目

 &ensp;&ensp;-r  使用正则表达式 基本正则表达式

 &ensp;&ensp; 示例 搜索名称或路径中带有“conf”的文件 
  locate  conf 

  使用Regex来搜索以“.conf”结尾的文件

  &ensp;&ensp; locate  -r  ‘\.conf$’

范例： in #  手动指定查看匹配到的前几个
```bash
[root@centos7 ~]# locate -n 3 -i "*.sh"
/boot/grub2/i386-pc/modinfo.sh
/etc/X11/xinit/xinitrc.d/00-start-message-bus.sh
/etc/X11/xinit/xinitrc.d/10-qt5-check-opengl2.sh
```
范例：- r  使用基本正则表达式 查看以.sh 结尾的文件

```bash
[root@centos7 ~]# locate -r "\.sh$"
```

## `find`

实时查找工具，通过遍历指定路径完成文件查找

 工作特点：
 
  &ensp;&ensp;• 查找速度略慢 
  
  &ensp;&ensp;• 精确查找 
  
  &ensp;&ensp;• 实时查找
  
   &ensp;&ensp;• 可能只搜索用户具备读取和执行权限的目录

`find语法：`

 find [OPTION]... [查找路径] [查找条件] [处理动作]

  查找路径：指定具体目标路径；默认为当前目录 
  
  查找条件：指定的查找标准，可以文件名、大小、类型、权限等标准进行； 
  
  默认为找出指定路径下的所有文件
  
   处理动作：对符合条件的文件做操作，默认输出至屏幕

`查找条件`

指搜索层级 

&ensp;&ensp;-maxdepth level 最大搜索目录深度,指定目录为第1级 

&ensp;&ensp;-mindepth level 最小搜索目录深度

&ensp;&ensp;先处理目录内的文件，再处理目录 -depth
 
&ensp;&ensp;根据文件名和inode查找：

 &ensp;&ensp;-name "文件名称"：支持使用glob 
 
&ensp;&ensp;&ensp;&ensp; *, ?, [], [^] 
 
&ensp;&ensp;-iname "文件名称"：不区分字母大小写
 
&ensp;&ensp;-inum #  按inode号查找
  
&ensp;&ensp;-samefile name  相同inode号的文件 
   
&ensp;&ensp;-links #   链接数为n的文件 
   
&ensp;&ensp;-regex “PATTERN”：以PATTERN匹配整个文件路径，而非文件名称

范例： -maxdepth level 指定搜索深度，find默认为递归搜索，先显示目录再显示文件  &ensp;&ensp;搜索/data本身不进入其子目录里面搜索

```bash
[root@centos7 ~]# find /data -maxdepth 2
/data
/data/a.sh
```
```bash
定义最大搜索深度和最小搜索深度
[root@centos7 ~]# find /root -maxdepth 2 -mindepth 2
/root/.cache/dconf
/root/.cache/imsettings
/root/.cache/libgweather
/root/.cache/evolution
/root/.cache/gnome-shell
/root/.cache/tracker
/root/.cache/abrt
```
范例： 查看/data目录下包含test的文件
```bash
[root@centos7 ~]# find /root -name "*test*"
[root@centos7 ~]# 
```
范例：-regex 匹配整个路径以.sh结尾的文件
```bash
[root@centos7 ~]# find /data -regex ".*\.sh$"
/data/a.sh
```

`查找条件`

根据属主、属组查找： 

&ensp;&ensp;-user USERNAME：查找属主为指定用户(UID)的文件

 &ensp;&ensp;-group GRPNAME: 查找属组为指定组(GID)的文件 

 &ensp;&ensp;-uid UserID：查找属主为指定的UID号的文件 

 &ensp;&ensp;-gid GroupID：查找属组为指定的GID号的文件 

 &ensp;&ensp;-nouser：查找没有属主的文件 
 
 &ensp;&ensp;-nogroup：查找没有属组的文件

 `查找条件`

根据文件类型查找：
 &ensp;&ensp;-type TYPE: 

&ensp;&ensp;&ensp;&ensp;f: 普通文件

&ensp;&ensp;&ensp;&ensp;d: 目录文件

&ensp;&ensp;&ensp;&ensp;l: 符号链接文件

&ensp;&ensp;&ensp;&ensp;s：套接字文件

&ensp;&ensp;&ensp;&ensp;b: 块设备文件

&ensp;&ensp;&ensp;&ensp;c: 字符设备文件

&ensp;&ensp;&ensp;&ensp;p: 管道文件
空文件或目录 -empty 

find /app -type d  -empty

## `查找条件`

`组合条件：`
 与：-a       优先级高于 -o

 或：-o 

 非：-not, !  取反

`德·摩根定律`：
 (非 A) 或 (非 B) = 非(A 且 B)

 (非 A) 且 (非 B) = 非(A 或 B)

  示例
 !A -a !B = !(A -o B) 

 !A -o !B = !(A -a B)

范例：所搜根目录下文件名为 .sh结尾的和属组为root的（两个条件默认为并且） -a 也是为并且

```bash
[root@centos7 ~]# find / -name "*.sh" -user root

相等

[root@centos7 ~]# find / -name "*.sh"  -a -user root
```
范例：所搜根目录下文件名为 .sh结尾的或者属组为root的 &ensp;&ensp;-o 为或者的意思 (匹配两个任意变量匹配到的内容)
```bash
[root@centos7 ~]# find / -name "*.sh"  -o -user root
```
范例：范例：所搜根目录下文件名为 .sh结尾的或者属组为root的文件，并且执行ls查看 &ensp;&ensp;-o 为或者的意思 ，如果想让匹配到的两个变量都执行ls 操作需要用  \(  \) 括起来
```bash
[root@centos7 ~]# find  \( -name "*.sh" -o -user root \) -ls
```

范例：查找文件所有者不是wang,且名字不是以.txt结尾的文件
```bash
[root@centos7 ~]# find ! \( -user wang -a -name "*.txt" \)
```
`find示例`

find -name snow.png

find -iname snow.png

find / -name  “*.txt”

find /var –name “*log*”

find -user joe -group joe

find -user joe -not -group joe

find -user joe -o -user jane

find -not \( -user joe -o -user jane \)

find / -user joe -o -uid 500

`find示例`

找出/tmp目录下，属主不是root，且文件名不以f开头的文件
 find /tmp \( -not -user root -a -not -name 'f*' \) -ls 
 
 find /tmp -not \( -user root -o -name 'f*' \) –ls
 
`排除目录`

示例： 查找/etc/下，除/etc/sane.d目录的其它所有.conf后缀的文件 

find /etc -path ‘/etc/sane.d’ -a –prune -o -name “*.conf” 

查找/etc/下，除/etc/sane.d和/etc/fonts两个目录的所有.conf后缀的文件 

find /etc \( -path "/etc/sane.d" -o -path "/etc/fonts" \) -a -prune -o name "*.conf"



`根据文件大小来查找：`
 -size [+|-]#UNIT 
 常用单位：k, M, G，c（byte） 
 
&ensp;&ensp; #UNIT: (#-1, #] 如：6k 表示(5k,6k] 
 
 &ensp;&ensp;-#UNIT：[0,#-1] 如：-6k 表示[0,5k] 
 
&ensp;&ensp; +#UNIT：(#,∞) 如：+6k 表示(6k,∞)
 


范例：查找/下 文件大小问10M的文件

搜索出的文件不是标准的10M 
```bash
[root@centos7 ~]# find / -size 10M -ls 
```
范例： 查找/下文件大小为5M到10M之间的文件

```bash

[root@centos7 ~]# find / \( -size +5M -size -10M \) -ls

```

`根据时间戳：`
 以“天”为单位

  -atime [+|-]#, 

  &ensp;&ensp; #: [#,#+1) 

   &ensp;&ensp;+#: [#+1,∞] 

  &ensp;&ensp; -#: [0,#) 


  &ensp;&ensp; -mtime 

  &ensp;&ensp; -ctime 

   `以“分钟”为单位 `

   &ensp;&ensp;-amin  #

   &ensp;&ensp;-mmin  #

   &ensp;&ensp;-cmin  #
范例： 查找/etc下一分钟修改的时间

```bash
[root@centos7 ~]# find /etc/ -mtime -1 
```
`根据权限查找：`

(默认进入文件夹内搜索)

 -perm [/|-]MODE

&ensp;&ensp;MODE: 精确权限匹配

&ensp;&ensp;/MODE：任何一类(u,g,o)对象的权限中只要能一位匹配即可，或关系，+ （查到的多）    （例如：一个文件所有者u：6 ：110 
   u:7:111 也会被匹配，0为不检查不关心，g:100,g:110 都会被匹配，匹配其中一位满足就可以被查找到）
 从centos7开始淘汰 

&ensp;&ensp;-MODE：每一类对象都必须同时拥有指定权限，与关系（并且） 匹配ugr三位都要有这个权限，查找的少

 &ensp;&ensp;0 表示不关注
    
find -perm 755 会匹配权限模式恰好是755的文件
只要当任意人有写权限时，find -perm +222就会匹配

只有当每个人都有写权限时，find -perm -222才会匹配 

只有当其它人（other）有写权限时，find -perm -002才会匹配

范例：/etc下匹配文件的权限为644的文件
```bash
[root@centos7 ~]# find /etc -perm 644 
```

范例：查找/etc下三者都具有写权限的文件(并且)
```bash
[root@centos7 ~]# find /etc -perm -222
```
## 处理动作
&ensp;&ensp;-print：默认的处理动作，显示至屏幕

 &ensp;&ensp;-ls：类似于对查找到的文件执行“ls -l”命令 

 &ensp;&ensp;-delete：删除查找到的文件 （搜索到不提示，直接删）
 
&ensp;&ensp;-fls file：查找到的所有文件的长格式信息保存至指定文件中

&ensp;&ensp;-ok COMMAND {} \; 对查找到的每个文件执行由COMMAND指定的命令，对于 每个文件执行命令之前，都会交互式要求用户确认

&ensp;&ensp;-exec COMMAND {} \; 对查找到的每个文件执行由COMMAND指定的命令

&ensp;&ensp;&ensp;&ensp;{}: 用于引用查找到的文件名称自身

&ensp;&ensp;find传递查找到的文件至后面指定的命令时，查找到所有符合条件的文件一次性 传递给后面的命令 

范例：查找不是目录的文件且精确匹配222权限的文件
```bash
[root@centos7 ~]# find ！-type d -perm -222 -ls
```
范例：-fls file 搜索到的结果保存到文件中(类似于重顶定向)
```bash
[root@centos7 ~]# find -type d -ls -fls a.txt
```
范例：精确查找权限为444 的文件且名字为.txt结尾的文件，查找到执行移动到root目录下操作
```bash
[root@centos7 ~]# find -perm -444 -name ".*.txt" -ok mv {} /root \;
```
范例：查找名字以.txt结尾的文件并执行复制匹配到的文件并改名为以.bak结尾
```bash
 90  find -name "*.txt" -exec cp {} /root/{}.bak \;
```

`find示例`

备份配置文件，添加.orig这个扩展名

 find  -name  “*.conf”  -exec  cp {}  {}.orig \;

 提示删除存在时间超过３天以上的joe的临时文件

  find /tmp -ctime +3 -user joe -ok rm {} \;

在主目录中寻找可被其它用户写入的文件

 find ~ -perm -002 -exec chmod o-w {} \;

 查找/data下的权限为644，后缀为sh的普通文件，增加执行权限 
  
find /data –type  f -perm 644  -name “*.sh” –exec chmod 755 {} \;
  
查看/home的目录

 find  /home –type d -ls


## `参数替换xargs`
&ensp;&ensp;由于很多命令不支持管道|来传递参数，而日常工作中有这个必要，所以就有了 xargs命令

&ensp;&ensp;xargs用于产生某个命令的参数，xargs 可以读入 stdin 的数据，并且以空格符 或回车符将 stdin 的数据分隔成为arguments 

&ensp;&ensp;注意：文件名或者是其他意义的名词内含有空格符的情况 有些命令不能接受过多参数，命令执行可能会失败，xargs可以解决 

示例：

&ensp;&ensp;ls f* |xargs rm
 
 &ensp;&ensp; find /sbin -perm +700 |ls -l       这个命令是错误的 
  
 &ensp;&ensp; find /sbin -perm +7000 | xargs ls –l    查找特殊权限的文件
  
`find和xargs格式：`

&ensp;&ensp;find | xargs COMMAND

范例：
```bash
[root@centos7 ~]# echo {1..100} | xargs touch 
[root@centos7 ~]# ls
1    18  27  36  45  54  63  72  81  90  anaconda-ks.cfg
10   19  28  37  46  55  64  73  82  91  Desktop
100  2   29  38  47  56  65  74  83  92  Documents
11   20  3   39  48  57  66  75  84  93  Downloads
12   21  30  4   49  58  67  76  85  94  initial-setup-ks.cfg
13   22  31  40  5   59  68  77  86  95  Music
14   23  32  41  50  6   69  78  87  96  Pictures
15   24  33  42  51  60  7   79  88  97  Public
16   25  34  43  52  61  70  8   89  98  Templates
17   26  35  44  53  62  71  80  9   99  Videos
```

