---
title: if条件判断
tags: linux
categories: linux
---
## 条件选择if语句

选择执行：

注意：if语句可嵌套

`单分支 `

&ensp;&ensp;if 判断条件;then 

条件为真的分支代码 

fi 

`双分支 `

if 判断条件; then 

&ensp;&ensp;条件为真的分支代码 

else 

&ensp;&ensp;条件为假的分支代码

fi

`多分支`

if 判断条件1; then 

&ensp;&ensp;条件1为真的分支代码 

elif 判断条件2; then 

&ensp;&ensp;条件2为真的分支代码 

elif 判断条件3; then 

&ensp;&ensp;条件3为真的分支代码 

else 以上条件都为假的分支代码 

fi 

`逐条件进行判断，第一次遇为“真”条件时，执行其分支，而后结束整个if语句`

范例：成绩判断的脚本
```bash
[root@centos7 daizhe]# !vim
vim chengji.sh

#!/bin/bash
read -p "请输入你的成绩:" chengji

if [ "$chengji" -lt 0 -o "$chengji" -gt 100 ];then
        echo "成绩错误"
                exit 10
elif [ "$chengji" -lt 60 ];then
        echo "成绩有点低"
elif [ "$chengji" -lt 80 ];then
        echo "成绩还可以"
else
        echo "高成绩啊"
fi

方法一修复：万一输入的是字母怎么办？精确匹配
if嵌套方法
[root@centos7 daizhe]# cat chengji.sh 
#!/bin/bash
read -p "请输入你的成绩:" chengji
if [[ "$chengji" =~ ^[[:digit:]]+$ ]];then
	if [ "$chengji" -lt 0 -o "$chengji" -gt 100 ];then 
		echo "成绩错误"
		exit 10
	elif [ "$chengji" -lt 60 ];then
		echo "成绩有点低"
	elif [ "$chengji" -lt 80 ];then
		echo "成绩一般"
 	elif [ "$chengji" -lt 90 ];then
		echo "加油100在和你招手"
	else
		echo "很好，你是最棒的"
	fi
else
	echo "请输入正确的数字？"
fi

方法2：修复
if多分支
[root@centos7 daizhe]# !vim
vim chengji2.sh

#!/bin/bash
read -p "请输入成绩？" fen
if [[ ! "$fen" =~ ^[[:digit:]]+$ ]];then
        echo "请输入正确的成绩格式"
                exit 20
elif [ "$fen" -gt 100 ];then
        echo "成绩错误"
                exit 10
elif [ "$fen" -lt 60 ];then
        echo "成绩低"
elif [ "$fen" -lt 80 ];then
        echo "成绩一般"
elif [ "$fen" -lt 90 ];then
        echo "进军100"
else
        echo "厉害"
fi

```

If示例
```bash
根据命令的退出状态来执行命令 

if ping -c1 -W2 station1 &> /dev/null; then 

    echo 'Station1 is UP'  

elif grep "station1" ~/maintenance.txt &> /dev/null;  then 

     echo 'Station1 is undergoing maintenance‘ 

else 

     echo 'Station1 is unexpectedly DOWN!' 

     exit 1 

fi
```

