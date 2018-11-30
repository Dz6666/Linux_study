---
title: 网络配置工具
tags: linux
categories: linux
---

## CentOS 7网络配置工具

CentOS7主机名

&ensp;&ensp;配置文件:/etc/hostname ，默认没有此文件，通过DNS反向解析获取主机 名，主机名默认为：localhost.localdomain

&ensp;&ensp;显示主机名信息 

&ensp;&ensp;&ensp;&ensp;hostname 

&ensp;&ensp;&ensp;&ensp;hostnamectl status

设置主机名 

&ensp;&ensp;hostnamectl set-hostname centos7.magedu.com 

删除文件

&ensp;&ensp;/etc/hostname，恢复主机名localhost.localdomain

CentOS 7网络配置工具

&ensp;&ensp;图形工具：nm-connection-editor

&ensp;&ensp;字符配置tui工具：nmtui

&ensp;&ensp;命令行工具：nmcli


## nmcli命令


地址配置工具：nmcli

nmcli [ OPTIONS ] OBJECT { COMMAND | help } 

&ensp;&ensp;device - show and manage network interfaces 

&ensp;&ensp;nmcli device help 

&ensp;&ensp;connection - start, stop, and manage network connections 

&ensp;&ensp;nmcli connection help

修改IP地址等属性： 

nmcli connection modify IFACE [+|-]
setting.property value 

&ensp;&ensp;setting.property: 

&ensp;&ensp;ipv4.addresses 
ipv4.gateway 

&ensp;&ensp;ipv4.dns1   ipv4.methodmanual | auto

修改配置文件执行生效：

&ensp;&ensp;systemctl restart network 

&ensp;&ensp;nmcli con reload 

nmcli命令生效： nmcli con down eth0 ;nmcli con up eth0


&ensp;&ensp;`NeworkManager`是管理和监控网络设置的守护进程 设备即网络接口，连接是对网络接口的配置，一个网络接口可有多个连接配置， 但同时只有一个连接配置生效 显示所有包括不活动连接 nmcli con show

显示所有活动连接 

&ensp;&ensp;nmcli con show --active 

显示网络连接配置 

&ensp;&ensp;nmcli con show  "System eth0“

显示设备状态 

&ensp;&ensp;nmcli dev  status 


显示网络接口属性 

&ensp;&ensp;nmcli dev show eth0

创建新连接default，IP自动通过dhcp获取 

&ensp;&ensp;nmcli con add con-name default  type Ethernet ifname eth0

删除连接 

&ensp;&ensp;nmcli con del default 

创建新连接static ，指定静态IP，不自动连接 

&ensp;&ensp;nmcti con add con-name static   ifname eth0 autoconnect no type 

Ethernet ipv4.addresses 172.25.X.10/24 ipv4.gateway   172.25.X.254 


启用static连接配置 

&ensp;&ensp;nmcli con up static

启用default连接配置 

&ensp;&ensp;nmcli con up default 

查看帮助 

&ensp;&ensp;nmcli con add help 


修改连接设置 

&ensp;&ensp;nmcli con mod“static” connection.autoconnect no 

&ensp;&ensp;nmcli con mod “static”  ipv4.dns 172.25.X.254 

&ensp;&ensp;nmcli con mod “static”  +ipv4.dns  8.8.8.8 

&ensp;&ensp;nmcli 
con mod “static”  -ipv4.dns  8.8.8.8 nmcli con 

&ensp;&ensp;mod “static” ipv4.addresses “172.25.X.10/24 

 172.25.X.254” 
 
&ensp;&ensp;nmcli con mod “static”  +ipv4.addresses 10.10.10.10/16

&ensp;&ensp;DNS设置，存放在/etc/resolv.conf文件中 PEERDNS=no 表示当IP通过dhcp自动获取时，dns仍是手动设置，不自动获取 等价于下面命令： nmcli con mod “system eth0” ipv4.ignore-auto-dns yes



修改连接配置后，需要重新加载配置 

&ensp;&ensp;nmcli con reload 

&ensp;&ensp;nmcli con down “system eth0” 可被自动激活 

&ensp;&ensp;nmcli con up  “system eth0” nmcli dev dis eth0 禁用网卡，访止被自动激活

图形工具 

nm-connection-editor 

字符工具 

nmtui nmtui-connect   

nmtui-edit      

nmtui-hostname 