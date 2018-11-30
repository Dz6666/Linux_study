---
title: ansible
tags: linux服务
categories: linux服务
---
![ansible](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/ansible%20LOG.png)

运维自动化发展历程及技术应用 

Ansible命令使用 

Ansible常用模块详解 

YAML语法简介 

Ansible playbook基础 

Playbook变量、tags、handlers使用 

Playbook模板templates 

Playbook条件判断 when 

Playbook字典 with_items 

Ansible Roles 

![自动化发展历史](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E8%87%AA%E5%8A%A8%E5%8C%96%E5%8F%91%E5%B1%95%E5%8E%86%E5%8F%B2.png)
![](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E8%87%AA%E5%8A%A8%E5%8C%96%E5%8E%86%E5%8F%B22.png)
![工程师核心职能](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E8%87%AA%E5%8A%A8%E5%8C%96%E5%8E%86%E5%8F%B23.png)
![工程师职能划分](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/%E8%BF%90%E7%BB%B4%E8%81%8C%E8%83%BD.png)

## 企业实际应用场景分析 

Dev开发环境  

&ensp;&ensp;使用者：程序员  

&ensp;&ensp;功能：程序员开发软件，测试BUG的环境  

&ensp;&ensp;管理者：程序员 

测试环境  

&ensp;&ensp;使用者：QA测试工程师  

&ensp;&ensp;功能：测试经过Dev环境测试通过的软件的功能  

&ensp;&ensp;管理者：运维  

&ensp;&ensp;说明：测试环境往往有多套,测试环境满足测试功能即可，不宜过多  

&ensp;&ensp;1、测试人员希望测试环境有多套,公司的产品多产品线并发，即多个版本， 意味着多个版本同步测试  

&ensp;&ensp;2、通常测试环境有多少套和产品线数量保持一样

发布环境：代码发布机，有些公司为堡垒机（安全屏障）  

&ensp;&ensp;使用者：运维  

&ensp;&ensp;功能：发布代码至生产环境  

&ensp;&ensp;管理者：运维（有经验）  

&ensp;&ensp;发布机：往往需要有2台（主备） 

生产环境  

v使用者：运维，少数情况开放权限给核心开发人员，极少数公司将权限完全 开放给开发人员并其维护  

&ensp;&ensp;功能：对用户提供公司产品的服务  

&ensp;&ensp;管理者：只能是运维  

&ensp;&ensp;生产环境服务器数量：一般比较多，且应用非常重要。往往需要自动工具协 助部署配置应用 

灰度环境（生产环境的一部分）  

&ensp;&ensp;使用者：运维  

&ensp;&ensp;功能：在全量发布代码前将代码的功能面向少量精准用户发布的环境,可基 于主机或用户执行灰度发布  

&ensp;&ensp;案例：共100台生产服务器，先发布其中的10台服务器，这10台服务器就 是灰度服务器  

&ensp;&ensp;管理者：运维  

&ensp;&ensp;灰度环境：往往该版本功能变更较大，为保险起见特意先让一部分用户优化 体验该功能，待这部分用户使用没有重大问题的时候，再全量发布至所有服务器 

## 程序发布 

程序发布要求：  

&ensp;&ensp;不能导致系统故障或造成系统完全不可用  

&ensp;&ensp;不能影响用户体验 

预发布验证：  

&ensp;&ensp;新版本的代码先发布到服务器（跟线上环境配置完全相同，只是未接入到调度器） 

灰度发布：  

&ensp;&ensp;基于主机，用户，业务 

发布路径：  

&ensp;&ensp;/webapp/tuangou  

&ensp;&ensp;/webapp/tuangou-1.1  

&ensp;&ensp;/webapp/tuangou-1.2  

发布更新
100台 v1.0  
90 up v1.0  
10 down v1.0 --升级->v2.0  
/app/pp-1.0 ---软连接-->/app/pp  
升级  
/app/pp-2.0---新建连接->/app/aa  
90 up v1.0  
10 up v2.0  
.....逐渐上线...灰度发布（金丝雀发布）.....

发布方式  
用户类型  
地区  等

蓝绿发布  
主备两套环境  
主：活动，绿  
备：非活动，蓝  
备用升级，互换化境状态

发布过程：在调度器上下线一批主机(标记为maintanance状态) --> 关闭服务 --> 部 署新版本的应用程序 --> 启动服务 --> 在调度器上启用这一批服务器 

自动化灰度发布：脚本、发布平台 

## 自动化运维应用场景 

文件传输 

应用部署 

配置管理 

任务流编排 

## 常用自动化运维工具 


`Ansible:python,Agentless,中小型应用环境 `

Saltstack:python，一般需部署agent，执行效率更高 

Puppet:ruby, 功能强大,配置复杂，重型,适合大型环境 

Fabric：python，agentless 

Chef: ruby,国内应用少 

Cfengine 

func 

## Ansible发展史 

Ansible 

创始人，Michael DeHaan（ Cobbler 与 Func 的作者） 

&ensp;&ensp;2012-03-09，发布0.0.1版，红帽收购 

&ensp;&ensp;2015-10-17，Red Hat宣布收购 

同类自动化工具GitHub关注程度（2016-07-10）

`特性 `

非主客 实质为集中管理，一台对多台，安装ansible的服务器为控制器，被管理的为被控制端。（被控制端安装代理程序木马agent,无代理agentless ssh key效率低）

模块化：调用特定的模块，完成特定任务 

有Paramiko，PyYAML，Jinja2（模板语言）三个关键模块 

支持自定义模块 

基于Python语言实现 

部署简单，基于python和SSH(默认已安装)，agentless 

安全，基于OpenSSH 

支持playbook（剧本）编排任务 

幂等性：一个任务执行1遍和执行n遍效果一样，不因重复执行带来意外情况 

无需代理不依赖PKI（无需ssl） 

可使用任何编程语言写模块 

YAML格式，编排任务，支持丰富的数据结构 

较强大的多层解决方案 

![ansible架构](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/ansible%E6%9E%B6%E6%9E%84.png)
![ansible工作原理](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/ansible%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.png)

## Ansible主要组成部分 

ANSIBLE PLAYBOOKS：任务剧本（任务集），编排定义Ansible任务集的配置 文件，由Ansible顺序依次执行，通常是JSON格式的YML文件 

INVENTORY：Ansible管理主机的清单/etc/anaible/hosts 

MODULES：Ansible执行命令的功能模块，多数为内置核心模块，也可自定义 

PLUGINS：模块功能的补充，如连接类型插件、循环插件、变量插件、过滤插 件等，该功能不常用 

API：供第三方程序调用的应用程序编程接口 

ANSIBLE：组合INVENTORY、API、MODULES、PLUGINS的绿框，可以理解 为是ansible命令工具，其为核心执行工具 

Ansible命令执行来源： 

&ensp;&ensp;USER，普通用户，即SYSTEM ADMINISTRATOR 

&ensp;&ensp;CMDB（配置管理数据库） API 调用 

&ensp;&ensp;PUBLIC/PRIVATE CLOUD API调用 

&ensp;&ensp;USER-> Ansible Playbook -> Ansibile 

利用ansible实现管理的方式： 

&ensp;&ensp;Ad-Hoc 即ansible命令，主要用于临时命令使用场景 

&ensp;&ensp;Ansible-playbook 主要用于长期规划好的，大型项目的场景，需要有前提 的规划 

Ansible-playbook（剧本）执行过程： 

&ensp;&ensp;将已有编排好的任务集写入Ansible-Playbook 

&ensp;&ensp;通过ansible-playbook命令分拆任务集至逐条ansible命令，按预定规则逐 条执行 

Ansible主要操作对象：  

&ensp;&ensp;HOSTS主机 

&ensp;&ensp;NETWORKING网络设备 

注意事项 

&ensp;&ensp;执行ansible的主机一般称为主控端，中控，master或堡垒机 

&ensp;&ensp;主控端Python版本需要2.6或以上 

&ensp;&ensp;被控端Python版本小于2.4需要安装python-simplejson 

&ensp;&ensp;被控端如开启SELinux需要安装libselinux-python 

&ensp;&ensp;windows不能做为主控端

## 安装 

`rpm包安装: EPEL源 `

&ensp;&ensp;yum install ansible 

`编译安装: `

&ensp;&ensp;yum -y install 

&ensp;&ensp;python-jinja2 PyYAML python-paramiko python-babel python-crypto 

&ensp;&ensp;tar xf ansible-1.5.4.tar.gz 

&ensp;&ensp;cd ansible-1.5.4 

&ensp;&ensp;python setup.py build 

&ensp;&ensp;python setup.py install 

&ensp;&ensp;mkdir /etc/ansible 

&ensp;&ensp;cp -r examples/* /etc/ansible 

`Git方式: `   

&ensp;&ensp;git clone git://github.com/ansible/ansible.git --recursive    

&ensp;&ensp;cd ./ansible    

&ensp;&ensp;source ./hacking/env-setup 

`pip安装：` pip是安装Python包的管理器，类似yum    

&ensp;&ensp;yum install python-pip python-devel    

&ensp;&ensp;yum install gcc glibc-devel zibl-devel  rpm-bulid openssl-devel    

&ensp;&ensp;pip install  --upgrade pip    

&ensp;&ensp;pip install ansible --upgrade 

&ensp;&ensp;确认安装： ansible --version 

## 相关文件 

`配置文件` 

&ensp;&ensp;/etc/ansible/ansible.cfg 主配置文件，配置ansible工作特性 

&ensp;&ensp;/etc/ansible/hosts 主机清单 

&ensp;&ensp;/etc/ansible/roles/ 存放角色的目录 

`程序` 

&ensp;&ensp;/usr/bin/ansible 主程序，临时命令执行工具 

&ensp;&ensp;/usr/bin/ansible-doc 查看配置文档，模块功能查看工具 

&ensp;&ensp;/usr/bin/ansible-galaxy 下载/上传优秀代码或Roles模块的官网平台 

&ensp;&ensp;/usr/bin/ansible-playbook 定制自动化任务，编排剧本工具/usr/bin/ansiblepull 远程执行命令的工具 

&ensp;&ensp;/usr/bin/ansible-vault  文件加密工具 

&ensp;&ensp;/usr/bin/ansible-console  基于Console界面与用户交互的执行工具

`主机清单inventory` 

&ensp;&ensp;Inventory 主机清单    

&ensp;&ensp;ansible的主要功用在于批量主机操作，为了便捷地使用其中的部分主机，可以 在inventory file中将其分组命名 

&ensp;&ensp;默认的inventory file为/etc/ansible/hosts 

&ensp;&ensp;inventory file可以有多个，且也可以通过Dynamic Inventory来动态生成

`/etc/ansible/hosts文件格式` 

inventory文件遵循INI文件风格，中括号中的字符为组名。可以将同一个主机 同时归并到多个不同的组中；此外，当如若目标主机使用了非默认的SSH端口， 还可以在主机名称之后使用冒号加端口号来标明  

&ensp;&ensp;ntp.magedu.com  

&ensp;&ensp;[webservers]  

&ensp;&ensp;www1.magedu.com:2222  

&ensp;&ensp;www2.magedu.com  

&ensp;&ensp;[dbservers]  

&ensp;&ensp;db1.magedu.com  

&ensp;&ensp;db2.magedu.com  

&ensp;&ensp;db3.magedu.com 

如果主机名称遵循相似的命名模式，还可以使用列表的方式标识各主机 

示例： 

&ensp;&ensp;[websrvs] 

&ensp;&ensp;www[01:100].example.com 
 
&ensp;&ensp;[dbsrvs] 

&ensp;&ensp;db-[a:f].example.com 

## `ansible 配置文件` 

Ansible 配置文件/etc/ansible/ansible.cfg （一般保持默认） 

&ensp;&ensp;[defaults] 

&ensp;&ensp;#inventory      = /etc/ansible/hosts  

&ensp;&ensp;# 主机列表配置文件 #library  = /usr/share/my_modules/ # 库文件存放目录  

&ensp;&ensp;#remote_tmp  = $HOME/.ansible/tmp  #临时py命令文件存放在远程主机目录 

&ensp;&ensp;#local_tmp      = $HOME/.ansible/tmp # 本机的临时命令执行目录 

&ensp;&ensp;#forks          = 5   # 默认并发数 

&ensp;&ensp;#sudo_user      = root  # 默认sudo 用户 

&ensp;&ensp;#ask_sudo_pass = True  #每次执行ansible命令是否询问ssh密码 

&ensp;&ensp;#ask_pass      = True    

&ensp;&ensp;#remote_port    = 22 

&ensp;&ensp;#host_key_checking = False  # 检查对应服务器的host_key，建议取消注释 

&ensp;&ensp;#log_path=/var/log/ansible.log  #日志文件 

范例：解析ansible的配置文件
```bash
[root@ansible ~]# vim /etc/ansible/ansible.cfg
[defaults]                                   以下默认值 
                                        
# some basic default values...                   
                                           
#inventory      = /etc/ansible/hosts         定义主机清单路径  
#library        = /usr/share/my_modules/     库文件
#module_utils   = /usr/share/my_module_utils/工具存放路径
#remote_tmp     = ~/.ansible/tmp             远程主机临时文件  
#local_tmp      = ~/.ansible/tmp             本地主机临时文件
#forks          = 5                          并发执行数
#poll_interval  = 15                         拉取间隔
#sudo_user      = root                       以谁的身份在远程执行
#ask_sudo_pass = True                        是否询问sudo口令（默认询问）
#ask_pass      = True                        是否询问执行身份的口令（默认询问）
#transport      = smart                      传输协议
#remote_port    = 22                         远程默认端口
#module_lang    = C                         
#module_set_locale = False                   使用的默认语言          
log_path = /var/log/ansible.log             日志信息，默认未启用，设置为启用

因为ansible不是一个服务，所以改完配置文件无需重启服务。
```

## `ansible系列命令 `


`Ansible系列命令  `  

ansible ansible-doc &ensp;&ensp; ansible-playbook    

&ensp;&ensp;ansible-vault &ensp;&ensp;    ansible-console&ensp;&ensp;   ansible-galaxy   &ensp;&ensp;ansible-pull  

&ensp;&ensp;ansible-doc: 显示模块帮助  

&ensp;&ensp;ansible-doc [options] [module...]  

&ensp;&ensp;-a   显示所有模块的文档   

&ensp;&ensp;-l, --list         列出可用模块    

&ensp;&ensp;-s, --snippet显示指定模块的playbook片段 

示例： 

&ensp;&ensp;ansible-doc –l  列出所有模块 

&ensp;&ensp;ansible-doc ping  查看指定模块帮助用法 

&ensp;&ensp;ansible-doc –s  ping 查看指定模块帮助用法

范例：查看ansible 模块所有的模块
```bash
查看所有的模块
[root@ansible ~]# ansible-doc -l 

列出某个模块的说明 
查看ping模块的说明
[root@ansible ~]# ansible-doc ping

模块简要说明
[root@ansible ~]# ansible-doc -s ping
```

## ansible的Host-pattern

`ansible的Host-pattern`

&ensp;&ensp;`匹配主机的列表`

&ensp;&ensp;All:表示所有的Inventory中的所有主机

&ensp;&ensp;&ensp;&ensp;ansible all -m ping

&ensp;&ensp;`*:通配符`

&ensp;&ensp;&ensp;&ensp;ansible "*" -m ping    =ansible all -m ping

&ensp;&ensp;&ensp;&ensp;ansible 192.168.1.* -m ping

&ensp;&ensp;&ensp;&ensp;ansible "srvs" -m ping 

范例：通配符使用
```bash
[root@ansible ~]# ansible 192.168.131.12* -m ping
192.168.131.129 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```
范例：列出管理机中主机清单中的所有主机
```bash
[root@ansible ~]# ansible all --list-hosts
  hosts (3):
    192.168.131.129
    192.168.131.173
    192.168.131.185
[root@ansible ~]# ansible websrvs --list-hosts
  hosts (2):
    192.168.131.129
    192.168.131.173
[root@ansible ~]# ansible appsrvs --list-hosts
  hosts (2):
    192.168.131.173
    192.168.131.185
```
&ensp;&ensp;`或关系`

&ensp;&ensp;&ensp;&ensp;ansible "websrvs:appsrvs" -m ping

&ensp;&ensp;&ensp;&ensp;ansible "192.168.131.129:192.168.131.130" -m ping

范例：实现主机清单中两个清单中或的关系执行此模板
```bash
[root@ansible ~]# ansible "appsrvs:websrvs" --list-hosts
  hosts (3):
    192.168.131.173
    192.168.131.185
    192.168.131.129
[root@ansible ~]# ansible "appsrvs:websrvs" -m ping
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

`逻辑与`  

&ensp;&ensp;ansible “websrvs:&dbsrvs” –m ping   

&ensp;&ensp;在websrvs组并且在dbsrvs组中的主机 

范例：实现主机清单中两个清单中与的关系执行此模板
```bash
[root@ansible ~]# ansible "appsrvs:&websrvs" --list-hosts
  hosts (1):
    192.168.131.173
[root@ansible ~]# ansible "appsrvs:&websrvs" -m ping
192.168.131.173 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```

`逻辑非  `

&ensp;&ensp;ansible ‘websrvs:!dbsrvs’ –m ping   

&ensp;&ensp;在websrvs组，但不在dbsrvs组中的主机  

&ensp;&ensp;注意：此处为单引号 

范例：实现主机清单中两个清单中非的关系执行此模板（取反）
```bash
此处为单引号
[root@ansible ~]# ansible 'appsrvs:!websrvs' --list-hosts
  hosts (1):
    192.168.131.185
[root@ansible ~]# ansible 'appsrvs:!websrvs' -m ping
192.168.131.185 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
```
`综合逻辑 `    

&ensp;&ensp;ansible ‘websrvs:dbsrvs:&appsrvs:!ftpsrvs’ –m ping  

`正则表达式 ` 

&ensp;&ensp;ansible “websrvs:&dbsrvs” –m ping   

&ensp;&ensp;ansible “~(web|db).*\.magedu\.com” –m ping

## `ansible命令执行过程 ` 


`ansible命令执行过程 ` 

&ensp;&ensp;1. 加载自己的配置文件 默认/etc/ansible/ansible.cfg 

&ensp;&ensp;2. 加载自己对应的模块文件，如command 

&ensp;&ensp;3. 通过ansible将模块或命令生成对应的临时py文件，并将该 文件传输至远程 服务器的对应执行用户$HOME/.ansible/tmp/ansible-tmp-数字/XXX.PY文件 

&ensp;&ensp;4. 给文件+x执行 

&ensp;&ensp;5. 执行并返回结果 

&ensp;&ensp;6. 删除临时py文件，sleep 0退出 

`执行状态： `

&ensp;&ensp;绿色：执行成功并且不需要做改变的操作

&ensp;&ensp;黄色：执行成功并且对目标主机做变更 

&ensp;&ensp;红色：执行失败 

## ansible使用示例 

示例 （visudo）

以wang用户执行ping存活检测   

&ensp;&ensp;ansible all -m ping -u wang  -k 

以wang sudo至root执行ping存活检测    

&ensp;&ensp;ansible all -m ping -u wang –b -k 

以wang sudo至mage用户执行ping存活检测    

&ensp;&ensp;ansible all -m ping -u wang –b -k --become-user mage 

以wang sudo至root用户执行ls    

&ensp;&ensp;ansible all -m command  -u wang --become-user=root -a 'ls /root'  -b -k -K 

## `ansible常用模块` 

`Command：`在远程主机执行命令，默认模块，可忽略-m选项 

&ensp;&ensp;ansible srvs -m command -a ‘service vsftpd start’  

&ensp;&ensp;ansible srvs -m command -a ‘echo magedu |passwd --stdin wang’   不成功 

&ensp;&ensp;此命令不支持 $VARNAME <  >  |  ; & 等，用shell模块实现

`Shell：`和command相似，用shell执行命令 

&ensp;&ensp;ansible srv -m shell -a ‘echo magedu |passwd –stdin wang’  

&ensp;&ensp;调用bash执行命令 类似 cat /tmp/stanley.md | awk -F‘|’ ‘{print $1,$2}’ &> /tmp/example.txt 这些复杂命令，即使使用shell也可能会失败，解决办 法：写到脚本时，copy到远程，执行，再把需要的结果拉回执行命令的机器 

`Script：运行脚本` 

&ensp;&ensp;-a "/PATH/TO/SCRIPT_FILE“ 

&ensp;&ensp;snsible websrvs  -m script -a f1.sh 

`Copy:从服务器复制文件到客户端` 

&ensp;&ensp;ansible srv -m copy -a “src=/root/f1.sh dest=/tmp/f2.sh    owner=wang  mode=600 backup=yes”     

如目标存在，默认覆盖，此处指定先备份 

&ensp;&ensp;ansible srv -m copy -a “content=‘test content\n’ dest=/tmp/f1.txt”  

&ensp;&ensp;利用内容，直接生成目标文件 

```bash
[root@ansible ~]# ansible-doc -s copy
      dest:   到目标文件
      src:    本地源文件 
      源时文件夹，目标也是文件夹
      mode:    设置权限
      group:   修改所属组
      backup:  如果目标主机存在了此文件，则先进行备份再覆盖
      content: 可以代替src,本身含义为内容，src文件，content: 文件内容
      owner:   修改所有者
```

`Fetch:从客户端取文件至服务器端,copy相反，目前fetch仅可以抓取文件，目录可先tar `

&ensp;&ensp;ansible srv -m fetch -a ‘src=/root/a.sh dest=/data/scripts’  


`File：设置文件属性` 管路目标主机的文件

&ensp;&ensp;ansible srv -m file -a "path=/root/a.sh owner=wang mode=755“ 

&ensp;&ensp;ansible web  -m file  -a ‘src=/app/testfile  dest=/app/testfile-link state=link’

`Hostname：管理主机名` 

&ensp;&ensp;ansible node1 -m hostname -a “name=websrv”  

`Cron：计划任务`     

&ensp;&ensp;支持时间：minute，hour，day，month，weekday 

&ensp;&ensp;ansible srv -m cron -a “minute=*/5 job=‘/usr/sbin/ntpdate 172.16.0.1 &>/dev/null’ name=Synctime” 创建任务 

&ensp;&ensp;ansible srv -m cron -a ‘state=absent name=Synctime’  删除任务 

`Yum：管理包` 

&ensp;&ensp;ansible srv -m yum -a ‘name=httpd state=latest’  安装 

&ensp;&ensp;ansible srv -m yum -a ‘name=httpd state=absent’  删除 
 
`Service：管理服务` 

停止服务&ensp;&ensp;ansible srv -m service -a 'name=httpd state=stopped' 

启动服务&ensp;&ensp;ansible srv -m service -a 'name=httpd state=started' 

加入启动项/关闭启动项&ensp;&ensp;ansible srv -m service -a 'name=httpd state=startes enabled=yes'

&ensp;&ensp;ansible srv –m service –a ‘name=httpd state=reloaded’ 

重新启动&ensp;&ensp;ansible srv -m service -a 'name=httpd state=restarted'   

`User：管理用户` 

&ensp;&ensp;ansible srv -m user -a 'name=user1 comment=“test user” uid=2048 home=/app/user1 group=root‘ 

&ensp;&ensp;ansible srv  -m user -a 'name=sysuser1 system=yes home=/app/sysuser1 ’ 

&ensp;&ensp;ansible srv -m user -a ‘name=user1 state=absent remove=yes‘    删除用户 及家目录等数据 

`Group：管理组` 

&ensp;&ensp;ansible  srv -m group -a "name=testgroup system=yes“ 

&ensp;&ensp;ansible  srv -m group -a "name=testgroup state=absent" 

`ansible-galaxy` 

&ensp;&ensp;连接 https://galaxy.ansible.com 下载相应的roles 

&ensp;&ensp;列出所有已安装的galaxy  

&ensp;&ensp;&ensp;&ensp;ansible-galaxy list 

安装galaxy  

&ensp;&ensp;ansible-galaxy install geerlingguy.redis 

删除galaxy  

&ensp;&ensp;ansible-galaxy remove geerlingguy.redis 

`ansible-pull` 

&ensp;&ensp;推送命令至远程，效率无限提升，对运维要求较高 

`Ansible-playbook` 

范例：

执行   
&ensp;&ensp;ansible-playbook hello.yml   

yml文件脚本内容   
&ensp;&ensp;cat  hello.yml    

&ensp;&ensp;#hello world yml file    

&ensp;&ensp;- hosts: websrvs       

&ensp;&ensp;&ensp;remote_user: root       

&ensp;&ensp;&ensp;tasks:           

&ensp;&ensp;&ensp;&ensp;- name: hello world             

&ensp;&ensp;&ensp;&ensp;command: /usr/bin/wall hello world 

`Ansible-vault` 

`功能：管理加密解密yml文件` 

&ensp;&ensp;ansible-vault [create|decrypt|edit|encrypt|rekey|view] 

&ensp;&ensp;ansible-vault encrypt hello.yml 加密 

&ensp;&ensp;ansible-vault decrypt hello.yml 解密 

&ensp;&ensp;ansible-vault view hello.yml 查看加密文件内容

&ensp;&ensp;ansible-vault edit  hello.yml 编辑加密文件 

&ensp;&ensp;ansible-vault rekey  hello.yml 修改口令 

&ensp;&ensp;ansible-vault create new.yml 创建新文件 
 
`Ansible-console：2.0+新增，可交互执行命令，支持tab `

&ensp;&ensp;root@test (2)[f:10] $   

执行用户@当前操作的主机组 (当前组的主机数量)[f:并发数]$ 

&ensp;&ensp;设置并发数： forks n  例如： forks 10 

&ensp;&ensp;切换组： cd 主机组  例如： cd web 

&ensp;&ensp;列出当前组主机列表： list 

&ensp;&ensp;列出所有的内置命令： ?或help 

&ensp;&ensp;示例： 

&ensp;&ensp;&ensp;&ensp;root@all (2)[f:5]$ list 

&ensp;&ensp;&ensp;&ensp;root@all (2)[f:5]$ cd appsrvs 

&ensp;&ensp;&ensp;&ensp;root@appsrvs (2)[f:5]$ list  

&ensp;&ensp;&ensp;&ensp;root@appsrvs (2)[f:5]$ yum name=httpd state=present 

&ensp;&ensp;&ensp;&ensp;root@appsrvs (2)[f:5]$ service name=httpd state=started 

## `playbook `

playbook是由一个或多个“play”组成的列表

play的主要功能在于将事先归并为一组的主机装扮成事先通过ansible中的task 定义好的角色。从根本上来讲，所谓task无非是调用ansible的一个module。 将多个play组织在一个playbook中，即可以让它们联同起来按事先编排的机制 同唱一台大戏 

Playbook采用YAML语言编写 

![playbook](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/playbook.png)
 
## YAML介绍 

&ensp;&ensp;YAML是一个可读性高的用来表达资料序列的格式。YAML参考了其他多种语言，包括：XML、 C语言、Python、Perl以及电子邮件格式RFC2822等。Clark Evans在2001年在首次发表了这种 语言，另外Ingy döt Net与Oren Ben-Kiki也是这语言的共同设计者 

&ensp;&ensp;YAML Ain't Markup Language，即YAML不是XML。不过，在开发的这种语言时，YAML的意 思其实是："Yet Another Markup Language"（仍是一种标记语言） 

特性  

&ensp;&ensp;YAML的可读性好  

&ensp;&ensp;YAML和脚本语言的交互性好  

&ensp;&ensp;YAML使用实现语言的数据类型  

&ensp;&ensp;YAML有一个一致的信息模型  

&ensp;&ensp;YAML易于实现  

&ensp;&ensp;YAML可以基于流来处理  

&ensp;&ensp;YAML表达能力强，扩展性好 

&ensp;&ensp;更多的内容及规范参见http://www.yaml.org 

## YAML语法简介 

&ensp;&ensp;在单一档案中，可用连续三个连字号(——)区分多个档案。另外，还有选择性的连续三 个点号( ... )用来表示档案结尾 

&ensp;&ensp;次行开始正常写Playbook的内容，一般建议写明该Playbook的功能 

&ensp;&ensp;使用#号注释代码 

&ensp;&ensp;缩进必须是统一的，不能空格和tab混用 

&ensp;&ensp;缩进的级别也必须是一致的，同样的缩进代表同样的级别，程序判别配置的级别是通过 缩进结合换行来实现的 

&ensp;&ensp;YAML文件内容是区别大小写的，k/v的值均需大小写敏感 

&ensp;&ensp;k/v的值可同行写也可换行写。同行使用:分隔 

&ensp;&ensp;v可是个字符串，也可是另一个列表 

&ensp;&ensp;一个完整的代码块功能需最少元素需包括 name: task 

&ensp;&ensp;一个name只能包括一个task 

&ensp;&ensp;YAML文件扩展名通常为yml或yaml 

## YAML语法简介 

`List：列表，其所有元素均使用“-”打头 `

示例： 

&ensp;&ensp;# A list of tasty fruits 

&ensp;&ensp;- Apple 

&ensp;&ensp;- Orange 

&ensp;&ensp;- Strawberry 

&ensp;&ensp;- Mango 

`Dictionary：字典，通常由多个key与value构成` 

示例： --- 

&ensp;&ensp;# An employee record 

&ensp;&ensp;name: Example Developer 

&ensp;&ensp;job: Developer 

&ensp;&ensp;skill: Elite 

&ensp;&ensp;也可以将key:value放置于{}中进行表示，用,分隔多个key:value 

示例： 

&ensp;&ensp;--- 

&ensp;&ensp;# An employee record 

&ensp;&ensp;{name: Example Developer, job: Developer, skill: Elite} 

## YAML语法 

YAML的语法和其他高阶语言类似，并且可以简单表达清单、散列表、标量等数据结构。其结构 （Structure）通过空格来展示，序列（Sequence）里的项用"-"来代表，Map里的键值对用":"分隔 

示例 

&ensp;&ensp;name: John Smith 

&ensp;&ensp;age: 41 

&ensp;&ensp;gender: Male 

&ensp;&ensp;spouse:     

&ensp;&ensp;&ensp;&ensp;name: Jane Smith     

&ensp;&ensp;&ensp;&ensp;age: 37     

&ensp;&ensp;&ensp;&ensp;gender: Female 

&ensp;&ensp;children:     

&ensp;&ensp;&ensp;&ensp;-   name: Jimmy Smith         

&ensp;&ensp;&ensp;&ensp;age: 17         

&ensp;&ensp;&ensp;&ensp;gender: Male     

&ensp;&ensp;-   name: Jenny Smith         

&ensp;&ensp;&ensp;&ensp;age 13         

&ensp;&ensp;&ensp;&ensp;gender: Female

## `Playbook核心元素 `

Hosts   执行的远程主机列表 

Tasks   任务集 

Varniables 内置变量或自定义变量在playbook中调用 

Templates  模板，可替换模板文件中的变量并实现一些简单逻辑的文件 

Handlers  和notity结合使用，由特定条件触发的操作，满足条件方才执行，否 则不执行 

tags 标签  指定某条任务执行，用于选择运行playbook中的部分代码。ansible 具有幂等性，因此会自动跳过没有变化的部分，即便如此，有些代码为测试其 确实没有发生变化的时间依然会非常地长。此时，如果确信其没有变化，就可 以通过tags跳过此些代码片断    

ansible-playbook –t tagsname useradd.yml 

## `playbook基础组件 `

`Hosts： `

&ensp;&ensp;playbook中的每一个play的目的都是为了让某个或某些主机以某个指定的用 户身份执行任务。hosts用于指定要执行指定任务的主机，须事先定义在主机 清单中 

可以是如下形式： 

&ensp;&ensp;one.example.com 

&ensp;&ensp;one.example.com:two.example.com 

&ensp;&ensp;192.168.1.50 192.168.1.* 

&ensp;&ensp;Websrvs:dbsrvs     两个组的并集 

&ensp;&ensp;Websrvs:&dbsrvs    两个组的交集 

&ensp;&ensp;webservers:!phoenix  在websrvs组，但不在dbsrvs组       

&ensp;&ensp;示例: - hosts: websrvs：dbsrvs 
 
`remote_user: `可用于Host和task中。也可以通过指定其通过sudo的方式在远 程主机上执行任务，其可用于play全局或某任务；此外，甚至可以在sudo时使 用sudo_user指定sudo时切换的用户 

&ensp;&ensp;- hosts: websrvs    

&ensp;&ensp;&ensp;&ensp;remote_user: root   

&ensp;&ensp;tasks:    

&ensp;&ensp;&ensp;&ensp;- name: test connection      

&ensp;&ensp;&ensp;&ensp;ping:      

&ensp;&ensp;&ensp;&ensp;remote_user: magedu      

&ensp;&ensp;&ensp;&ensp;sudo: yes     默认sudo为root      
&ensp;&ensp;&ensp;&ensp;sudo_user:wang    sudo为wang

`task列表和action` 

&ensp;&ensp;play的主体部分是task list。task list中的各任务按次序逐个在hosts中指定 的所有主机上执行，即在所有主机上完成第一个任务后，再开始第二个任务 

&ensp;&ensp;task的目的是使用指定的参数执行模块，而在模块参数中可以使用变量。模 块执行是幂等的，这意味着多次执行是安全的，因为其结果均一致 

&ensp;&ensp;每个task都应该有其name，用于playbook的执行结果输出，建议其内容尽 可能清晰地描述任务执行步骤。如果未提供name，则action的结果将用于 输出 

`tasks：任务列表` 

格式：

&ensp;&ensp;(1) action: module arguments      

&ensp;&ensp;(2) module: arguments      建议使用 

&ensp;&ensp;注意：shell和command模块后面跟命令，而非key=value 

某任务的状态在运行后为changed时，可通过“notify”通知给相应的 handlers 

任务可以通过"tags“打标签，而后可在ansible-playbook命令上使用-t指定进 行调用     

示例：        

&ensp;&ensp;tasks:  

&ensp;&ensp;&ensp;&ensp;- name: disable selinux    

&ensp;&ensp;&ensp;&ensp;command: /sbin/setenforce 0

如果命令或脚本的退出码不为零，可以使用如下方式替代          

&ensp;&ensp;tasks:    

&ensp;&ensp;&ensp;&ensp;- name: run this command and ignore the result       

&ensp;&ensp;&ensp;&ensp;shell: /usr/bin/somecommand || /bin/true 

或者使用ignore_errors来忽略错误信息：          

&ensp;&ensp;tasks:    

&ensp;&ensp;&ensp;&ensp;- name: run this command and ignore the result       

&ensp;&ensp;&ensp;&ensp;shell: /usr/bin/somecommand       

&ensp;&ensp;&ensp;&ensp;ignore_errors: True 

`运行playbook的方式`  

&ensp;&ensp;ansible-playbook <filename.yml> ... [options] 

常见选项  

&ensp;&ensp;--check 只检测可能会发生的改变，但不真正执行操作  -C

&ensp;&ensp;--list-hosts 列出运行任务的主机  

&ensp;&ensp;--limit 主机列表 只针对主机列表中的主机执行  

&ensp;&ensp;-v 显示过程  -vv  -vvv 更详细 

示例  

&ensp;&ensp;ansible-playbook  file.yml  --check 只检测  

&ensp;&ensp;ansible-playbook  file.yml    

&ensp;&ensp;ansible-playbook  file.yml  --limit websrvs

![范例](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/playbook%E8%8C%83%E4%BE%8B.png)

## Playbook示例 

`示例：sysuser.yml `

--- 
 
&ensp;&ensp;- hosts: all   

&ensp;&ensp;&ensp;&ensp;remote_user: root     

&ensp;&ensp;tasks:      

&ensp;&ensp;&ensp;&ensp;- name: create mysql user         

&ensp;&ensp;&ensp;&ensp;user:  name=mysql system=yes uid=36      

&ensp;&ensp;&ensp;&ensp;- name: create a group          

&ensp;&ensp;&ensp;&ensp;group: name=httpd system=yes 

`示例：httpd.yml `

&ensp;&ensp;- hosts: websrvs   

&ensp;&ensp;&ensp;&ensp;remote_user: root      

&ensp;&ensp;tasks:      

&ensp;&ensp;&ensp;&ensp;- name: Install httpd         

&ensp;&ensp;yum: name=httpd state=present 
 
&ensp;&ensp;&ensp;&ensp;- name: Install configure file         

&ensp;&ensp;&ensp;&ensp;copy: src=files/httpd.conf dest=/etc/httpd/conf/        

&ensp;&ensp;&ensp;&ensp;- name: start service         

&ensp;&ensp;&ensp;&ensp;service: name=httpd state=started enabled=yes 

## `handlers和notify结合使用触发条件 `

Handlers    

是task列表，这些task与前述的task并没有本质上的不同,用于当关注的资源发生 变化时，才会采取一定的操作 

Notify此action可用于在每个play的最后被触发，这样可避免多次有改变发生 时每次都执行指定的操作，仅在所有的变化发生完成后一次性地执行指定操作。 在notify中列出的操作称为handler，也即notify中调用handler中定义的操作

## Playbook中handlers使用

&ensp;&ensp;- hosts: websrvs 

&ensp;&ensp;remote_user: root 

&ensp;&ensp;tasks:    

&ensp;&ensp;&ensp;&ensp;- name: Install httpd     

&ensp;&ensp;&ensp;&ensp;yum: name=httpd state=present   

&ensp;&ensp;&ensp;&ensp;- name: Install configure file  

&ensp;&ensp;&ensp;&ensp;copy: src=files/httpd.conf dest=/etc/httpd/conf/      

&ensp;&ensp;&ensp;&ensp;notify: restart httpd          

&ensp;&ensp;&ensp;&ensp;- name: ensure apache is running           

&ensp;&ensp;&ensp;&ensp;service: name=httpd state=started enabled=yes 

&ensp;&ensp;handlers:    

&ensp;&ensp;&ensp;&ensp;- name: restart httpd        

&ensp;&ensp;&ensp;&ensp;service: name=httpd status=restarted 

![幂等性notify触发多个动作](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/notify%E8%8C%83%E4%BE%8B.png)

## `Playbook中tags使用 `将执行动作贴上标签

示例：httpd.yml 

&ensp;&ensp;- hosts: websrvs   

&ensp;&ensp;remote_user: root   

&ensp;&ensp;tasks:     

&ensp;&ensp;&ensp;&ensp;- name: Install httpd         

&ensp;&ensp;&ensp;&ensp;yum: name=httpd state=present 

&ensp;&ensp;&ensp;&ensp;- name: Install configure file         

&ensp;&ensp;&ensp;&ensp;copy: src=files/httpd.conf dest=/etc/httpd/conf/        

&ensp;&ensp;&ensp;&ensp;tags: conf 

&ensp;&ensp;&ensp;&ensp;- name: start httpd service         

&ensp;&ensp;&ensp;&ensp;tags: service         

&ensp;&ensp;&ensp;&ensp;service: name=httpd state=started enabled=yes     

&ensp;&ensp;&ensp;&ensp;ansible-playbook –t conf httpd.yml 

## `Playbook中变量使用 `

`变量名：仅能由字母、数字和下划线组成，且只能以字母开头 `

`变量来源： `

&ensp;&ensp;1 ansible setup facts 远程主机的所有变量都可直接调用 

&ensp;&ensp;2 在/etc/ansible/hosts中定义        

&ensp;&ensp;&ensp;&ensp;普通变量：主机组中主机单独定义，优先级高于公共变量        

&ensp;&ensp;&ensp;&ensp;公共（组）变量：针对主机组中所有主机定义统一变量 

&ensp;&ensp;3 通过命令行指定变量，优先级最高          

&ensp;&ensp;&ensp;&ensp;ansible-playbook –e varname=value 

&ensp;&ensp;4 在playbook中定义 

&ensp;&ensp;vars:     

&ensp;&ensp;&ensp;&ensp;- var1: value1     

&ensp;&ensp;&ensp;&ensp;- var2: value2 

&ensp;&ensp;5 在独立的变量YAML文件中定义 

&ensp;&ensp;6 在role中定义 

范例：ansible setup facts 模块中的变量

过滤变量 ansible all -m setup -a "filter=***"
```bash
ansible_processor_count   cpu数量
ansible_nodename          主机名
ansible_memtotal_mb       总内存大小
ansible_distribution_major_version 主版本号
方便：可以判断版本号，来发送相对应的配置文件
```

`变量命名`   

&ensp;&ensp;变量名仅能由字母、数字和下划线组成，且只能以字母开头 

`变量定义：key=value `

&ensp;&ensp;示例：http_port=80 

`变量调用方式： `

&ensp;&ensp;通过{{ variable_name }} 调用变量，且变量名前后必须有空格，有时用 “{{ variable_name }}”才生效 

&ensp;&ensp;ansible-playbook –e 选项指定      

&ensp;&ensp;ansible-playbook test.yml -e "hosts=www user=magedu" 

## 示例：使用setup变量

示例：var.yml 

- hosts: websrvs   

remote_user: root 
 
tasks:      

&ensp;&ensp;- name: create log file        

&ensp;&ensp;file: name=/var/log/ {{ ansible_fqdn }} state=touch 
 
ansible-playbook  var.yml 

## 示例：变量 

示例：var.yml 

- hosts: websrvs  

remote_user: root   

tasks:       

&ensp;&ensp;- name: install package          

&ensp;&ensp;yum: name={{ pkname }} state=present 
 
ansible-playbook  –e pkname=httpd  var.yml 

示例：var.yml  

- hosts: websrvs   

remote_user: root   

vars:       

&ensp;&ensp;- username: user1       

&ensp;&ensp;- groupname: group1   

tasks:       

&ensp;&ensp;- name: create group         

&ensp;&ensp;group: name={{ groupname }} state=present       
 
&ensp;&ensp;- name: create user          

&ensp;&ensp;user: name={{ username }} state=present 
 
ansible-playbook  var.yml     

ansible-playbook -e "username=user2 groupname=group2”  var2.yml 
 
## `变量 `

`主机变量 `

&ensp;&ensp;可以在inventory中定义主机时为其添加主机变量以便于在playbook中使用 
 
示例： 

&ensp;&ensp;[websrvs] www1.magedu.com http_port=80 

&ensp;&ensp;maxRequestsPerChild=808 www2.magedu.com http_port=8080 

&ensp;&ensp;maxRequestsPerChild=909 

`组变量 `    

&ensp;&ensp;组变量是指赋予给指定组内所有主机上的在playbook中可用的变量 

示例： 

&ensp;&ensp;[websrvs] 

&ensp;&ensp;www1.magedu.com 

&ensp;&ensp;www2.magedu.com 
 
&ensp;&ensp;[websrvs:vars] 

&ensp;&ensp;ntp_server=ntp.magedu.com 

&ensp;&ensp;nfs_server=nfs.magedu.com 
 
## 示例：变量 

`普通变量 `

&ensp;&ensp;[websrvs] 

&ensp;&ensp;192.168.99.101 http_port=8080 hname=www1 

&ensp;&ensp;192.168.99.102 http_port=80    hname=www2  

`公共（组）变量 `

&ensp;&ensp;[websvrs:vars] 

&ensp;&ensp;http_port=808 

&ensp;&ensp;mark=“_” 

&ensp;&ensp;[websrvs] 

&ensp;&ensp;192.168.99.101 http_port=8080 hname=www1 

&ensp;&ensp;192.168.99.102 http_port=80 hname=www2  

&ensp;&ensp;ansible  websvrs  –m hostname –a ‘name={{ hname }}{{ mark }}{{ http_port }}’ 

`命令行指定变量：`       

&ensp;&ensp;ansible  websvrs  –e http_port=8000 –m hostname –a      ‘name={{ hname }}{{ mark }}{{ http_port }}’ 

## 使用变量文件 

cat  vars.yml  

&ensp;&ensp;var1: httpd 

&ensp;&ensp;var2: nginx 
 
cat  var.yml 

&ensp;&ensp;- hosts: web    

&ensp;&ensp;remote_user: root    

&ensp;&ensp;vars_files:        

&ensp;&ensp;&ensp;&ensp;- vars.yml    

&ensp;&ensp;tasks:       

&ensp;&ensp;&ensp;&ensp;- name: create httpd log          

&ensp;&ensp;&ensp;&ensp;file: name=/app/{{ var1 }}.log state=touch       

&ensp;&ensp;&ensp;&ensp;- name: create nginx log          

&ensp;&ensp;&ensp;&ensp;file: name=/app/{{ var2 }}.log state=touch 

## `模板templates`

文本文件，嵌套有脚本（使用模板编程语言编写） 

Jinja2语言，使用字面量，有下面形式 

&ensp;&ensp;字符串：使用单引号或双引号 

&ensp;&ensp;数字：整数，浮点数 

&ensp;&ensp;列表：[item1, item2, ...] 

&ensp;&ensp;元组：(item1, item2, ...) 

&ensp;&ensp;字典：{key1:value1, key2:value2, ...} 

&ensp;&ensp;布尔型：true/false 

算术运算：+, -, *, /, //整除, %, ** 

比较操作：==, !=, >, >=, <, <= 

逻辑运算：and, or, not  

流表达式：For If  When 

`templates` 

templates功能：根据模块文件动态生成对应的配置文件 

templates文件必须存放于templates目录下，且命名为 .j2 结尾 

yaml/yml 文件需和templates目录平级，目录结构如下：  

./      

├── temnginx.yml     

└── templates   文件夹             
 
  └── nginx.conf.j2  这种结构调用无需写路径（路径格式很重要）
 
templates实质上是一个模板，但是不可用于ansible执行，尽可以在ansible-playbook执行


示例：

利用templates 同步nginx配置文件   

准备templates/nginx.conf.j2文件   

vim temnginx.yml 

- hosts: websrvs   

remote_user: root 
 
  tasks:      
  
  - name: template config to remote hosts    
  
  template: src=nginx.conf.j2 dest=/etc/nginx/nginx.conf 
 
ansible-playbook temnginx.yml 

## Playbook中template变更替换 

修改文件nginx.conf.j2 下面行为    

worker_processes {{ ansible_processor_vcpus }}; 

cat temnginx2.yml 

- hosts: websrvs   

remote_user: root     

tasks:       

- name: template config to remote hosts         
template: src=nginx.conf.j2 dest=/etc/nginx/nginx.conf  
 
ansible-playbook temnginx2.yml 

## Playbook中template算术运算 

算法运算： 

示例：    

vim nginx.conf.j2     

worker_processes {{ ansible_processor_vcpus**2 }};        

worker_processes {{ ansible_processor_vcpus+2 }};  

## `when `

条件测试:如果需要根据变量、facts或此前任务的执行结果来做为某task执行与 否的前提时要用到条件测试,通过when语句实现，在task中使用，jinja2的语法 格式 

when语句 

在task后添加when子句即可使用条件测试；when语句支持Jinja2表达式语法 

示例： 

tasks:        

- name: "shutdown RedHat flavored systems"           
command: /sbin/shutdown -h now           

when: ansible_os_family == "RedHat

## 示例：when条件判断 

- hosts: websrvs   

remote_user: root   

tasks:      

- name: add group nginx        

tags: user        

user: name=nginx state=present      

- name: add user nginx       

user: name=nginx state=present group=nginx   

- name: Install Nginx        

yum: name=nginx state=present      

- name: restart Nginx        

service: name=nginx state=restarted        

when: ansible_distribution_major_version == "6" 

示例：  

tasks:     

- name: install conf file to centos7   

template: src=nginx.conf.c7.j2   

when: ansible_distribution_major_version == "7" 

- name: install conf file to centos6   

template: src=nginx.conf.c6.j2   

when: ansible_distribution_major_version == "6" 

## `迭代：with_items `

迭代：当有需要重复性执行的任务时，可以使用迭代机制 

&ensp;&ensp;对迭代项的引用，固定变量名为”item“ 

&ensp;&ensp;要在task中使用with_items给定要迭代的元素列表 

&ensp;&ensp;列表格式：  

&ensp;&ensp;字符串  

&ensp;&ensp;字典 

示例 

示例： 

- name: add several users   

user: name={{ item }} state=present groups=wheel   

with_items:      

- testuser1      

- testuser2 

上面语句的功能等同于下面的语句： 

- name: add user testuser1   

user: name=testuser1 state=present groups=wheel 

- name: add user testuser2   

user: name=testuser2 state=present groups=wheel 

示例：迭代 

示例：将多个文件进行copy到被控端 

--- 
 
- hosts: testsrv   

remote_user: root     

tasks 

- name: Create rsyncd config   

copy: src={{ item }} dest=/etc/{{ item }}   

with_items:     

- rsyncd.secrets     

- rsyncd.conf  

示例：迭代嵌套子变量 

- hosts：websrvs   

remote_user: root   

tasks:      

&ensp;&ensp;- name: add some groups         

&ensp;&ensp;group: name={{ item }} state=present         

&ensp;&ensp;with_items:            

&ensp;&ensp;&ensp;&ensp;- group1            

&ensp;&ensp;&ensp;&ensp;- group2            

&ensp;&ensp;&ensp;&ensp;- group3     

&ensp;&ensp;- name: add some users        

&ensp;&ensp;user: name={{ item.name }} group={{ item.group }} state=present        

with_items:           

&ensp;&ensp;&ensp;&ensp;- { name: 'user1', group: 'group1' }           

&ensp;&ensp;&ensp;&ensp;- { name: 'user2', group: 'group2' }           

&ensp;&ensp;&ensp;&ensp;- { name: 'user3', group: 'group3' } 

## `Playbook中template for if `

{% for vhost in nginx_vhosts %} 

server { 
    
listen {{ vhost.listen | default('80 default_server') }}; 

{% if vhost.server_name is defined %} 

server_name {{ vhost.server_name }}; 

{% endif %} 
 
{% if vhost.root is defined %} 

root {{ vhost.root }}; 

{% endif %} 

## `roles角色`

roles    
![roles目录结构安排](https://raw.githubusercontent.com/Dz6666/Dz6666.github.ii/master/css/roles.png)

&ensp;&ensp;ansilbe自1.2版本引入的新特性，用于层次性、结构化地组织playbook。roles 能够根据层次型结构自动装载变量文件、tasks以及handlers等。要使用roles只需 要在playbook中使用include指令即可。简单来讲，roles就是通过分别将变量、 文件、任务、模板及处理器放置于单独的目录中，并可以便捷地include它们的一 种机制。角色一般用于基于主机构建服务的场景中，但也可以是用于构建守护进程 等场景中 

复杂场景：建议使用roles，代码复用度高 

&ensp;&ensp;变更指定主机或主机组 

&ensp;&ensp;如命名不规范维护和传承成本大 

&ensp;&ensp;某些功能需多个Playbook，通过Includes即可实现 
 
`角色(roles)：角色集合` 目录结构

roles/  

&ensp;&ensp;mysql/  

&ensp;&ensp;httpd/  

&ensp;&ensp;nginx/  

&ensp;&ensp;memcached/ 

## roles目录结构 

每个角色，以特定的层级目录结构进行组织 

roles目录结构： 

&ensp;&ensp;playbook.yml 

&ensp;&ensp;roles/   

&ensp;&ensp;&ensp;&ensp;project/     

&ensp;&ensp;&ensp;&ensp;tasks/     

&ensp;&ensp;&ensp;&ensp;files/     

&ensp;&ensp;&ensp;&ensp;vars/            

&ensp;&ensp;&ensp;&ensp;templates/     

&ensp;&ensp;&ensp;&ensp;handlers/     

&ensp;&ensp;&ensp;&ensp;default/    不常用     
&ensp;&ensp;&ensp;&ensp;meta/       不常用 

## Roles各目录作用 

/roles/project/ :项目名称,有以下子目录 

files/ ：存放由copy或script模块等调用的文件 

templates/：template模块查找所需要模板文件的目录 

tasks/：定义task,role的基本元素，至少应该包含一个名为main.yml的文件； 其它的文件需要在此文件中通过include进行包含 

handlers/：至少应该包含一个名为main.yml的文件；其它的文件需要在此 文件中通过include进行包含 

vars/：定义变量，至少应该包含一个名为main.yml的文件；其它的文件需要 在此文件中通过include进行包含 

meta/：定义当前角色的特殊设定及其依赖关系,至少应该包含一个名为 main.yml的文件，其它文件需在此文件中通过include进行包含 

default/：设定默认变量时使用此目录中的main.yml文件 

## 创建role 

创建role的步骤 

(1) 创建以roles命名的目录

(2) 在roles目录中分别创建以各角色名称命名的目录，如webservers等 

(3) 在每个角色命名的目录中分别创建files、handlers、meta、tasks、 templates和vars目录；用不到的目录可以创建为空目录，也可以不创建 

(4) 在playbook文件中，调用各角色 

