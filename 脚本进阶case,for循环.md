## 条件判断：case语句和for循环
---
title: case条件判断
tags: linux
categories: linux
---

case 变量引用 in 

PAT1) 

分支1 

;; 

PAT2)

分支2 

;; 

... 

*) 

默认分支 

;; 

`esac`

case支持glob风格的通配符： 

&ensp;&ensp;*: 任意长度任意字符 

&ensp;&ensp;?: 任意单个字符 

&ensp;&ensp;[]：指定范围内的任意单个字符 

&ensp;&ensp;a|b: a或b

范例：数字判断

number

1,2,3 cmd1

4,5,6 cmd2

7,8,9 cmd3

10,11 cmd4

other cmd5
```bash
if语句写法
if [ "$number" -eq 1 -o "$number" -eq 2 -o "number" -eq 3 ];then 
cmd1
if [ "$number" -eq 4 -o "$number" -eq 5 -o "$number" -eq 6 ];then 
cmd2

````

范例:菜单
```bash
[root@centos7 daizhe]# cat caidan.sh 
#!/bin/bash
cat << EOF
1:jiaozi
2:baozi
3:mantou
4:lamian
EOF
read -p "请输入你要吃的饭的编号？" fan
case $fan in
1)
	echo "jiaozi 10元"
	;;
2)
	echo "baozi 15元"
	;;
3)
	echo "mantou 11元"
	;;
4)
	echo "lamian 22元"
	;;
*) 
	echo "我们家没有,你想要的"
;;
esac

```
范例：写一个判断输入的yes和no的大小写都可以识别的脚本
```bash
read -p "请输入yes或者no？" yn
yn= `echo $yn |tr 'A-Z' 'a-z'`
case $yn in
y|yes)
echo yes
;;
n|no)
echo no
;;
*)
echo xx
;;
esac
```
方法2：
```bash
[root@centos7 daizhe]# bash yesorno.sh 
请输入yes或者no？yes
yes
[root@centos7 daizhe]# bash yesorno.sh^C
[root@centos7 daizhe]# cat yesorno.sh 
#!/bin/bash
read -p "请输入yes或者no？" yn
case $yn in
[Yy]|[Yy][Ee][Ss])
	echo yes
	;;
[Nn]|[Nn][Oo])
	echo no
	;;
*)
	echo xx
	;;
esac

[root@centos7 daizhe]# yy=yes;[[ $yy =~ ^[Yy]|[Yy][Ee][Ss]$ ]] && echo ture
ture
[root@centos7 daizhe]# yy=yEs;[[ $yy =~ ^（[Yy]|[Yy][Ee][Ss]）$ ]] && echo ture
ture
[root@centos7 daizhe]# yy=yEs;[[ $yy =~ ^[Yy]([Ee][Ss])?$ ]] && echo ture
括号内是一个整体，？为括号内可有可无
```
## 循环

循环执行 

&ensp;&ensp;将某代码段重复运行多次 

&ensp;&ensp;重复运行多少次 

&ensp;&ensp;&ensp;&ensp;循环次数事先已知 

&ensp;&ensp;&ensp;&ensp;循环次数事先未知 

&ensp;&ensp;有进入条件和退出条件 

for, while, until

## for循环


for 变量名（变量的名字，不是变量引用，不需要加$） in 列表(多少个单词，决定循环多少次，变量名第一次的值，取决于列表第一个单词的值，第二次执行，变量名为列表中第二个单词的值);do （do和done之间的指令）

&ensp;&ensp;&ensp;&ensp;循环体 

&ensp;&ensp;done 

执行机制： 依次将列表中的元素赋值给“变量名”; 每次赋值后即执行一次循环体; 直 到列表中的元素耗尽，循环结束

列表生成方式： 

&ensp;&ensp;(1) 直接给出列表 

&ensp;&ensp;(2) 整数列表： 

&ensp;&ensp;&ensp;&ensp;(a) {start..end} 
&ensp;&ensp;&ensp;&ensp;(b) $(seq [start [step]] end) 

&ensp;&ensp;(3) 返回列表的命令 

&ensp;&ensp;&ensp;&ensp;$(COMMAND)、``调用命令

&ensp;&ensp;(4) 使用glob，如：*.sh 

&ensp;&ensp;(5) 变量引用； $@, $*

## for特殊格式

`双小括号方法，即((…))格式，也可以用于算术运算` 

双小括号方法也可以使bash Shell实现C语言风格的变量操作 

&ensp;&ensp;I=10 

&ensp;&ensp;((I++)) 

for循环的特殊格式： 

&ensp;&ensp;`for ((控制变量初始化;条件判断表达式;控制变量的修正表达式)) `

&ensp;&ensp;`do` 

&ensp;&ensp;&ensp;&ensp;`循环体 `

&ensp;&ensp;`done `

控制变量初始化：仅在运行到循环代码段时执行一次 

控制变量的修正表达式：每轮循环结束会先进行控制变量修正运算，而后再做 条件判断

理解for的循环含义
```bash[root@centos7 daizhe]# for num in 1 3 7 0 2 a x  ;do echo mun is $num;done
mun is 1
mun is 3
mun is 7
mun is 0
mun is 2
mun is a
mun is x

[root@centos7 daizhe]# for num in `seq 10` ;do echo num is $num ;done
num is 1
num is 2
num is 3
num is 4
num is 5
num is 6
num is 7
num is 8
num is 9
num is 10

支持步跳，例如`seq 10 2 30` 10-30 步跳为2 
[root@centos7 daizhe]# for aa in `seq 10 2 30` ;do echo aa is $aa ;done
aa is 10
aa is 12
aa is 14
aa is 16
aa is 18
aa is 20
aa is 22
aa is 24
aa is 26
aa is 28
aa is 30

[root@centos7 daizhe]# for aa in *.sh ;do echo filename is $aa ;done
filename is caidan.sh
filename is case.sh
filename is chengji2.sh
filename is chengji.sh
filename is user.sh
filename is yesorno.sh

```
范例：算数从1+~100
```bash

[root@centos7 daizhe]# echo {1..100} | tr ' ' '+' | bc
5050

[root@centos7 daizhe]# cat saunshu.sh 
#!/bin/bash
sum=0
for i in {1..100} ;do
	let sum=sum+i 
done
echo sum=$sum
[root@centos7 daizhe]# bash saunshu.sh 
sum=5050
```
 范例：
 猴子第一天摘下若干个桃子，当即吃了一半，还不过瘾，又多吃了一个，第二天早上又将剩下的桃子吃掉一半，又多吃了一个。以后的每天早上都吃了前一天上下的一半零一个。到了第10天早上再想吃一个时，只剩下一个桃子了。求第一天共摘了多少？
```bash
[root@centos7 daizhe]# !vim
vim houzi.sh

#!/bin/bash
sum=1
for i in {9..1} ;do
let sum=(sum+1)*2
done
echo "$sum"

[root@centos7 daizhe]# cat houzi2.sh 
#!/bin/bash
read -p "天数：" day
read -p "剩余：" sum

let day=day-1
for i in `seq 1 $day`
	do
		let sum=(sum+1)*2
done
echo "桃子$sum"

```
范例：利用脚本实现将当前目录下的所有文件，后缀为所有文件变成.log后缀的文件

```bash
[root@centos7 aa]# ls
aa.ping  a.c.d.txt  a.txt  log.sh  qq.png
取文件名称
[root@centos7 aa]# ls | sed -nr 's/(.*)\..*/\1/p'
aa
a.c.d
a
log
qq
[root@centos7 aa]# cat test.log 
#!/bin/bash
for i in /aa/* ;do
	mnu=`echo $i | sed -nr 's/(.*)\..*/\1/p'`
	mv $i $mnu.log
done
[root@centos7 aa]# ls
aa.log  a.c.d.log  a.log  log.log  qq.log  test.log


搜索当前目录下的文件将jpg文件后缀的文件改成log
[root@centos7 aa]# ls
aa.log  a.c.d.log  a.log  log.log  qq.log  test.log
[root@centos7 aa]# rename "log" "txt" *
[root@centos7 aa]# ls
aa.txt  a.c.d.txt  a.txt  qq.txt  test.txt  txt.log


截取文件后缀，并且去重
[root@centos7 /data/a5]# ls | sed -nr 's/(.*)\.(.*)/\2/p' | sort -u

替换文件名字的后缀
[root@centos7 /data/a5]# for i in `ls | sed -nr 's/.*\.(.*)/\1/p'`;do rename "$i" ".sh" *;done
[root@centos7 /data/a5]# ls
chengji.....sh              wenjianming....sh  yes.....sh
tihuanwenjianhouzui.....sh  yes2.....sh

```
范例：

编写脚本，提示请输入地址ip，如192.168.0.0，判断输入的网段中的主机在线状态

（扫描网络中在线的主机）主机ip 1~254
```bash
初步：实现扫描网络主机是否通，但是效率低下，进程停不下来
[root@centos7 /data/a5]# cat ping.sh 
#!/bin/bash
netid=172.18.34
for i in {1..154};do
if ping -c 1  $netid.$i &> /dev/null;then
	echo "this computer is $netid.$i tong...."
else 
	echo "this computer is $netid.$i done...."
fi
done


改善：将ping的过程并行（由于是并行所以顺序就不规范）
[root@centos7 /data/a5]# cat ping.sh 
#!/bin/bash
netid=172.18.34
for i in {1..154};do
{
if ping -c 1  $netid.$i &> /dev/null;then
	echo "this computer is $netid.$i tong...."
else 
	echo "this computer is $netid.$i done...."
fi
}&
done
wait（执行完退出）

改善：
根据用户自己的输入的网段来扫描输入的网段来判断网段的主机是否在线
，并且将可以访问的ip放进文件中，方便查看
[root@centos7 /data/a5]# cat ping2.sh 
#!/bin/bash
read -p "please input netid(sge:192.168.52.0:)" netid
netid=`echo "$netid" | sed -nr 's/(.*)\..*/\1/p'`
echo netid=$netid
for id in {1..254};do 
	{
	if ping  -c1 -w1 $netid.$id &> /dev/null;then
		echo "$netid.$id id upping..." >> /tmp/ping.txt
	else
		echo "$netid.$id is doning...."
	fi
	}&
done
wait

```
范例：

循环嵌套，实现打印矩形
```bash
[root@centos7 /data/a5]# !vim
vim juxing.sh

  1 #!/bin/bash
  2 read -p "请输入列数：" col
  3 read -p "请输入行号：" line
  4 for i in `seq $line`;do
  5         for j in `seq $col`;do
  6                 echo -n "*"
  7         done
  8         echo
  9 done


继续改进：
生成随机颜色、高亮、闪烁、
[root@centos7 /data/a5]# cat juxing.sh 
#!/bin/bash
read -p "请输入列数：" col
read -p "请输入行号：" line
for i in `seq $line`;do
	for j in `seq $col`;do
		COLOR=$[RANDOM%7+31]
		echo -e "\033[1;5;${COLOR}m*\033[0m\c"	
	done
	echo
done

改进：
实现外围的星星闪烁，内部的星星不闪烁
判断行是不是第一行和最后一行，判断列是不是第一列和最后一列

方法1：
[root@centos7 /data/a5]# cat juxing.sh 
#!/bin/bash
read -p "请输入列数：" col
read -p "请输入行号：" line
for i in `seq $line`;do
	for j in `seq $col`;do
		COLOR=$[RANDOM%7+31]
	if [ $i -eq 1 -o $i -eq $line -o $j -eq 1 -o $j -eq $col ];then
		echo -e "\033[1;5;${COLOR}m*\033[0m\c"
	else 
		echo -e "*\c"
		fi
	done
	echo
done

解析：if [ $i -eq 1 -o $i -eq $line -o $j -eq 1 -o $j -eq $col ];then

第一行和最后一行，第一列和最后一列都打印颜色，如果不是则不打印颜色。

方法2：
for嵌套加case嵌套实现

[root@centos7 /data/a5]# !vim
vim juxing2.sh

  1 #!/bin/bash
  2 read -p "请输入列数：" col
  3 read -p "请输入行号：" line
  4 for i in `seq $line`;do
  5         for j in `seq $col`;do
  6                 case $i in
  7                 1|$line)
  8                         COLOR=$[RANDOM%7+31]
  9                         echo -e "\033[1;5;${COLOR}m*\033[0m\c"
 10                         ;;
 11                 *)
 12                         case $j in
 13                         1|$col)
 14                                 COLOR=$[RANDOM%7+31]
 15                                 echo -e "\033[1;5;${COLOR}m*\033[0m\c"
 16                                 ;;
 17                         *)
 18                                 echo -e "*\c"
 19                                 ;;
 20                         esac
 21                 esac
 22         done
 23         echo
 24 done

```
范例：
打印等腰三角形

每行星星的算法

1 1  

2 3 2*2-1

3 5 3*2-1

4 7 4*2-1

5 9 5*2-1

行*2-1=星的个数
```bash
目标演示：
[root@centos7 /data/wang/script]# ./triangleTree30.sh 
please one number,then print triangle:3
  *
 ***
*****

操作
第一步：
[root@centos7 /data/a5]# cat sanjiao.sh 
#!/bin/bash
read -p "请输入行数: " line
for i in `seq $line`;do
	#xing 的个数
	let xing=i*2-1
	for j in `seq $xing`;do
		echo -e "*\c" 
	done
	#echo 换行
	echo
done
[root@centos7 /data/a5]# ./sanjiao.sh 
请输入行数: 10
*
***
*****
*******
*********
***********
*************
***************
*****************
*******************


第二步：
解决星的位置问题
解决空格的个数
空格的个数与行的关系
第一行5个空格
第二行4个空格
...
最后一行一个空格
最后一行0个空格

结论：空格数=总行数-当前行号

[root@centos7 /data/a5]# cat sanjiao2.sh 
#!/bin/bash
read -p "请输入行数: " line
for i in `seq $line`;do
	#xing 的个数
	let xing=i*2-1
	#空格的个数=行号减去当前行
	let space=$line-$i
	for j in `seq $space`;do
		echo -n " "
	done
	for k in `seq $xing`;do
		echo -n "*"
	done
	#echo 换行
	echo
done
[root@centos7 /data/a5]# ./sanjiao2.sh 
请输入行数: 10
         *
        ***
       *****
      *******
     *********
    ***********
   *************
  ***************
 *****************
*******************
```

范例：

打印九九乘法表
```bash
方法1：

[root@centos7 /data/a5]# cat jiujiu.sh 
#!/bin/bash
for i in  {1..9};do
for j in {1..9};do
		#-le为是否小于等于
		if [ $j -le $i ];then 
		echo -e "$j*$i=$[i*j]\t\c"
		fi
	done
	echo
done
[root@centos7 /data/a5]# ./jiujiu.sh 
1*1=1	
1*2=2	2*2=4	
1*3=3	2*3=6	3*3=9	
1*4=4	2*4=8	3*4=12	4*4=16	
1*5=5	2*5=10	3*5=15	4*5=20	5*5=25	
1*6=6	2*6=12	3*6=18	4*6=24	5*6=30	6*6=36	
1*7=7	2*7=14	3*7=21	4*7=28	5*7=35	6*7=42	7*7=49	
1*8=8	2*8=16	3*8=24	4*8=32	5*8=40	6*8=48	7*8=56	8*8=64	
1*9=9	2*9=18	3*9=27	4*9=36	5*9=45	6*9=54	7*9=63	8*9=72	9*9=81

方法2：

[root@centos7 /data/a5]# cat jiujiu2.sh 
#!/bin/bash
for i in {1..9};do
		for j in `seq $i`;do
		let mnu=i*j
		echo -e "${j}X${i}=$mnu\t\c"
	done
	echo
done
[root@centos7 /data/a5]# ./jiujiu2.sh 
1X1=1	
1X2=2	2X2=4	
1X3=3	2X3=6	3X3=9	
1X4=4	2X4=8	3X4=12	4X4=16	
1X5=5	2X5=10	3X5=15	4X5=20	5X5=25	
1X6=6	2X6=12	3X6=18	4X6=24	5X6=30	6X6=36	
1X7=7	2X7=14	3X7=21	4X7=28	5X7=35	6X7=42	7X7=49	
1X8=8	2X8=16	3X8=24	4X8=32	5X8=40	6X8=48	7X8=56	8X8=64	
1X9=9	2X9=18	3X9=27	4X9=36	5X9=45	6X9=54	7X9=63	8X9=72	9X9=81	

倒着的九九乘法表
[root@centos7 /data/a5]# cat daojiujiu.sh 
#!/bin/bash
for i in {9..1};do
	for j in `seq $i`;do
		echo -e "$j*$i=$[j*i]\t\c"
	done
	echo
done
[root@centos7 /data/a5]# bash daojiujiu.sh 
1*9=9	2*9=18	3*9=27	4*9=36	5*9=45	6*9=54	7*9=63	8*9=72	9*9=81	
1*8=8	2*8=16	3*8=24	4*8=32	5*8=40	6*8=48	7*8=56	8*8=64	
1*7=7	2*7=14	3*7=21	4*7=28	5*7=35	6*7=42	7*7=49	
1*6=6	2*6=12	3*6=18	4*6=24	5*6=30	6*6=36	
1*5=5	2*5=10	3*5=15	4*5=20	5*5=25	
1*4=4	2*4=8	3*4=12	4*4=16	
1*3=3	2*3=6	3*3=9	
1*2=2	2*2=4	
1*1=1	
```

范例：

打印国际象棋

行&ensp;&ensp;&ensp;&ensp;列&ensp;&ensp;&ensp;&ensp;颜色

1&ensp;&ensp;&ensp;&ensp;1&ensp;&ensp;&ensp;&ensp;白

1&ensp;&ensp;&ensp;&ensp;2&ensp;&ensp;&ensp;&ensp;黑

1&ensp;&ensp;&ensp;&ensp;3&ensp;&ensp;&ensp;&ensp;白

1&ensp;&ensp;&ensp;&ensp;4&ensp;&ensp;&ensp;&ensp;黑

2&ensp;&ensp;&ensp;&ensp;1&ensp;&ensp;&ensp;&ensp;黑

2&ensp;&ensp;&ensp;&ensp;2&ensp;&ensp;&ensp;&ensp;白

2&ensp;&ensp;&ensp;&ensp;3&ensp;&ensp;&ensp;&ensp;黑

2&ensp;&ensp;&ensp;&ensp;4&ensp;&ensp;&ensp;&ensp;白

行号加列号偶数为白、奇数为黑
```bash
[root@centos7 /data/a5]# cat xiangqi.sh 
#!/bin/bash
for i in {1..8};do
		for j in {1..8};do
		#对行和列取模%   K为取模的值
		#对2取模，不是0 就是1 
		#偶数47背景白色
		#如果不是则打印黑色
		k=$[(j+i)%2]
		if [ $k -eq 0 ];then
			echo -e "\033[47m  \033[0m\c"
		else
			echo -e "  \c"
		fi
	done
	echo
done

```
![国际象棋](https://img-blog.csdnimg.cn/20181106162358832.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzU1MTE1Mg==,size_16,color_FFFFFF,t_70)


`time bash 脚本` 可以检测脚本的性能速度

