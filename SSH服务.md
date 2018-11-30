---
title: ssh服务
tags: linux服务
categories: linux服务
---
## SSH 服务
ssh: secure shell, protocol, 22/tcp, 安全的远程登录 

具体的软件实现：  

&ensp;&ensp;OpenSSH: ssh协议的开源实现，CentOS默认安装  

&ensp;&ensp;dropbear：另一个开源实现 

SSH协议版本  

&ensp;&ensp;v1: 基于CRC-32做MAC，不安全；man-in-middle  

&ensp;&ensp;`v2：`双方主机协议选择安全的MAC方式  

&ensp;&ensp;基于DH算法做密钥交换，基于RSA或DSA实现身份认证 

两种方式的用户登录认证：  

&ensp;&ensp;基于password  

&ensp;&ensp;基于key

## Openssh软件组成

OpenSSH介绍

相关包：  

&ensp;&ensp;openssh  客户端服务端都安装的

&ensp;&ensp;openssh-clients  客户端安装的

&ensp;&ensp;openssh-server   服务端安装的

工具：  

&ensp;&ensp;&ensp;&ensp;基于C/S结构 

&ensp;&ensp;&ensp;&ensp;Client: ssh, scp, sftp，slogin      

&ensp;&ensp;Windows客户端：     

&ensp;&ensp;&ensp;&ensp;xshell, putty, securecrt, sshsecureshellclient  

&ensp;&ensp;Server: sshd 

范例：ssh远程连接主机的安全过程
```bash
服务器端和客户端首次链接，服务端就要将自己的公钥＋会话id传给客户端，客户端要拿得到的公钥和id做异或，得到res值，客户端再服务端的私钥去加密res传送给服务端，服务端得到客户端发来的数据，拿自己的私钥去解密，再拿会话id和res去做异或，得到客户端的公钥，此时服务端就有了自己的公钥和客户端的私钥，此时传输数据就达到了安全的加密传输。

客户端确定第一次连接服务端，需要确定得到的公钥的安全
[root@centos7 ~]# ssh 192.168.131.129
The authenticity of host '192.168.131.129 (192.168.131.129)' can't be established.
RSA key fingerprint is SHA256:UXGG1lxW5SEZHzcXUY+uxvN/TfHiWAsxFwxotFUhFdM.
RSA key fingerprint is MD5:81:4f:63:e3:15:95:f8:86:2e:91:0b:55:4f:26:db:9e.
Are you sure you want to continue connecting (yes/no)?   真的相信得到的公钥就是对方主机的公钥么？

如果要求确认的话，就要坐在服务端去拿自己的公钥做哈希，进行比对
目前身份为服务端
[root@centos6 ~]# cd /etc/ssh
[root@centos6 ssh]# ls
-rw-------. 1 root root   1675 Oct 14 22:35 ssh_host_rsa_key
-rw-r--r--. 1 root root    382 Oct 14 22:35 ssh_host_rsa_key.pub
[root@centos6 ssh]# cp ssh_host_rsa_key.pub /data/
[root@centos6 ssh]# cd /data
[root@centos6 data]# ls
-rw-r--r--. 1 root root   382 Nov 15 17:49 ssh_host_rsa_key.pub

将此文件开头的算法删除，和文件最后的回车换行，目前为base64算法，解码base64转换为原有数据，做MD5哈希运算
[root@centos6 data]# vi ssh_host_rsa_key.pub 

AAAAB3NzaC1yc2EAAAABIwAAAQEAsI/zoWjoP2BVIGvu7Dywt2k+nD8OtDrbLkoZHotUZIbucxOvC1fMvzDLWOh99ErOPjX5oDPilEg+PR5lcKSWUfClHSkOsfakOxDqNzrHukV5y1kmJxCBmxxrGy05SZbAMlblYbCtGLA5YKSVBij5mh3XpGmy0F2LkGNTKWiqW0cXUBUothI2M0OK5gSYz5/Srpx5t5q2ZpdYC1YBh1avUx3kQyicaJKbSXXEUSdMbyxEhznic+v7Yd5HGQVnccLhY9c3S1LinK8kQsV6N7CkayaHCiA8D1C0vDU+/ayTXfDA420xdS/6HzlqI3ul8UEe4zVCDq4VyAzMtitApOMxRw==        
[root@centos6 data]# base64 -d ssh_host_rsa_key.pub > pubkey
[root@centos6 data]# cat pubkey    真实的公钥
ssh-rsa#°󠩨?`U k謁°·i>?´:ٮJTd�¯
                              W̿0ʘ羴J̾5򴢔H>=ep¤Q𣛩±򢹐赺ǺEyʙ&'kI󿾲V䡰­°9`¤(𞗤i²ϝcS)hª[GP(¶63C䄘ϟҮy·¶fX
                 VV¯SとhIuÑ'Lo,D9ᴫ򝅙gqácշKR✯$Bĺ7°¤k& 
 <P´¼5>򞱀⫱u/񸨣{¥󿟣5B® 
                  Ɍ¶+@¤ᰇ
拿哈希完的结果，去和第一次客户端连接服务端第一次提示的MD5哈希算法结果去对比
[root@centos6 data]# md5sum pubkey 
814f63e31595f8862e910b554f26db9e  pubkey

敲过yes的远程主机的公钥已经被服务端所记录
[root@centos7 ~]# cat .ssh/known_hosts 
192.168.131.129 ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAsI/zoWjoP2BVIGvu7Dywt2k+nD8OtDrbLkoZHotUZIbucxOvC1fMvzDLWOh99ErOPjX5oDPilEg+PR5lcKSWUfClHSkOsfakOxDqNzrHukV5y1kmJxCBmxxrGy05SZbAMlblYbCtGLA5YKSVBij5mh3XpGmy0F2LkGNTKWiqW0cXUBUothI2M0OK5gSYz5/Srpx5t5q2ZpdYC1YBh1avUx3kQyicaJKbSXXEUSdMbyxEhznic+v7Yd5HGQVnccLhY9c3S1LinK8kQsV6N7CkayaHCiA8D1C0vDU+/ayTXfDA420xdS/6HzlqI3ul8UEe4zVCDq4VyAzMtitApOMxRw==
````
范例：冒充其他客户端去连接服务端
```bash
偷到连结过的客户端私钥，将偷到的私钥替换为本机的私钥，注意原有属性，就可以冒充客户端的身份，连接服务端
```


## ssh客户端

`客户端组件：` 

`ssh, 客户端配置文件：/etc/ssh/ssh_config  ` 

`ssh, 服务端配置文件：/etc/ssh/sshd_config`

&ensp;&ensp;Host PATTERN   

&ensp;&ensp;StrictHostKeyChecking no 首次登录不显示检查提示 

格式：

&ensp;&ensp;ssh [user@]host [COMMAND]      

&ensp;&ensp;ssh [-l user] host [COMMAND] 

`常见选项 ` 

&ensp;&ensp;-p port：远程服务器监听的端口  

&ensp;&ensp;-b：指定连接的源IP  (指定哪个ip去连接对方)

&ensp;&ensp;-v：调试模式  

&ensp;&ensp;-C：压缩方式  

&ensp;&ensp;-X：支持x11转发  

&ensp;&ensp;-t：强制伪tty分配   

&ensp;&ensp;&ensp;&ensp;ssh -t remoteserver1 ssh  -t remoteserver2  ssh remoteserver3

## ssh客户端 

&ensp;&ensp;允许实现对远程系统经验证地加密安全访问 当用户远程连接ssh服务器时，会复制ssh服务器

&ensp;&ensp;/etc/ssh/ssh_host*key.pub （CentOS7默认是ssh_host_ecdsa_key.pub）文件中的公钥到客户机的 ~./ssh/know_hosts中。下次连接时，会自动匹配相应私钥，不能匹配，将拒 绝连接 
 
## ssh服务登录验证 

ssh服务登录验证方式： 


&ensp;&ensp;用户/口令   

&ensp;&ensp;基于密钥 

基于用户和口令登录验证 

&ensp;&ensp;1 客户端发起ssh请求，服务器会把自己的公钥发送给用户 

&ensp;&ensp;2 用户会根据服务器发来的公钥对密码进行加密 

&ensp;&ensp;3 加密后的信息回传给服务器，服务器用自己的私钥解密，如果密码正确，则 用户登录成功 

![基于用户口令登陆验证](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/QQ%E6%88%AA%E5%9B%BE20181116192250.png)


## `ssh服务基于key验证`

只要走ssh协议的都不用设置密码

基于密钥的登录方式 ：针对主机上的单个用户

1 首先在客户端生成一对密钥（ssh-keygen） 

2 并将客户端的公钥ssh-copy-id 拷贝到服务端 

3 当客户端再次发送一个连接请求，包括ip、用户名 

4 服务端得到客户端的请求后，会到authorized_keys中查找，如果有响应的IP 和用户，就会随机生成一个字符串，例如：acdf 

5 服务端将使用客户端拷贝过来的公钥进行加密，然后发送给客户端 

6 得到服务端发来的消息后，客户端会使用私钥进行解密，然后将解密后的 字符串发送给服务端 

7服务端接受到客户端发来的字符串后，跟之前的字符串进行对比，如果一致， 就允许免密码登录 
 
范例：ssh服务基于key验证连接
```bash
实验准备：想要实现centos7客户端连接centos6服务端免用户口令验证基于key验证
第一步：在centos7客户端上生成公钥私钥对
[root@centos7 ~]# ssh-keygen
Generating public/private rsa key pair.(默认rsa算法)
Enter file in which to save the key (/root/.ssh/id_rsa): （默认生成的公钥私钥对放置路径）
Enter passphrase (empty for no passphrase): （保护私钥的安全是否使用对称密钥给私钥加密）
Enter same passphrase again: （回车）
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:WW/HeL7GsUHAEOcNO3SveaGWJK7ab1hogCniSIW2Xiw root@centos7.com
The key's randomart image is:
+---[RSA 2048]----+
|  .       o++ .  |
| o .       +o= . |
|. +   o   ..+o...|
| E + o . o..o++o.|
|= + .   S ..+=* .|
|.o       o.o.+o. |
|        ..o  ..+ |
|        o. .  +. |
|       . .o. ..  |
+----[SHA256]-----+
[root@centos7 ~]# ll /root/.ssh/
-rw-------. 1 root root 1675 Nov 15 11:25 id_rsa
-rw-r--r--. 1 root root  398 Nov 15 11:25 id_rsa.pub

第二步：将centos7客户端的公钥发送给centos6服务端
[root@centos7 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub 192.168.131.129
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@192.168.131.129's password: 

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh '192.168.131.129'"
and check to make sure that only the key(s) you wanted were added.

服务端查看是否收到客户端传来的公钥
[root@centos6 ~]# ll /root/.ssh/authorized_keys 
-rw-------. 1 root root 398 Nov 15 19:36 /root/.ssh/authorized_keys
[root@centos6 ~]# cat /root/.ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCcOuK1ykJil51kUKkCFX+e2VIWiEgrIulBAkeOv+S+/2aJzX6uNvrL1EK8rVuJDNAE4hr8gEfNXvhKajJRSPZR6WojBpdCuocS7rn6DLmdUcUzPtbd4LWLttyVF6VJ+ilfvvJsvGs6vX828/ix7xRR2H+Z4gt/IOMO+961VEUVNRCqyYoW9u6UgygzsDkoR01Do7Z8syoGllyTnbIRLz3xHjdOHL85u8hzrkXZG6h+eTMQJ+LqB/zivE8JHCJBlWkYktYXLsfFvdwb3VRMGsytm5tXhFp5NKjsWgEVQUCNWnmDs9tDr0FiL1DKAy2Y3n+Fi+vb+OQlnz+GVRkX8MN5 root@centos7.com

第三步：centos7客户端登陆centos6服务端验证
[root@centos7 ~]# ssh 192.168.131.129
Last login: Thu Nov 15 18:10:59 2018 from 192.168.131.178
[root@centos6 ~]# 
```
![服务登陆验证](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/ssh%E6%9C%8D%E5%8A%A1%E9%AA%8C%E8%AF%81%E7%99%BB%E9%99%86.png)

## 基于key认证

基于密钥的认证： 

&ensp;&ensp;(1) 在客户端生成密钥对  

&ensp;&ensp;&ensp;&ensp;ssh-keygen -t rsa [-P ''] [-f “~/.ssh/id_rsa"]

&ensp;&ensp;(2) 把公钥文件传输至远程服务器对应用户的家目录  （此程序进制将客户端的私钥传送到服务端，如果你发的时候写的是私钥，发送到服务端的也是公钥）

&ensp;&ensp;&ensp;&ensp;ssh-copy-id [-i [identity_file]] [user@]host 

&ensp;&ensp;(3) 测试 

&ensp;&ensp;(4) 在SecureCRT或Xshell实现基于key验证 在SecureCRT工具—>创建公钥—>生成Identity.pub文件 转化为openssh兼容格式（适合SecureCRT，Xshell不需要转化格式），并复制到 需登录主机上相应文件authorized_keys中,注意权限必须为600，在需登录的ssh 主机上执行： ssh-keygen  -i -f Identity.pub >> .ssh/authorized_keys 

&ensp;&ensp;(5)重设私钥口令：  

&ensp;&ensp;&ensp;&ensp;ssh-keygen –p  

&ensp;&ensp;(6)验证代理（authentication agent）保密解密后的密钥 

&ensp;&ensp;&ensp;&ensp;这样口令就只需要输入一次 

&ensp;&ensp;&ensp;&ensp;在GNOME中，代理被自动提供给root用户 

&ensp;&ensp;&ensp;&ensp;否则运行ssh-agent bash 

&ensp;&ensp;(7)钥匙通过命令添加给代理  

&ensp;&ensp;&ensp;&ensp;ssh-add 

&ensp;&ensp;（8）接手新机器清理key验证登陆

&ensp;&ensp;&ensp;&ensp;清理/root/.ssh/authorized_keys文件

范例：使用上述基于key验证，批量在其他主机创建账号
```bash
[root@centos6 ~]# cat iplist.txt 
172.18.0.1
172.18.0.2
172.18.0.3
[root@centos6 ~]# cat user.sh 
#!/bin/bash
while read line do
	ssh $line useradd httpduser
done < iplist.txt
```
范例：虽然方便例了但是，如果获取管理者的密钥的话，就等同于也可以管理管理者主机可以管理的主机，这样可以对管理的密钥进行加密，保障管理者的密钥不被泄露
```bash
[root@centos7 ~]# ssh-keygen -p
Enter file in which the key is (/root/.ssh/id_rsa): (是否对此密钥加密)
Enter new passphrase (empty for no passphrase): （加密密码）
Enter same passphrase again:（确认加密密码） 
Your identification has been saved with the new pas
[root@centos7 ~]# cat /root/.ssh/id_rsa
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,4156470F697B4DAE17684EB28EE86928
sphrase.
以后只要用到私钥就要输入加密口令
此时管理下面的主机都要用到次口令

也可以将管理机此口令进行托管，避免每次进行管路其他主机进行口令的输入（代理程序仅记录一次，下次退出重新输入）
[root@centos7 ~]# ssh-agent bash
[root@centos7 ~]# ssh-add
Enter passphrase for /root/.ssh/id_rsa: （输入设定的加密密码）
Identity added: /root/.ssh/id_rsa (/root/.ssh/id_rsa)
```
范例：脚本实现批量用户基于key验证登陆
```bash
[root@centos6 ~]# cat iplist.txt 
172.18.0.1
172.18.0.2
172.18.0.3
[root@centos6 ~]# vim createkey.sh

#!/bin/bash
user=daizhe    
password=daizhe123
ssh-keygen -t rsa -p "" -f /root/.ssh/id_rsa

while read ip ;do
expect << EOF
set timeout 10
expect ssh-copy-id -i /root/.ssh/id_rsa.pub $user@$ip
expect {
"yes/no" { send "yes\n";exp_continue }
"password" { send "$password\n" }
}
expect eof
EOF
done < iplist.txt
~                     
```
范例：实现N台机器互相ssh连接实现key验证
```bash
假如此时仅有两台主机
方法1：

在每台主机上生成自己的公钥和私钥
[root@centos7 ~]# ssh-keygen  -t rsa -P "" -f /root/.ssh/id_rsa
Generating public/private rsa key pair.
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:n5qefNZE/mhsaHzQoAECq9DUD6yAedTLmAz39xrfpBk root@centos7.com
The key's randomart image is:
+---[RSA 2048]----+
|.o+=             |
|=oo.* .          |
|.*o* = .         |
|..= + o . . .    |
|.    . .So =     |
|      . E.o.+    |
|       + Bo* o   |
|      ..++B B .  |
|       .*+ +     |
+----[SHA256]-----+

[root@centos6 ~]# ssh-keygen  -t rsa -P "" -f /root/.ssh/id_rsa
Generating public/private rsa key pair.
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
bd:14:50:4d:55:f8:70:7a:d1:f4:07:13:34:76:e7:7c root@centos6.com
The key's randomart image is:
+--[ RSA 2048]----+
|        ...o.oO=*|
|         .  ..oO=|
|          .    =E|
|         . .  . =|
|        S o    . |
|         . .     |
|          .      |
|                 |
|                 |
+-----------------+

连到同一台主机上
[root@centos7 ~]# ssh-copy-id 192.168.131.178
[root@centos6 ~]#  ssh-copy-id 192.168.131.178

此时被链接的主上存放了所有主机连接的公钥
[root@centos7 ~]# cat /root/.ssh/authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAvEPfDGOBh1wV59fF9fnrQMv45tPB4zR96v8xvsHQGhqy7/v8IZ7pJ/GWIn8UH3OesSI3r9ZHMOOqjfcR/yz+ZYLw07xESsT/l0Y4sxYqfFnetiKcDw1UfU44rg5P4vSACw2ofol+iHah8mz512w66otJ7GxVxo/QA/4/DOX+Az6viwi1b5ZLirnoN6m1vNGxSV3ds2A/V/FIq7eoAnmOxqQg9CiHVEsRgRFS0eiA/SjR1eOmFU1J6RDpau3e3OLgZcQC+MZTUoice0bD7suD5PTzG/IRuCtvWXDcZ48de3oioe/ZpLUcwihYVgUrzlloiKDNHXYe9O16cEs8/8KCrQ==ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCcOuK1ykJil51kUKkCFX+e2VIWiEgrIulBAkeOv+S+/2aJzX6uNvrL1EK8rVuJDNAE4hr8gEfNXvhKajJRSPZR6WojBpdCuocS7rn6DLmdUcUzPtbd4LWLttyVF6VJ+ilfvvJsvGs6vX828/ix7xRR2H+Z4gt/IOMO+961VEUVNRCqyYoW9u6UgygzsDkoR01Do7Z8syoGllyTnbIRLz3xHjdOHL85u8hzrkXZG6h+eTMQJ+LqB/zivE8JHCJBlWkYktYXLsfFvdwb3VRMGsytm5tXhFp5NKjsWgEVQUCNWnmDs9tDr0FiL1DKAy2Y3n+Fi+vb+OQlnz+GVRkX8MN5 /root/.ssh/id_rsa
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA2wffC2VPMUhZWLmnpjoJle44e+T51E6iQ5VpygV/uFZGGssRNMBnuc6KTmkWgptT29gfqIlXrJRhW6orFNCewHxZHiZpr+b9sJ0vB2oG67qTjcab8mGmKmiRfqLLNCV4Wod6Y+xrwx6TYf2AE5IsYmEVom+mJRG0dcJgl2AWkd+KGfXvzB5CCEIrGaJa9bWy40SR+Rw4cAppFvsYA8uQOEBV13f72vhvOHimxTvuq2zFu1pMNYQe8gqFV2NpuAFgPCa5LThl7xw6+Qur2AubjTLtOljePtXKe10nKHy2JBwDnMrPsMTfq7pFsQryoeNfsuZ47IEdiGIZeEYbv+slZQ== root@centos6.com

再将被链接的存有所有人的公钥复制到所有的主机上
[root@centos7 ~]# scp -p /root/.ssh/authorized_keys 192.168.131.129
[root@centos7 ~]# scp -p ....
此时已经实现之间通讯基于key验证

方法2：
在管理机上生成公钥和私钥
[root@centos7 ~]# ssh-keygen -t rsa -P "" -f /root/.ssh/id_rsa
Generating public/private rsa key pair.
Created directory '/root/.ssh'.
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:uDHj47XmJjuhJZcsHCWOje+LAwhTkQHWeeVKTCXoOP4 root@centos7.com
The key's randomart image is:
+---[RSA 2048]----+
|.o++oooo         |
|. o++.+          |
| .o=.= .         |
|ooo.= ..         |
|+..o +=.S        |
|o.  =.*=         |
| ... *+..        |
|  .Eo.ooo.       |
|  ....oBo        |
+----[SHA256]-----+

管理机本机连接本机
[root@centos7 ~]# ssh-copy-id 192.168.131.178
此时管理机文件中有了自己的私钥文件
[root@centos7 .ssh]# cat authorized_keys 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCcOuK1ykJil51kUKkCFX+e2VIWiEgrIulBAkeOv+S+/2aJzX6uNvrL1EK8rVuJDNAE4hr8gEfNXvhKajJRSPZR6WojBpdCuocS7rn6DLmdUcUzPtbd4LWLttyVF6VJ+ilfvvJsvGs6vX828/ix7xRR2H+Z4gt/IOMO+961VEUVNRCqyYoW9u6UgygzsDkoR01Do7Z8syoGllyTnbIRLz3xHjdOHL85u8hzrkXZG6h+eTMQJ+LqB/zivE8JHCJBlWkYktYXLsfFvdwb3VRMGsytm5tXhFp5NKjsWgEVQUCNWnmDs9tDr0FiL1DKAy2Y3n+Fi+vb+OQlnz+GVRkX8MN5 /root/.ssh/id_rsa

将管理机的整个.ssh目录cp到其他本机上（大家公用相同的公钥和私钥）
[root@centos7 .ssh]# scp -rp /root/.ssh/ 192.168.131.129:/root/
root@192.168.131.129's password: 
id_rsa                              100% 1679   998.2KB/s   00:00    
id_rsa.pub                          100%  398   582.3KB/s   00:00    
known_hosts                         100%  574     1.0MB/s   00:00    
authorized_keys                     100%  399   554.5KB/s   00:00    
此时互相就可以连接了
[root@centos7 .ssh]# ssh 192.168.131.129
Last login: Fri Nov 16 00:37:25 2018 from 192.168.131.129
[root@centos6 ~]# exit
logout
```
## scp命令 

`scp命令： `

`scp [options] SRC... DEST/ `

两种方式：  

scp [options] [user@]host:/sourcefile  /destpath  

scp [options] /sourcefile  [user@]host:/destpath 

常用选项：  

&ensp;&ensp;-C 压缩数据流  

&ensp;&ensp;-r 递归复制  

&ensp;&ensp;-p 保持原文件的属性信息  

&ensp;&ensp;-q 静默模式  

&ensp;&ensp;-P PORT 指明remote host的监听的端口

## rsync命令 

基于ssh和rsh服务实现高效率的远程系统之间复制文件 

使用安全的shell连接做为传输方式 ,底层也是利用ssh协议安全传输

&ensp;&ensp;rsync –av /etc server1:/tmp 复制目录和目录下文件 

&ensp;&ensp;rsync –av  /etc/  server1:/tmp 只复制目录下文件 

`比scp更快，只复制不同的文件 `

选项： 

&ensp;&ensp;-n 模拟复制过程 

&ensp;&ensp;-v 显示详细过程 

&ensp;&ensp;-r 递归复制目录树 

&ensp;&ensp;-p 保留权限 

&ensp;&ensp;-t 保留时间戳 

&ensp;&ensp;-g 保留组信息 

&ensp;&ensp;-o 保留所有者信息 

&ensp;&ensp;-l  将软链接文件本身进行复制（默认） 

&ensp;&ensp;-L 将软链接文件指向的文件复制 

&ensp;&ensp;-a 存档，相当于–rlptgoD，但不保留ACL（-A）和SELinux属性（-X） 


## sftp命令 

底层也是基于ssh安全传输

交互式文件传输工具 

用法和传统的ftp工具相似 

利用ssh服务实现安全的文件上传和下载 

使用ls cd mkdir rmdir pwd get put等指令，可用？或help获取帮助信息  

&ensp;&ensp;sftp [user@]host  

&ensp;&ensp;sftp> help 

## ssh高级应用和端口转发

## `pssh工具 `

pssh是一个python编写可以在多台服务器上执行命令的工具，也可实现文件复制 

选项如下： 基于epel源

&ensp;&ensp;--version：查看版本  

&ensp;&ensp;-h：主机文件列表，内容格式”[user@]host[:port]”  

&ensp;&ensp;-H：主机字符串，内容格式”[user@]host[:port]”  

&ensp;&ensp;-A：手动输入密码模式  

&ensp;&ensp;-i：每个服务器内部处理信息输出  

&ensp;&ensp;-l：登录使用的用户名  

&ensp;&ensp;-p：并发的线程数【可选】  

&ensp;&ensp;-o：输出的文件目录【可选】  

&ensp;&ensp;-e：错误输入文件【可选】  

&ensp;&ensp;-t：TIMEOUT 超时时间设置，0无限制【可选】 

&ensp;&ensp;-O：SSH的选项  

&ensp;&ensp;-P：打印出服务器返回信息 

&ensp;&ensp;-v：详细模式 

```bash
[root@centos7 ~]# rpm -ql pssh
/usr/bin/pnuke
/usr/bin/prsync
/usr/bin/pscp.pssh
/usr/bin/pslurp
/usr/bin/pssh
[root@centos7 ~]# pssh -H 192.168.131.129 -A -i 'hostname'
Warning: do not enter your password if anyone else has superuser
privileges or access to your account.
Password: 
[1] 17:23:15 [SUCCESS] 192.168.131.129
centos6.com

认为用的口令为相同的口令（可以都基于key验证，将key设置口令）
[root@centos7 ~]# pssh -H 192.168.131.129 -H 192.168.131.178 -A -i 'hostname'
Warning: do not enter your password if anyone else has superuser
privileges or access to your account.
Password: 
[1] 17:23:52 [SUCCESS] 192.168.131.129
centos6.com
[2] 17:23:53 [SUCCESS] 192.168.131.178
centos7.com

实现执行多个主机的命令
[root@centos7 ~]# cat ip.sh 
192.168.131.129
192.168.131.178
[root@centos7 ~]# pssh -h ip.sh -i -A hostname
Warning: do not enter your password if anyone else has superuser
privileges or access to your account.
Password: 
[1] 17:44:56 [SUCCESS] 192.168.131.178
centos7.com
[2] 17:44:57 [SUCCESS] 192.168.131.129
centos6.com

基于key
[root@centos7 ~]# pssh -h ip.sh -i -A 'hostname'
Warning: do not enter your password if anyone else has superuser
privileges or access to your account.
Password: 
[1] 17:44:56 [SUCCESS] 192.168.131.178
centos7.com
[2] 17:44:57 [SUCCESS] 192.168.131.129
centos6.com
[root@centos7 ~]# pssh -h ip.sh -i  'hostname'
[1] 17:48:40 [SUCCESS] 192.168.131.129
centos6.com
[2] 17:48:40 [SUCCESS] 192.168.131.178
centos7.com

将输出结果放到文件中
[root@centos7 ~]# pssh -h ip.sh -o /data -i  'hostname'
[1] 17:50:36 [SUCCESS] 192.168.131.129
centos6.com
[2] 17:50:36 [SUCCESS] 192.168.131.178
centos7.com
[root@centos7 ~]# cd /data
[root@centos7 data]# ll
-rw-r--r--. 1 root root  12 Nov 15 17:50 192.168.131.129
-rw-r--r--. 1 root root  12 Nov 15 17:50 192.168.131.178

批量修改selinux策略
[root@centos7 ~]# pssh -h /root/ip.sh -i 'sed -i "s/^SELINUX=.*/SELINUX=enforcing/" /etc/selinux/config'
[1] 18:18:02 [SUCCESS] 192.168.131.129
[2] 18:18:05 [SUCCESS] 192.168.131.178
```

Pssh示例 


通过pssh批量关闭seLinux 

pssh -H root@192.168.1.10  -i  "sed -i "s/SELINUX=enforcing/SELINUX=disabled/"  /etc/selinux/config"                         批量发送指令 

pssh -H root@192.168.1.10  -i  setenforce 0   

pssh -H xuewb@192.168.1.10  -i  hostname 

当不支持ssh的key认证时，通过 -A选项，使用密码认证批量执行指令 

pssh -H xuewb@192.168.1.10  -A -i  hostname    

将标准错误和标准正确重定向都保存至/app目录下 

pssh -H 192.168.1.10 -o /app -e /app   -i "hostname" 

## PSCP.PSSH命令

`pscp.pssh功能是将本地文件批量复制到远程主机 `

pscp [-vAr] [-h hosts_file] [-H [user@]host[:port]] [-l user] [-p par] [-o outdir] [-e errdir] [-t timeout] [-O options] [-x args] [-X arg] local remote 

Pscp-pssh选项  

&ensp;&ensp;-v 显示复制过程  

&ensp;&ensp;-r 递归复制目录 

将本地curl.sh 复制到/app/目录 

&ensp;&ensp;pscp.pssh -H 192.168.1.10 /root/test/curl.sh    /app/    

&ensp;&ensp;pscp.pssh -h host.txt  /root/test/curl.sh    /app/  

将本地多个文件批量复制到/app/目录 

&ensp;&ensp;pscp.pssh -H 192.168.1.10  /root/f1.sh  /root/f2.sh    /app/ 

将本地目录批量复制到/app/目录  

&ensp;&ensp;pscp.pssh -H 192.168.1.10  -r /root/test/    /app/ 

范例：pscp.pssh命令
```bash
[root@centos7 ~]# pscp.pssh -h ip.sh /root/aa.sh /data/
[1] 18:26:35 [SUCCESS] 192.168.131.129
[2] 18:26:35 [SUCCESS] 192.168.131.178
[root@centos7 ~]# pssh -h ip.sh -i '/data/aa.sh'
[1] 18:27:02 [SUCCESS] 192.168.131.129
CentOS release 6.9 (Final)
[2] 18:27:04 [SUCCESS] 192.168.131.178
CentOS Linux release 7.5.1804 (Core) 
```
## PSLURP命令 

`pslurp功能是将远程主机的文件批量复制到本地`

pslurp [-vAr] [-h  hosts_file] [-H [user@]host[:port]] [-l user] [-p par][-o outdir] [-e errdir] [-t timeout] [-O options] [-x args] [-X arg] [-L localdir] remote local（本地名） 

Pslurp选项 

&ensp;&ensp;-L 指定从远程主机下载到本机的存储的目录，local是下载到本地后的名称 

&ensp;&ensp;-r 递归复制目录 

批量下载目标服务器的passwd文件至/app下，并更名为
user 

&ensp;&ensp;pslurp -H 192.168.1.10 -L /app/ /etc/passwd user 

## SSH端口转发

`SSH端口转发`

&ensp;&ensp;SSH 会自动加密和解密所有 SSH 客户端与服务端之间的网络数据。但是，SSH 还能够将其他 TCP 端口的网络数据通过 SSH 链接来转发，并且自动提供了相应的 加密及解密服务。这一过程也被叫做“隧道”（tunneling），这是因为 SSH 为 其他 TCP 链接提供了一个安全的通道来进行传输而得名。例如，Telnet，SMTP， LDAP 这些 TCP 应用均能够从中得益，避免了用户名，密码以及隐私信息的明文 传输。而与此同时，如果工作环境中的防火墙限制了一些网络端口的使用，但是 允许 SSH 的连接，也能够通过将 TCP 端口转发来使用 SSH 进行通讯 

`SSH 端口转发能够提供两大功能：` 

&ensp;&ensp;加密 SSH Client 端至 SSH Server 端之间的通讯数据 

&ensp;&ensp;突破防火墙的限制完成一些之前无法建立的 TCP 连接 

## `SSH端口转发 `
`本地转发：`    

&ensp;&ensp;-L localport:remotehost:remotehostport   sshserver 

选项：  

&ensp;&ensp;-f 后台启用  

&ensp;&ensp;-N 不打开远程shell，处于等待状态  

&ensp;&ensp;-g 启用网关功能 

示例  

&ensp;&ensp;ssh –L  9527:telnetsrv:23 -N  sshsrv  

&ensp;&ensp;telnet 127.0.0.1 9527  

&ensp;&ensp;当访问本机的9527的端口时，被加密后转发到sshsrv的ssh服务，再解密被转发 到telnetsrv:23  

&ensp;&ensp;data <--> localhost:9527 <--> localhost:XXXXX <-->sshsrv:22 <--> sshsrv:YYYYY <--> telnetsrv:23 

范例：本地转发--实现三台主机，实现内外网telnet服务基于ssh加密安全传输过程（端口转发实验）

![本地转发](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E6%9C%AC%E5%9C%B0%E8%BD%AC%E5%8F%91.png)

```bash
此实验局限于伟大的防火墙
实验前提：
              本机    wan                                    lan
9527------->centos7------ssh(telnet)---> || redhat7 -----telnet--->   centos6||
           internet         telnet      ssh server               telnet server
           ssh client                    telnet client
 (data--->localhost:9527---localhost:xxx--->ssh(data)----sshserver---->data---->ssh server:xxx---->data---->inter server)

centos7 ip:192.168.131.179   sshclient
redhat ip :192.168.131.173   ssh server
centos6 ip:192.168.131.129   telnel server

在centos6 上搭建一个telnet服务器
[root@centos6 ~]# yum install telnet-server
[root@centos6 ~]# chkconfig telnet on
[root@centos6 ~]# service xinetd start
Starting xinetd:                                           [  OK  ]
内外网，直接拒接centos7外网的连接、设置防火墙
[root@centos6 ~]# iptables -F
[root@centos6 ~]# iptables -A INPUT -s 192.168.131.179 -j REJECT

第一步：在centos7上打开9527端口
[root@centos7 ~]# ssh -L 9527:192.168.131.129:23 -fN 192.168.131.173
                                最终ip               跳板ip
[root@centos7 ~]# ss -ntl
LISTEN      0      128       ::1:9527               :::*       

第二步：centos7连接本机telnet
[root@centos7 ~]# telnet 127.0.0.1 9527
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'.
CentOS release 6.9 (Final)
Kernel 2.6.32-696.el6.x86_64 on an x86_64
centos6.com login: daizhe
Password: 
[daizhe@centos6 ~]$   telnet 不安全的传输协议，仅可以普通用户连接

centos6检验(在centos6 认为redhat7在连，此时中间传输为加密)
[root@centos6 ~]# ss -nt
State      Recv-Q Send-Q           Local Address:Port             Peer Address:Port 
ESTAB      0      0              192.168.131.129:23            192.168.131.173:46049 
ESTAB      0      64             192.168.131.129:22              192.168.131.1:60868 

杀死进程
[root@centos7 ~]# killall ssh
```
`远程转发: `  

&ensp;&ensp;-R sshserverport:remotehost:remotehostport  sshserver 

示例：    

&ensp;&ensp;ssh –R 9527:telnetsrv:23 –N  sshsrv    让sshsrv侦听9527端口的访问，如有访问，就加密后通过ssh服务转发请求到本 机ssh客户端，再由本机解密后转发到telnetsrv:23 

&ensp;&ensp;Data <--> sshsrv:9527 <-->sshsrv:22 <-->localhost:XXXXX <-->localhost:YYYYY <--> telnetsrv:23 

范例：远程转发

![远程转发](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E8%BF%9C%E7%A8%8B%E8%BD%AC%E5%8F%91.png)

```bash
内网连外网 无需修改防火墙策略
                  wan              本机                      lan
centos7------ssh(telnet)---> || redhat7 -----telnet--->   centos6||
internet         telnet      ssh client               telnet server
ssh server                    telnet client

centos7 ip:192.168.131.179   ssh client
redhat ip :192.168.131.173   ssh server
centos6 ip:192.168.131.129   telnel server

data-->internet:9527-->internet:22--ssh(data)-->localhost:xxx---data---localhost:xxx---data--->telnet server:23

centos6 上继续设置防火墙阻止centos7的ssh连接
第一步:本机为redhat7 在此机器上设置，打开通道
[root@redhat ~]# ssh -R 9527:192.168.131.129:23 -fN 192.168.131.179   （fNg g选项代表网关的意思，监听此网段，实现外面多台主机连接，建立 堡垒机，互相使用9527建立，需要开启网管功能）
[root@redhat ~]# ss -nt
State      Recv-Q Send-Q           Local Address:Port             Peer Address:Port 
ESTAB      0      0              192.168.131.173:57755         192.168.131.179:22    
ESTAB      0      52             192.168.131.173:22              192.168.131.1:60877 

第二步：centos7 发起telnet连接
[root@centos7 ~]# telnet 127.0.0.1 9527
Trying 127.0.0.1...
Connected to 127.0.0.1.
[daizhe@centos6 ~]$ 
```
`动态端口转发：` 

&ensp;&ensp;当用firefox访问internet时，本机的1080端口做为代理服务器，firefox的访问 请求被转发到sshserver上，由sshserver替之访问internet     

&ensp;&ensp;ssh -D 1080 root@sshserver     

&ensp;&ensp;在本机firefox设置代理socket proxy:127.0.0.1:1080     

&ensp;&ensp;curl  --socks5 127.0.0.1:1080  http://www.qq.com 

![翻墙](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/QQ%E6%88%AA%E5%9B%BE20181115212746.png)
范例：过程演示
```bash
次实验国内的主机必须是centos系统，如果想要实现国内的windows主机访问，可是利用国内的centos系统的主机在下述实现中的第一步打开端口是使用g选项，并使得国内的windows系统在浏览器配置转发代理指向国内的centos系统

centos7 ip:192.168.131.179 
redhat ip :192.168.131.173  
centos6 ip:192.168.131.129 

centos7 代表国内的用户
redhat7 代表过外的服务器
centos6 代表国外网站资源



[root@centos6 ~]# curl 192.168.131.129
www.google.com
国内防火墙
[root@centos7 ~]# iptables -A INPUT -s 192.168.131.129 -j REJECT
访问不了google
[root@centos7 ~]# curl 192.168.131.129
curl: (7) Failed connect to 192.168.131.129:80; Connection refused
国外的服务器是可以网问google的
[root@redhat ~]# curl 192.168.131.129
www.google.com

第一步：本地客户端开一个端口使用此端口连接远程的国外服务器
[root@centos7 ~]# ssh -D 12354 192.168.131.173 -fN
root@192.168.131.173's password: 
[root@centos7 ~]# ss -tnl
LISTEN     0      128           ::1:12354                      :::* 
[root@centos7 ~]# curl 192.168.131.129
curl: (7) Failed connect to 192.168.131.129:80; Connection refused

第二步：在国内的客户端配置代理服务
浏览器设置代理服务指向自己127.0.0.1 端口指定自己设定的端口号12354
[root@centos7 ~]# curl --socks5 127.0.0.1:12354 192.168.131.129
www.google.com
```



## X 协议转发

所有图形化应用程序都是X客户程序 

&ensp;&ensp;能够通过tcp/ip连接远程X服务器 

&ensp;&ensp;数据没有加密机，但是它通过ssh连接隧道安全进行 

ssh -X user@remotehost gedit   

&ensp;&ensp;remotehost主机上的gedit工具，将会显示在本机的X服务器上  

&ensp;&ensp;传输的数据将通过ssh连接加密

## ssh服务器 

`服务器端：sshd, 配置文件: /etc/ssh/sshd_config `

`常用参数： `

&ensp;&ensp;Port    端口

&ensp;&ensp;ListenAddress ip   监听的地址

&ensp;&ensp;LoginGraceTime 2m  

&ensp;&ensp;PermitRootLogin yes 

&ensp;&ensp;StrictModes yes   检查.ssh/文件的所有者，权限等 

&ensp;&ensp;MaxAuthTries   6 

&ensp;&ensp;MaxSessions  10 同一个连接最大会话 

&ensp;&ensp;PubkeyAuthentication yes 

&ensp;&ensp;PermitEmptyPasswords no 

&ensp;&ensp;PasswordAuthentication yes 

&ensp;&ensp;GatewayPorts no 

&ensp;&ensp;ClientAliveInterval：单位:秒 

&ensp;&ensp;ClientAliveCountMax：默认3 

&ensp;&ensp;UseDNS yes   

&ensp;&ensp;GSSAPIAuthentication yes 提高速度可改为no 

&ensp;&ensp;MaxStartups 未认证连接最大值，默认值10 

&ensp;&ensp;Banner /path/file 

&ensp;&ensp;限制可登录用户的办法：   

&ensp;&ensp;&ensp;&ensp;AllowUsers user1 user2 user3   

&ensp;&ensp;&ensp;&ensp;DenyUsers   

&ensp;&ensp;&ensp;&ensp;AllowGroups   

&ensp;&ensp;&ensp;&ensp;DenyGroups 

范例：学习ssh服务端的配置文件
```bash
[root@centos7 ssh]# vim /etc/ssh/sshd_config 
#Port 22   默认端口号
#ListenAddress 0.0.0.0  监听的地址

私钥文件尝试算法连接
HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key

SyslogFacility AUTHPRIV  登陆日志记录/var/log/secure
#LoginGraceTime 2m     等待登陆时常
#PermitRootLogin yes   是否登陆root账号登陆
#MaxAuthTries 6        最大连接尝试次数一半 3次
#MaxSessions 10        此机器一个链接可以建立几个会话（克隆会话）
#PubkeyAuthentication yes   公钥验证
#PasswordAuthentication yes 是否可以口令验证
#PermitEmptyPasswords no    是否禁止空口令登陆

影响连接速度
GSSAPIAuthentication yes    是否关闭ssh基于GSS验证
GSSAPICleanupCredentials no
#UseDNS yes                 是否开启DNS反向解析

检查连接的客户端多少秒检查几次，如果不活跃断开连接
#ClientAliveInterval 0      客户端活动的检查间隔（单位：秒 ）
#ClientAliveCountMax 3      检查客户端的次数

#MaxStartups 10:30:100      限制并发连接
#Banner none                登陆提示信息（后面可以加文件路径，需要放在etc目录下以ssh.txt文件命名的文件）

限制可登录用户的办法：   

AllowUsers user1 user2 user3   允许的人

DenyUsers                      拒绝的人
 
AllowGroups                    允许的组        

DenyGroups                     拒绝的组
```

## ssh服务的最佳实践 


建议使用非默认端口 

禁止使用protocol version 1 

限制可登录用户 

设定空闲会话超时时长 

利用防火墙设置ssh访问策略 

仅监听特定的IP地址 

基于口令认证时，使用强密码策略     

&ensp;&ensp;tr -dc A-Za-z0-9_ < /dev/urandom | head -c 30| xargs 

使用基于密钥的认证 

禁止使用空密码 

禁止root用户直接登录 

限制ssh的访问频度和并发在线数 

经常分析日志 
