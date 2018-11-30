---
title: ansible实验步骤
tags: linux服务
categories: linux服务
---
ansible 并非一个服务，无需长期运行，可以称之为控制机，基于python 2.7.5版本研发

## `ansible模块`

`范例：ansible实验应用场景`
![ansible实验主机](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/ansible%E5%AE%9E%E9%AA%8C%E5%87%86%E5%A4%87.png)

准备实验环境：这里准备四台虚拟机：  

一台centos7 作为ansible堡垒机  
两台redhat7 作为被控制端  
一台centos6 作为被控制端

堡垒机ip:192.168.131.184:主机名；ansible  
客户机centos6 ip:192.168.131.129：主机名：centos6    
客户机redhat ip:192.168.131.173:主机名：redhat  
客户机redhat1 ip:192.168.131.185:主机名：redhat7

```bash
堡垒机安装ansible服务，是基于epel源的，实验前提，虚拟机是可以连接互联网，已经配置好yum源
[root@ansible ~]# yum install ansible
[root@ansible ~]# rpm -ql ansible | less
/etc/ansible/ansible.cfg  主配置文件
/etc/ansible/hosts  主机清单
/usr/bin/ansible   可执行程序（软连接方便升级）

堡垒机的主机清单/etc/ansible/hosts，要想让堡垒机去管理其他主机，就要将被管理的主机ip加入主机清单内
[root@ansible ~]# vim /etc/ansible/hosts 
192.168.131.129  单个ip写法
192.168.131.173
192.168.131.185

[websrvs]        分组写法
192.168.131.129
192.168.131.173
  
[appsrvs]        分组写法            [组名]   也支持这种写法，代表17和27主机ip
192.168.131.173                 192.168.131.[1:2]7
192.168.131.185           

修改ansible的配置文件，首先启用ansible的日志信息
[root@ansible ~]# vim /etc/ansible/ansible.cfg
log_path = /var/log/ansible.log             日志信息，默认未启用，设置为启用

   查看所有的模块
   [root@ansible ~]# ansible-doc -l 

```
`范例：简述ping模块，及实验模块基于key验证`
```bash
列出某个模块的说明 

ping模块:测试主机清单中的ip的主机是否于堡垒机知否可以正常通讯
查看ping模块的说明
[root@ansible ~]# ansible-doc ping
[root@ansible ~]# ansible 192.168.131.129 -m ping（第一次登陆基于ssh验证）

想要使得主机取消ssh连接的账户密码验证修改ansible的配置文件
[root@ansible ~]# vim /etc/ansible/ansible.cfg
#uncomment this to disable SSH key host checking
host_key_checking = False

再次执行ping模块（错误原为为ansible默认为key验证）
[root@ansible ~]# ansible 192.168.131.129 -m ping
192.168.131.129 | UNREACHABLE! => {
    "changed": false, 
    想要解决错误可是使用-k选项 提示ssh连接输入连接用户的密码
    [root@ansible ~]# ansible 192.168.131.129 -m ping -k
      SSH password: 
          192.168.131.129 | SUCCESS => {
         "changed": false, 
         "ping": "pong"
      }

    脚本实现基于key验证，避免每次输口令，决绝对端主机口令不一致的现象
    主机清单：
    [root@ansible ~]# cat iplist.sh 
    192.168.131.129
    192.168.131.173
    192.168.131.185
    安装expect
    [root@ansible ~]# yum install expect -y
    脚本：
    [root@ansible ~]# cat keyssh.sh 
     #!/bin/bash
     user=root
     ssh-keygen -t rsa -P "" -f /root/.ssh/id_rsa
     while read line ;do
     ip=$line
     password=123456
     expect << EOF
     set timeout 10
     spawn ssh-copy-id -i /root/.ssh/id_rsa.pub $user@$ip
     expect {
       "yes/no" { send "yes\n";exp_continue }
       "password" { send "$password\n" }
     }
     expect eof
     EOF
     done < iplist.sh

    [root@ansible ~]# bash keyssh.sh 
    
    ping模块基于key验证验证堡垒机是否与被控制端是否正常通讯
    [root@ansible ~]# ansible all -m ping
      192.168.131.129 | SUCCESS => {
         "changed": false, 
         "ping": "pong"
      }
      192.168.131.173 | SUCCESS => {
         "changed": false, 
         "ping": "pong"
      }
      192.168.131.185 | SUCCESS => {
         "changed": false, 
         "ping": "pong"
      }
```
`范例：ansible常用模块Command ：在远程主机执行linux命令（默认模块）`
```bash
在centos6被控制主机上有一个文件
[root@centos6 ~]# ls
a.txt    

在控制机上使用command模块删除centos6上的文件 (系统模块名称可省略)
[root@ansible ~]# ansible 192.168.131.129 -m command -a 'rm -f /root/a.txt'
 [WARNING]: Consider using file module with state=absent rather than running rm

192.168.131.129 | SUCCESS | rc=0 >>

查看所有被控制机上的主机列表
[root@ansible ~]# ansible all -m command -a 'getent passwd'
模块名称可省略
[root@ansible ~]# ansible 192.168.131.129 -a 'getent passwd'

在centos6被控制机上创建用户
[root@ansible ~]# ansible 192.168.131.129 -a 'useradd user11'
192.168.131.129 | SUCCESS | rc=0 >>
[root@ansible ~]# ansible 192.168.131.129 -a 'getent passwd user11'
192.168.131.129 | SUCCESS | rc=0 >>
user11:x:501:501::/home/user11:/bin/bash
```
`范例：ansible常用模块shell ：在远程主机执行linux命令`
```bash
查看shell模块帮助
[root@ansible ~]# ansible-doc -s shell

在centos6上使用shell模块改用户的口令
[root@ansible ~]# ansible 192.168.131.129 -m shell -a 'echo daizhe | passwd --stdin daizhe'
192.168.131.129 | SUCCESS | rc=0 >>
Changing password for user daizhe.
passwd: all authentication tokens updated successfully.

显示所有被控制端的主机名
[root@ansible ~]# ansible all -m shell -a 'echo $HOSTNAME'
192.168.131.129 | SUCCESS | rc=0 >>
centos6.com
192.168.131.173 | SUCCESS | rc=0 >>
redhat.com
192.168.131.185 | SUCCESS | rc=0 >>
redhat7.com

将所有被控制机的/data目录下的所有文件删除 chdir
[root@ansible ~]# ansible all -m shell -a 'chdir=/data rm -rf *'
 [WARNING]: Consider using file module with state=absent rather than running rm
192.168.131.129 | SUCCESS | rc=0 >>
192.168.131.173 | SUCCESS | rc=0 >>
192.168.131.185 | SUCCESS | rc=0 >>
[root@ansible ~]# ansible all -m shell -a 'chdir=/data ls'
192.168.131.129 | SUCCESS | rc=0 >>
192.168.131.173 | SUCCESS | rc=0 >>
192.168.131.185 | SUCCESS | rc=0 >>


ansible默认模块为command ，shell 模块比较好用我们可以将shell模块设置为默认模块，编辑ansible配置文件
[root@ansible ~]# vim /etc/ansible/ansible.cfg 
module_name = shell


```
`范例：使用Script 脚本模块 实现将控制端的脚本在所有被控制端的主机上执行一下`
```bash
查看帮助
[root@ansible ~]# ansible-doc -s script
- name: Runs a local script on a remote node after transferring it

脚本：将所有被控制的主机上将selinux修改为disabled
[root@ansible ~]# cat selinux.sh 
#!/bin/bash
sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config

[root@ansible ~]# ansible all -m script -a "/root/selinux.sh"
192.168.131.173 | SUCCESS => {
    "changed": true, 
    "rc": 0, 
    "stderr": "Shared connection to 192.168.131.173 closed.\r\n", 
    "stdout": "", 
    "stdout_lines": []
}
192.168.131.129 | SUCCESS => {
    "changed": true, 
    "rc": 0, 
    "stderr": "Shared connection to 192.168.131.129 closed.\r\n", 
    "stdout": "", 
    "stdout_lines": []
}
192.168.131.185 | SUCCESS => {
    "changed": true, 
    "rc": 0, 
    "stderr": "Shared connection to 192.168.131.185 closed.\r\n", 
    "stdout": "", 
    "stdout_lines": []
}
```
```bash
shell模块和script模块中都存在的模块用法
creates:如果已经存在此步骤不执行
removes:如果存在，此步骤执行

fstab文件存在，则后续命令则不执行
[root@ansible ~]# ansible all -a "creates=/etc/fstab rm -rf /data/*"
192.168.131.129 | SUCCESS | rc=0 >>
skipped, since /etc/fstab exists
192.168.131.173 | SUCCESS | rc=0 >>
skipped, since /etc/fstab exists
192.168.131.185 | SUCCESS | rc=0 >>
skipped, since /etc/fstab exists

[root@ansible ~]# ansible all -a "removes=/etc/fstab rm -rf /data/*"
 [WARNING]: Consider using file module with state=absent rather than running rm
192.168.131.129 | SUCCESS | rc=0 >>
192.168.131.173 | SUCCESS | rc=0 >>
192.168.131.185 | SUCCESS | rc=0 >>
```
`范例：copy模块`
```bash
将控制机上的fstab文件保留原名，发送到所有被控制端的主机上，并修改文件的所有者，并设置权限
[root@ansible ~]# ansible all -m copy -a "src=/etc/fstab dest=/root/ owner=daizhe mode=600"

确认是否成功
[root@ansible ~]# ansible all -m shell -a 'ls -l /root/fstab'
192.168.131.129 | SUCCESS | rc=0 >>
-rw-------. 1 daizhe root 595 Nov 22 07:38 /root/fstab
...

root@centos6 ~]# ll /root/fstab
-rw-------. 1 daizhe root 595 Nov 22 07:38 /root/fstab
....

将控制机上的fstab文件保留原名，发送到所有被控制端的主机上，并修改文件的所有者，并设置权限,如果对方有此文件，则先备份再进行修改
[root@ansible ~]# ansible all -m copy -a "src=/etc/fstab dest=/root/ owner=daizhe mode=600 backup=yes"

拷贝主机上的文件夹到所有的控制端
[root@ansible ~]# ansible all -m copy -a "src=/data dest=/root/"

使用copy模块中的content 生成所有被控制端的yum配置文件
[root@ansible ~]# ansible 192.168.131.173 -m copy -a 'content="[haha]\nbaseurl=https://mirrors.aliyun.com/epel/7/x86_64/\ngpgcheck=0\nenabled=1" dest=/etc/yum.repos.d/haha.repo'
192.168.131.173 | SUCCESS => {
    "changed": true, 
    "checksum": "0d7ffdd1ba1b53d2b4f3540fc8e77a1ba40b4232", 
    "dest": "/etc/yum.repos.d/haha.repo", 
    "gid": 0, 
    "group": "root", 
    "md5sum": "e74dc0b9c50a24d884dc8e8d5d71438b", 
    "mode": "0644", 
    "owner": "root", 
    "secontext": "system_u:object_r:system_conf_t:s0", 
    "size": 77, 
    "src": "/root/.ansible/tmp/ansible-tmp-1542855358.21-118898081082202/source", 
    "state": "file", 
    "uid": 0
}

确认是否生成
[root@ansible ~]# ansible 192.168.131.173 -a 'cat /etc/yum.repos.d/haha.repo' 
192.168.131.173 | SUCCESS | rc=0 >>
[haha]
baseurl=https://mirrors.aliyun.com/epel/7/x86_64/
gpgcheck=0
enabled=1

```
`范例：fetch模块`
```bash
从被控的主机上抓取主机名文件到本地
[root@ansible ~]# ansible all -m fetch -a 'src=/etc/hosts dest=/data/'
192.168.131.129 | SUCCESS => {
    "changed": true, 
    "checksum": "7335999eb54c15c67566186bdfc46f64e0d5a1aa", 
    "dest": "/data/192.168.131.129/etc/hosts", 
    "md5sum": "54fb6627dbaa37721048e4549db3224d", 
    "remote_checksum": "7335999eb54c15c67566186bdfc46f64e0d5a1aa", 
    "remote_md5sum": null
}
192.168.131.173 | SUCCESS => {
    "changed": true, 
    "checksum": "7335999eb54c15c67566186bdfc46f64e0d5a1aa", 
    "dest": "/data/192.168.131.173/etc/hosts", 
    "md5sum": "54fb6627dbaa37721048e4549db3224d", 
    "remote_checksum": "7335999eb54c15c67566186bdfc46f64e0d5a1aa", 
    "remote_md5sum": null
}
192.168.131.185 | SUCCESS => {
    "changed": true, 
    "checksum": "7335999eb54c15c67566186bdfc46f64e0d5a1aa", 
    "dest": "/data/192.168.131.185/etc/hosts", 
    "md5sum": "54fb6627dbaa37721048e4549db3224d", 
    "remote_checksum": "7335999eb54c15c67566186bdfc46f64e0d5a1aa", 
    "remote_md5sum": null
}
[root@ansible ~]# cd /data
[root@ansible data]# ls
192.168.131.129  192.168.131.173  192.168.131.185

fetch 模板不支持抓取目录
想要实现将被控制端的/data目录抓取到本机
打包
[root@ansible ~]# ansible all -m shell -a 'tar cf /root/data.tar /data'
 [WARNING]: Consider using unarchive module rather than running tar
192.168.131.129 | SUCCESS | rc=0 >>
tar: Removing leading `/' from member names
192.168.131.173 | SUCCESS | rc=0 >>
tar: Removing leading `/' from member names
192.168.131.185 | SUCCESS | rc=0 >>
tar: Removing leading `/' from member names

抓取
[root@ansible ~]# ansible all -m fetch -a 'src=/root/data.tar dest=/root'
```
`范例：file模块`
```bash
path:指定创建文件的路径
state:指定对文件进行的操作
    touch：对文件进行创建操作
    absent:删除操作
    link:创建连接文件、软连接
    hard:创建硬链接
dest=path=name 意义相同，目标创建的文件
再所有被管理的终端上的/data目录上创建文件
[root@ansible ~]# ansible all -m file -a 'path=/data/file state=touch'

删除上面创建的文件
[root@ansible ~]# ansible all -m file -a 'path=/data/file state=absent'

再被管理终端上创建连接文件
[root@ansible ~]# ansible all -a 'ls /data'
192.168.131.129 | SUCCESS | rc=0 >>
fstab
192.168.131.173 | SUCCESS | rc=0 >>
fstab
192.168.131.185 | SUCCESS | rc=0 >>
fstab

对所有终端上的/data/fstab文件创建连接(软连接)
[root@ansible ~]# ansible all -m file -a 'src=/data/fstab path=/data/fstab.link state=link'
[root@ansible ~]# ansible all -a 'ls -l /data'
192.168.131.129 | SUCCESS | rc=0 >>
total 4
-rw-r--r--. 1 root root 595 Nov 22 08:02 fstab
lrwxrwxrwx. 1 root root  11 Nov 22 08:20 fstab.link -> /data/fstab
....

[root@ansible ~]# ansible all -a 'ls -l /data'
192.168.131.129 | SUCCESS | rc=0 >>
total 8
-rw-r--r--. 2 root root 595 Nov 22 08:02 fstab
-rw-r--r--. 2 root root 595 Nov 22 08:02 fstab2.link

在被控制端创建文件夹路径为/data/datadir
[root@ansible ~]# ansible all -m file -a 'dest=/data/datadir state=directory'
[root@ansible ~]# ansible all -a 'ls -l /data/'
192.168.131.129 | SUCCESS | rc=0 >>
drwxr-xr-x. 2 root root 4096 Nov 22 08:26 datadir

删除被控制的目录/文件
[root@ansible ~]# ansible all -m file -a 'dest=/data/datadir state=absent'
```
`范例：Hostname模块`
```bash
修改单独被控制端的主机名
[root@ansible ~]# ansible 192.168.131.173 -m hostname -a 'name=redhat6'

查看 立即生效
hosts文件未进行更改
[root@ansible ~]# ansible 192.168.131.173 -a 'hostname'
192.168.131.173 | SUCCESS | rc=0 >>
redhat6
```
`范例：Cron模块`
```bash
设定计划任务，周六日，每五分钟执行一次，执行同步时间操作
[root@ansible ~]# ansible 192.168.131.173 -m cron -a 'minute=*/5 weekday=0,6 job="/usr/sbin/ntpdate 172.18.0.1 &> /dev/null" name=tongbu'

[root@redhat ~]# crontab -l
#Ansible: tongbu
*/5 * * * 0,6 /usr/sbin/ntpdate 172.18.0.1 &> /dev/null

禁用被控制端的计划任务
[root@ansible ~]# ansible 192.168.131.173 -m cron -a 'minute=*/5 weekday=0,6 job="/usr/sbin/ntpdate 172.18.0.1 &> /dev/null" name=tongbu disbaled=ture'

再次启用
[root@ansible ~]# ansible 192.168.131.173 -m cron -a 'minute=*/5 weekday=0,6 job="/usr/sbin/ntpdate 172.18.0.1 &> /dev/null" name=tongbu disbaled=false'

彻底铲除此计划任务
[root@ansible ~]# ansible 192.168.131.173 -m cron -a 'name=tongbu state=sbsent'
```
`范例：Yum模块,前提被控制机上 yum 是已经配置好的`
```bash
name=  指定包的名称

使用yum模板在被管理终端上安装htop包
[root@ansible ~]# ansible 192.168.131.173 -m yum -a 'name=htop'

一次安装多个包，前提是，在原来本机上未安装
[root@ansible ~]# ansible all -a 'rpm -q http,vsftpd,memcached'
[root@ansible ~]# ansible all -m yum -a 'name=httpd,vsftpd,memcached'

卸载被管理机上的应用程序
[root@ansible ~]# ansible all -m yum -a 'name=httpd state=absent'

更新被管理机yum缓存，同时安装 httpd包
[root@ansible ~]# ansible all -m yum -a 'name=httpd update_cache=yes'
```
`范例：service模块`
```bash
启动被管理端的http服务
[root@ansible ~]# ansible 192.168.131.173 -m service -a 'name=httpd state=started'

关闭服务
[root@ansible ~]# ansible 192.168.131.173 -m service -a 'name=httpd state=stopped

设置为开机启动
[root@ansible ~]# ansible 192.168.131.173 -m service -a 'name=httpd state=started enabled=yes'

systemctl is-enabled httpd
chkconfig --list httpd
```
`范例：user模块`
```bash
name= 指定用户名
comment= 描述信息
uid= 设定uid
home= 设定家目录
group= 制定主组
groups= 设定附加组
shell=指定shell类型
remove=yes 删除家目录文件
system=yes 设置系统用户、系统组

在被管理终端上创建用户，并指定属性
[root@ansible ~]# ansible all -m user -a 'name=haha comment="test user" uid=2000 home=/data/ group=root groups=bin shell=/sbin/nologin'

确定用户是否创建
[root@ansible ~]# ansible all -a 'getent passwd haha'
192.168.131.129 | SUCCESS | rc=0 >>
haha:x:2000:0:test user:/data/:/sbin/nologin
.....

删除被管理终端的用户haha,删除家目录，但是不删除家目录的文件
[root@ansible ~]# ansible all -m user -a 'name=haha state=absent'
192.168.131.173 | SUCCESS => {
    "changed": true, 
    "force": false, 
    "name": "haha", 
    "remove": false, 
    "state": "absent"
}
.....

实现删除用户，也将用户的家目录和相关文件删除
[root@ansible ~]# ansible all -m user -a 'name=hahahaha state=absent remove=yes'

```
## `playbook `

范例：写一个简单的playbook
```bash
[root@ansible data]# vim test.yml
---
- hosts: websrvs   （应用主机）
  remote_user: root (在远程主机执行任务以谁的身份)

  tasks:            (任务列表)     
  - name: diyi     （任务名称，多个任务，多个任务名）
    ping:          （模块）
  - name: dier
    shell: /bin/ls /data/

执行之前，检查是否有错误语法，仅是测试并不是真正去执行
[root@ansible data]# ansible-playbook --check 


查看这个playbook执行会对哪个主机进行操作
[root@ansible data]# ansible-playbook test.yml --list-hosts
playbook: test.yml
  play #1 (appsrvs): appsrvs	TAGS: []
    pattern: [u'appsrvs']
    hosts (2):
      192.168.131.187
      192.168.131.188

查看这个playbook执行任务的列表
[root@ansible data]# ansible-playbook test.yml --list-tasks
playbook: test.yml
  play #1 (appsrvs): appsrvs	TAGS: []
    tasks:
      diyi	TAGS: []
      dier	TAGS: []

```
`范例：使用playbook 实现在被控制端，安装http包，将本机的http配置文件范例复制到被控制端，并且将被控制端的服务，启动起来，设置为开机启动`
```bash
第一步：
创建apache用户，指定组-g apache，指定uid -u 80,指定shell类型-s /sbin/nologin ,指定家目录-d /usr/share/httpd ,系统账户-r
第二步：
安装软件包
第三步：
将控制端修改好的配置文件scp到被控制端的服务的主配置文件中，最为服务已经特定配置好再进行的安装

第四步：重启服务，设置为开机启动项

给apache系统用户设置一个安全的密码
[root@ansible ~]# openssl passwd -1
Password: 
Verifying - Password: 
$1$uJM71bTc$9yyqQzIm/5PZBrHKTpSHu0


准备好控制端需要http服务的配置文件
[root@ansible playbook]# ls
httpd.conf   修改配置文件，修改端口号8080

编写playbook
[root@ansible playbook]# vim httpd.yml

---
- hosts: 192.168.131.187
  remote_user: root

  tasks:
    - name: create group
      group: name=apache system=yes gid=80
    - name: create user
      user: name=apache group=apache uid=80 shell=/sbin/nologin home=/usr/sh
are/httpd system=yes password='$1$uJM71bTc$9yyqQzIm/5PZBrHKTpSHu0'
    - name: install package
      yum: name=httpd
    - name: config file
      copy: src=/root/playbook/httpd.conf dest=/etc/httpd/conf/ backup=yes
    - name: server httpd
      service: name=httpd state=started enabled=yes

执行前测试
[root@ansible playbook]# ansible-playbook -C httpd.yml 

检查无误则执行此脚本
[root@ansible playbook]# ansible-playbook httpd.yml 

查看是否成功
[root@ansible playbook]# ansible 192.168.131.187 -a 'ss -ntl'
LISTEN     0      128                      :::8080                    :::*     
[root@ansible playbook]# ansible 192.168.131.187 -a 'getent passwd apache'
192.168.131.187 | SUCCESS | rc=0 >>
apache:x:80:80::/usr/share/httpd:/sbin/nologin


如果文件中有一项修改了，想再次执行此playbook ，使得服务设置发生变化,这里原有内容不被修改，
方法1
可以将重启服务的时候将started 设置为restarted
方法2 handlers 和notify（幂等性不做重复操作）
触发动作：如果此项发生修改则触发此操作，如果此项未发生变化，则不触发此操作（上述实验：如果修改控制端发给被控制端的配置文件端口号发生变化，则要重新拷贝配置文件到被控制端，命为发生修改，触发动作）切记触发任务notify 和 触发的操作handlers 名字要一样，因为可能出现多个触发任务

[root@ansible playbook]# vim httpd.yml 

---
- hosts: 192.168.131.187
  remote_user: root

  tasks:
    - name: create group
      group: name=apache system=yes gid=80
    - name: create user
      user: name=apache group=apache uid=80 shell=/sbin/nologin home=/usr/share/httpd system=yes password='$1$uJM71bTc$9yyqQzIm/5PZBrHKTpSHu0'
    - name: install package
      yum: name=httpd
    - name: config file
      copy: src=/root/playbook/httpd.conf dest=/etc/httpd/conf/ backup=yes
      notify: restart service httpd
    - name: server httpd
      service: name=httpd state=started enabled=yes

  handlers:
    - name: restart service httpd
      service: name=httpd state=restarted
测试:将控制端发给被控制端的服务配置文件发射管修改 Listen 80808

[root@ansible playbook]# ansible-playbook -C httpd.yml 
[root@ansible playbook]# ansible-playbook httpd.yml 
[root@ansible playbook]# ansible 192.168.131.187 -a 'ss -tnlp'
LISTEN     0      128                      :::9527   
````
范例：触发两个动作
```bsah
[root@ansible playbook]# cat httpd.yml 
---
- hosts: 192.168.131.187
  remote_user: root

  tasks:
    - name: create group
      group: name=apache system=yes gid=80
    - name: create user
      user: name=apache group=apache uid=80 shell=/sbin/nologin home=/usr/share/httpd system=yes password='$1$uJM71bTc$9yyqQzIm/5PZBrHKTpSHu0'
    - name: install package
      yum: name=httpd 
    - name: config file
      copy: src=/root/playbook/httpd.conf dest=/etc/httpd/conf/ backup=yes
      notify: 
        - restart service
        - check httpd
    - name: server httpd
      service: name=httpd state=started enabled=yes

  handlers:
    - name: restart service
      service: name=httpd state=restarted
    - name: check httpd
      shell: /usr/bin/killall -0 httpd &> /dev/null
````
范例：Playbook中tags使用 `
```bash
用处：可以挑选性的执行playbook中的操作
[root@ansible playbook]# vim httpd.yml 

---
- hosts: 192.168.131.187
  remote_user: root

  tasks:
    - name: create group
      group: name=apache system=yes gid=80
    - name: create user
      user: name=apache group=apache uid=80 shell=/sbin/nologin home=/usr/share/httpd system=yes password='$1$uJM71bTc$9yyqQzIm/5PZBrHKTpSHu0'
    - name: install package
      yum: name=httpd
    - name: config file
      copy: src=/root/playbook/httpd.conf dest=/etc/httpd/conf/ backup=yes
      tags: config
      notify:
        - restart service
        - check httpd
    - name: server httpd
      tags: server
      service: name=httpd state=started enabled=yes

  handlers:
    - name: restart service
      service: name=httpd state=restarted
    - name: check httpd
      shell: /usr/bin/killall -0 httpd &> /dev/null

仅执行特定标签
[root@ansible playbook]# ansible-playbook -t config httpd.yml 

也可以将一个标签代表连个操作，或者一个动作多个标签，
执行两个标签
[root@ansible playbook]# ansible-playbook -t "config,server" httpd.yml 
```

## `Playbook中变量使用 `
`范例：使用变量实现，写的playbook中安装的包，并非是固定的包`
```bash
在playbook中定义变量的名称
[root@ansible playbook]# vim httpd.yml 

---
- hosts: 192.168.131.187
  remote_user: root

  tasks:
    - name: install package
      yum: name={{name}}
    - name: service
      tags: service
      service: name={{name}} state=started enabled=yes

定义变量的值
可以使用命令上 -e 定义变量（playbook内的变量名称最好不要使用保留字）
[root@ansible playbook]# ansible-playbook -e name=vsftpd httpd.yml 
确认是否成功 vsftpd端口默认为21
[root@ansible playbook]# ansible 192.168.131.187 -a 'ss -tnl'
LISTEN     0      32                       :::21            
如果报名和服务名不一样，需要在playbook中定义两个变量samba端口默认用的是445
[root@ansible playbook]# cat httpd.yml 
---
- hosts: 192.168.131.187
  remote_user: root

  tasks:
    - name: install package
      yum: name={{name1}}
    - name: service
      tags: service
      service: name={{name2}} state=started enabled=yes

[root@ansible playbook]# ansible-playbook -e "name1=samba name2=smb" httpd.yml 
```
`范例：直接在playbook中定义变量，实现安装包和程序名的不固定`性
```bash
[root@ansible playbook]# vim httpd.yml 

---
- hosts: 192.168.131.187
  remote_user: root
  vars:
     - name1: samba
     - name2: smb
  tasks:
    - name: install package
      yum: name={{name1}}
    - name: service
      tags: service
      service: name={{name2}} state=started enabled=yes

[root@ansible playbook]# ansible-playbook httpd.yml 

如果命令行中使用-e赋值变量内容，命令行的优先级高
```
`范例：可以自己定义变量变量文件，实现安装包和程序名的不固定`
```bash
自己定义变量文件
[root@ansible playbook]# cat vars.yml 
name1: httpd
name2: httpd

通过playbook调用变量文件
[root@ansible playbook]# vim httpd.yml 

---
- hosts: 192.168.131.187
  remote_user: root
  vars_files:
     - vars.yml
  tasks:
    - name: install package
      yum: name={{name1}}
    - name: service
      tags: service
      service: name={{name2}} state=started enabled=yes

[root@ansible playbook]# ansible-playbook httpd.yml 
```
`范例：在/etc/ansible/hosts文件中定义变量`
```bash
定义的变量
[root@ansible playbook]# vim /etc/ansible/hosts 
192.168.131.129 nodename=centos6_1
192.168.131.187 nodename=centos7_1

playbook
[root@ansible playbook]# vim test3.yml

---
- hosts: 192.168.131.187
  remote_user: root

  tasks:
    - name: hostname
      hostname: name={{nodename}}

[root@ansible playbook]# ansible-playbook --check test3.yml 
[root@ansible playbook]# ansible 192.168.131.187 -m shell -a 'hostname'
192.168.131.187 | SUCCESS | rc=0 >>
centos7_1
```
`范例：在hosts主机清单中定义组变量：组变量是指赋予给指定组内所有主机上的在playbook中可用的变量 `
```bash
[root@ansible playbook]# vim /etc/ansible/hosts 
[appsrvs]
192.168.131.129 nodename=centos6_1
192.168.131.187 nodename=centos7_1

[appsrvs:vars]
suffix=daizhedu.com

[root@ansible playbook]# cat test3.yml 
---
- hosts: appsrvs
  remote_user: root
  
  tasks:
    - name: hostname
      hostname: name={{nodename}}.{{suffix}}

[root@ansible playbook]# ansible-playbook  test3.yml 
[root@ansible playbook]# ansible appsrvs -m shell -a 'hostname'
192.168.131.129 | SUCCESS | rc=0 >>
centos6_1.daizhedu.com

192.168.131.187 | SUCCESS | rc=0 >>
centos7_1.daizhedu.com
```
或许在多个地方定义变量：  
命令行-e 优先级高    
尽可能变量统一定义

## `模板templates`

`范例：如何实现templates   (演示范例为nginx 基于epel源，所有管理机和被管理机都要连接互联网)`
```bash
[root@ansible playbook]# mkdir templates
[root@ansible playbook]# cp /etc/nginx/nginx.conf /root/playbook/templates/nginx.conf.j2

首先将所有被控制端安装上nginx服务，编写一个playbook
[root@ansible playbook]# vim nginx.yml 

- hosts: appsrvs
  remote_user: root

  tasks:
    - name: install
      yum: name=nginx
    - name: service
      service: name=nginx state=restarted enabled=yes

[root@ansible playbook]# ansible-playbook  nginx.yml 

[root@ansible playbook]# ansible appsrvs -a 'ps aux | grep nginx'
192.168.131.129 | SUCCESS | rc=0 >>
root       4485  0.0  0.1 108944  2048 ?        Ss   05:31   0:00 nginx: master process /usr/sbin/nginx -c /etc/nginx/nginx.conf
nginx      4486  0.0  0.1 109368  2720 ?        S    05:31   0:00 nginx: worker process                   
nginx      4487  0.0  0.1 109368  2772 ?        S    05:31   0:00 nginx: worker process       

修改cpu worker进程、几颗cpu,几个worker进程
修改worker进程 ，是来自cpu的两倍
系统版本不同，cup个数不同，需要的配置文件也不同

[root@ansible playbook]# pwd
/root/playbook
[root@ansible ~]# tree /root/playbook/
/root/playbook/

├── nginx.yml
├── templates
    └── nginx.conf

[root@ansible playbook]# vim nginx.yml 

- hosts: appsrvs
  remote_user: root

  tasks:
    - name: install
      yum: name=nginx
    - name: template
      template: src=nginx.conf.j2 dest=/etc/nginx/nginx.conf
      notify: restart
    - name: service
      service: name=nginx state=started enabled=yes

  hanlers:
    - name: restart
      service: name=nginx state=restarted

查看服务启动的worker 对应的cpu个数(cpu个数影响了生成的worker进程的个数，默认为cpu个数有几个就生成相对应的worker进程个数)
[root@ansible ~]# ansible 192.168.131.129 -m setup -a 'filter=ansible_processor_count'
192.168.131.129 | SUCCESS => {
    "ansible_facts": {
        "ansible_processor_count": 2
    }, 
    "changed": false
}


修改堡垒机上放在templates目录下的nginx的配置文件，修改内容，调用模板中的变量、运算
实现worker的进程，是cup个数的两倍
[root@ansible playbook]# ls /root/playbook/templates/
nginx.conf.j2

[root@ansible playbook]# vim templates/nginx.conf.j2 
worker_processes{{ansible_processor_count*2}};     修改为worker为cpu的2倍

[root@ansible playbook]# ansible-playbook --check nginx.yml 

再次查看worker对应的cpu个数，是否发生修改
[root@ansible ~]# ansible 192.168.131.129 -m setup -a 'filter=ansible_processor_count'

```
`范例：修改主机清单，添加自己定义的变量`
```bash
[root@ansible templates]# vim /etc/ansible/hosts 
192.168.131.129 nodename=centos6_1 port=81

修改j2模板，修改自己定义的修改的监听端口
[root@ansible templates]# pwd
/root/playbook/templates

[root@ansible templates]# vim nginx.conf.j2 

    server {
        listen       {{port}} default_server;

[root@ansible playbook]# vim nginx.yml 

- hosts: 192.168.131.129
  remote_user: root

  tasks:
    - name: install
      yum: name=nginx
    - name: template
      template: src=nginx.conf.j2 dest=/etc/nginx/nginx.conf
      notify: restart
    - name: service
      service: name=nginx state=started enabled=yes

  hanlers:
    - name: restart
      service: name=nginx state=restarted

[root@ansible playbook]# ansible-playbook -C nginx.yml 

如果playbook和主机清单中都定义变量，名称相同，执行playbook时，playbook生效，但是优先级最高的是命令行-e
```

## `迭代：with_items `重复执行

`范例：在被管理端上创建多个用户`
```bash
[root@ansible playbook]# cat useradd.yml 
---
- hosts: 192.168.131.129
  remote_user: root

  tasks:
    - name: add user
      user: name={{item}} state=present(省略不写默认，创建) groups=haha
      with_items:
        - aa
        - bb

[root@ansible playbook]# ansible-playbook -C useradd.yml 
```
`范例：在被管理终端，干两件事，第一创建用户，第二创建组，创建第一个组，作为第一个用户的主组，第二个创建的组，作为第二个用户的主组...`字典
```bash
[root@ansible playbook]# !vim
vim group.user.yml

---
- hosts: appsrvs
  remote_user: root

  tasks:
    - name: create groups
      group: name={{item}}
      with_items:
        - g1
        - g2
        - g3
    - name: create users
      user: name={{item.name}} group={{item.group}}
      with_items:
        - {name: 'user1',group: 'g1'}
        - {name: 'user2',group: 'g2'}
        - {name: 'user3',group: 'g3'}

[root@ansible playbook]# ansible-playbook -C group.user.yml 

```
`范例：批量在被管理端安装多个程序`
```bash
[root@ansible playbook]# vim anzhuang.yml

---
- hosts: appsrvs
  remote_user: root

  tasks:
    - name: install app
      yum: name={{item}}
      with_items:
        - app1
        - app2
        - app3
```

## `Playbook中template for if `
`模板中调用for循环`

范例：
```bash
编写playbook,在yml文件中定义变量，在j2文件中引用变量的值

[root@ansible playbook]# vim test_for.yml

---
- hosts: 192.168.131.129
  remote_user: root
  vars:
    ports:
      - 81
      - 82
      - 83

   tasks:
     - name: template
       template: src=test_for.conf.j2 dest=/data/test_for.conf

准备j2文件

[root@ansible playbook]# vim /root/playbook/templates/test_for.conf.j2

{% for i in ports %}
server{
        listen {{i}}
}
{% endfor %}

[root@ansible playbook]# ansible-playbook -C test_for.yml

[root@centos6_1 data]# cat test_for.conf 
server{
        listen {{81}}
}
server{
        listen {{82}}
}
server{
        listen {{83}}
}

进一步改进：将playbook文件，中的端口，写成字典格式
[root@ansible playbook]# vim test_for.yml 

- hosts: 192.168.131.129
  remote_user: root
  vars:
    ports:
      - listen_port: 81
      - listen_port: 82
      - listen_port: 83

  tasks:
    - name: template
      template: src=test_for.conf.j2 dest=/data/test_for.conf

[root@ansible playbook]# vim /root/playbook/templates/test_for.conf.j2 

{% for i in ports %}
server{
        listen {{i.listen_port}}
}
{% endfor %}

[root@centos6_1 data]# cat test_for.conf 
server{
        listen 81
}
server{
        listen 82
}
server{
        listen 83
}


进一步实现生成的文件中，类似三台主机的写法
编写playbook
[root@ansible playbook]# vim /root/playbook/test_for.yml 

- hosts: 192.168.131.129
  remote_user: root
  vars:
    vhosts:
      - host1：
        listen_port: 81
        hostname: www.host1.txt
        dirname: /data/www1
      - host2：
        listen_port: 82
        hostname: www.host2.txt
        dirname: /data/www2
      - host3：
        listen_port: 83
        hostname: www.host3.txt
        dirname: /data/www3
  tasks:
    - name: template
      template: src=test_for.conf.j2 dest=/data/test_for.conf

编写j2文件格式
[root@ansible playbook]# vim /root/playbook/templates/test_for.conf.j2 

{% for i in vhosts %}
server{
        listen {{i.listen_port}}
        servername {{i.hostname}}
        root {{i.dirname}}
}
{% endfor %}

[root@ansible playbook]# ansible-playbook test_for.yml 

在客户端查看
[root@centos6_1 data]# cat test_for.conf 
server{
        listen 81
        servername www.host1.txt
        root /data/www1
}
server{
        listen 82
        servername www.host2.txt
        root /data/www2
}
server{
        listen 83
        servername www.host3.txt
        root /data/www3
}
```
`使用if进行条件判断`
```bash
[root@ansible playbook]# cat test_for.yml 
- hosts: 192.168.131.129
  remote_user: root
  vars:
    vhosts:
      - host1:
        listen_port: 81
        host_name: www.host1.txt
        dirname: /data/www1
      - host2:
        listen_port: 82
        #host_name: www.host2.txt
        dirname: /data/www2
      - host3:
        listen_port: 83    
        host_name: www.host3.txt
        dirname: /data/www3
  tasks:
    - name: template
      template: src=test_for.conf.j2 dest=/data/test_for.conf

[root@ansible ~]# vim /root/playbook/templates/test_for.conf.j2 
[root@ansible playbook]# vim templates/test_for.conf.j2 

{% for i in vhosts %}
server{
        listen {{i.listen_port}}
{% if i.host_name is defined %}
#有下面此行则执行，没有则跳过
        server_name {{i.host_name}}
{%endif%}
        root {{i.dirname}}
}
{% endfor %}

在客户端查看执行结果
[root@centos6_1 ~]# cat /data/test_for.conf 
server{
        listen 81
        server_name www.host1.txt
        root /data/www1
}
server{
        listen 82
        root /data/www2
}
server{
        listen 83
        server_name www.host3.txt
        root /data/www3
}
```
## `roles角色`

```bash
角色路径很重要
[root@ansible playbook]# pwd
/root/playbook
[root@ansible playbook]# mkdir /root/playbook/roles

[root@ansible playbook]# tree /root/playbook/roles/
/root/playbook/roles/
├── httpd 
├── mysql
└── nginx

[root@ansible playbook]# mkdir /root/playbook/roles/{httpd,nginx,mysql}/{tasks,files,templates,handlers,vars}

[root@ansible playbook]# tree /root/playbook/roles/
/root/playbook/roles/
├── httpd
│   ├── files        相关文件
│   ├── handlers     触发执行
│   ├── tasks        命令
│   ├── templates    模板
│   └── vars         变量
├── mysql  
│   ├── files
│   ├── handlers
│   ├── tasks
│   ├── templates
│   └── vars
└── nginx
    ├── files
    ├── handlers
    ├── tasks
    ├── templates
    └── vars

使用角色的方式，来安装httpd服务

查看目录内文件结构
[root@ansible ~]# cd /root/playbook/roles/httpd/
[root@ansible httpd]# tree
.
├── files
├── handlers
├── tasks
├── templates
└── vars

第一步：建立tasks任务
[root@ansible ~]# cd /root/playbook/roles/httpd/tasks/
[root@ansible tasks]# touch user.yml group.yml config.yml install.yml service.yml

创建组
[root@ansible tasks]# vim group.yml 
- name: create group
  group: name=apache system=yes gid=80
创建用户
[root@ansible tasks]# vim user.yml 
- name: create user
  user: name=apache uid=80 shell=/sbin/nolgin home=/usr/bin/share/httpd syste
m=yes
安装包
[root@ansible tasks]# vim install.yml 
- name: install package
  yum: name=httpd
拷贝配置文件
[root@ansible tasks]# vim config.yml 
- name: config file
  copy: src=httpd.conf dest=/etc/httpd/conf backup=yes（将配置文件放在files文件中，不需要填写文件路径）
启动服务
[root@ansible tasks]# vim service.yml 
- name: service
  tags: service
  service: name=httpd state=started enabled=yes

定义执行顺序总入口
[root@ansible tasks]# touch main.yml
[root@ansible tasks]# vim main.yml 
- include: group.yml
- include: user.yml
- include: install.yml
- include: config.yml
- include: service.yml

拷贝file文件
[root@ansible httpd]# cp /etc/httpd/conf/httpd.conf files/

查看文件结构
[root@ansible httpd]# tree /root/playbook/roles/httpd/
/root/playbook/roles/httpd/
├── files
│   └── httpd.conf
├── handlers
├── tasks
│   ├── config.yml
│   ├── group.yml
│   ├── install.yml
│   ├── main.yml
│   ├── service.yml
│   └── user.yml
├── templates
└── vars

准备验证，可以将cp过来的放在files目录中的配置文件端口进行修改，差看实验变动

创建playbook调用此角色（要求playbook必须放在与角色平级的路径下）
[root@ansible playbook]# pwd
/root/playbook
[root@ansible playbook]# ls
roles 
[root@ansible playbook]# cat httpd_roles.yml 
- hosts: 192.168.131.191
  remote_user: root

  roles:
    - role: httpd

再次查看目录文件结构
[root@ansible playbook]# tree /root/playbook/httpd_roles.yml  roles/httpd/
/root/playbook/httpd_roles.yml [error opening dir]
roles/httpd/
├── files
│   └── httpd.conf
├── handlers
├── tasks
│   ├── config.yml
│   ├── group.yml
│   ├── install.yml
│   ├── main.yml
│   ├── service.yml
│   └── user.yml
├── templates
└── vars
5 directories, 7 files

执行
[root@ansible playbook]# ansible-playbook httpd_roles.yml 

设置触发
如果配置文件发生修改则触发handlers的执行
[root@ansible httpd]# vim tasks/config.yml 

- name: config file
  copy: src=httpd.conf dest=/etc/httpd/conf backup=yes
  notify: restart

[root@ansible httpd]# vim handlers/restart.yml

- name: restart
  service: name=httpd state=restarted

因为这是触发，只有满足此条件才会执行，无需再main.yml中定义，直接运行此playbook即可

```
`最小安装，默认没有tab补全，可以安装 bash-completion`

`添加网卡，无需重启，启用网卡是nmcli connection up Wired\ connection\ 1 `

![给角色贴标签](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/ansible%20%E7%BB%99%E8%A7%92%E8%89%B2%E8%B4%B4%E6%A0%87%E7%AD%BE.png)

## memcaches 缓存服务器
```bash
[root@ansible playbook]# yum install memcached

[root@ansible playbook]# cat /etc/sysconfig/memcached 
PORT="11211"
USER="memcached"
MAXCONN="1024"
CACHESIZE="64"     默认缓存值
OPTIONS=""

N台主机当缓存服务器，由于每个服务器存在内存的大小不一致
参考内存的总值比 当缓存



拷贝模板
[root@ansible memcached]# cp /etc/sysconfig/memcached /root/playbook/templates/memcached.j2
[root@ansible memcached]# vim !$
vim /root/playbook/templates/memcached.j2

PORT="11211"
USER="memcached"
MAXCONN="1024"
CACHESIZE="{{ansible_memtotal_mb//4}}"（取整）
OPTIONS=""


[root@ansible playbook]# cd roles/
[root@ansible roles]# ls
[root@ansible roles]# mkdir memcached
[root@ansible roles]# ls
memcached
[root@ansible memcached]# mkdir {tasks,files,templates,handlers,vars}

[root@ansible tasks]# vim config.yml
- name: config file
  template: sec=memcached.j2 dest=/etc/sysconfig/memcache backup=yes

[root@ansible tasks]# vim install.yml
- name: insatll package
  yum: name=memcached

[root@ansible tasks]# vim service.yml
- name: service
  service: name=memcached state=started enabled=yes

[root@ansible tasks]# vim main.yml
- include: install.yml
- include: config.yml
- include: service.yml

[root@ansible memcached]# tree
├── files
├── handlers
├── tasks
│   ├── config.yml
│   ├── install.yml
│   ├── main.yml
│   └── service.yml
├── templates
└── vars

[root@ansible ~]# cd /root/playbook/
[root@ansible playbook]# vim memcached.yml
- hosts: 192.168.131.191
  remote_user: root

  roles:
    - role: memcached

```