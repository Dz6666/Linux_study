---
title: TCP IP协议
tags: linux
categories: linux
---
## TCP/IP 协议栈

Transmission Control Protocol/Internet Protocol 

&ensp;&ensp;传输控制协议/因特网互联协议

TCP/IP是一个Protocol Stack，包括TCP、IP、UDP、 ICMP、RIP、TELNET、FTP、SMTP、ARP等许多协议 

最早发源于美国国防部（缩写为DoD）的因特网的前身 ARPA网项目，1983年1月1日，TCP/IP取代了旧的网络 控制协议NCP，成为今天的互联网和局域网的基石和标 准,由互联网工程任务组负责维护 

共定义了四层 :应用层、传输层、internet层、网络访问层

和ISO参考模型的分层有对应关系：应用层（应用层、表示层、会话层）、传输层（传输层）、internet层（网络层路由器）、网络访问层（数据链路层 交换机、物理层hub）

## 传输层协议：tcp和udp

TCP特性


工作在传输层 

面向连接协议 

全双工协议 

半关闭

错误检查

将数据打包成段，排序 

确认机制 

数据恢复，重传 

流量控制，滑动窗口 

拥塞控制，慢启动和拥塞避免算法

## TCP包头
&ensp;&ensp;源端口、目标端口：计算机上的进程要和其他进程通信是要通过计算机端口的， 而一个计算机端口某个时刻只能被一个进程占用，所以通过指定源端口和目标 端口，就可以知道是哪两个进程需要通信。源端口、目标端口是用16位表示的， 可推算计算机的端口个数为2^16个

&ensp;&ensp;序列号：表示本报文段所发送数据的第一个字节的编号。在TCP连接中所传送的 字节流的每一个字节都会按顺序编号。由于序列号由32位表示，所以每2^32个 字节，就会出现序列号回绕，再次从 0 开始 

&ensp;&ensp;确认号：表示接收方期望收到发送方下一个报文段的第一个字节数据的编号。 也就是告诉发送方：我希望你（指发送方）下次发送的数据的第一个字节数据 的编号为此确认号 数据偏移：表示TCP报文段的首部长度，共4位，由于TCP首部包含一个长度可 变的选项部分，需要指定这个TCP报文段到底有多长。它指出 TCP 报文段的数 据起始处距离 TCP 报文段的起始处有多远。该字段的单位是32位(即4个字节为 计算单位），4位二进制最大表示15，所以数据偏移也就是TCP首部最大60字节

&ensp;&ensp; URG：表示本报文段中发送的数据是否包含紧急数据。后面的紧急指针字段（urgent pointer）只有当URG=1时才有效 

&ensp;&ensp;ACK：表示是否前面确认号字段是否有效。只有当ACK=1时，前面的确认号字段才有效。 TCP规定，连接建立后，ACK必须为1,带ACK标志的TCP报文段称为确认报文段 

&ensp;&ensp; PSH：提示接收端应用程序应该立即从TCP接收缓冲区中读走数据，为接收后续数据腾出空 间。如果为1，则表示对方应当立即把数据提交给上层应用，而不是缓存起来，如果应用程序 不将接收到的数据读走，就会一直停留在TCP接收缓冲区中 

&ensp;&ensp; RST：如果收到一个RST=1的报文，说明与主机的连接出现了严重错误（如主机崩溃），必 须释放连接，然后再重新建立连接。或者说明上次发送给主机的数据有问题，主机拒绝响应， 带RST标志的TCP报文段称为复位报文段 

&ensp;&ensp; SYN：在建立连接时使用，用来同步序号。当SYN=1，ACK=0时，表示这是一个请求建立连 接的报文段；当SYN=1，ACK=1时，表示对方同意建立连接。SYN=1，说明这是一个请求 建立连接或同意建立连接的报文。只有在前两次握手中SYN才置为1，带SYN标志的TCP报文 段称为同步报文段 

&ensp;&ensp; FIN：表示通知对方本端要关闭连接了，标记数据是否发送完毕。如果FIN=1，即告诉对方： “我的数据已经发送完毕，你可以释放连接了”，带FIN标志的TCP报文段称为结束报文段

&ensp;&ensp;窗口大小：表示现在允许对方发送的数据量，也就是告诉对方，从本报文段 的确认号开始允许对方发送的数据量，达到此值，需要ACK确认后才能再继 续传送后面数据，由Window size value * Window size scaling factor （此值在三次握手阶段TCP选项Window scale协商得到）得出此值

&ensp;&ensp;校验和：提供额外的可靠性 

&ensp;&ensp;紧急指针：标记紧急数据在数据字段中的位置 选项部分：其最大长度可根据TCP首部长度进行推算。TCP首部长度用4位表 示，选项部分最长为：(2^4-1)*4-20=40字节 

&ensp;&ensp;&ensp;&ensp;常见选项： 
&ensp;&ensp;&ensp;&ensp;&ensp;最大报文段长度：Maxium Segment Size，MSS，通常1460字节

&ensp;&ensp;&ensp;&ensp;&ensp;窗口扩大：Window Scale 

&ensp;&ensp;&ensp;&ensp;&ensp;时间戳： Timestamps


## TCP包头选项


1 最大报文段长度MSS（Maximum Segment Size） 指明自己期望对方发送TCP报文段时那个数据字段的长度。比如：1460字节。数 据字段的长度加上TCP首部的长度才等于整个TCP报文段的长度。MSS不宜设的太 大也不宜设的太小。若选择太小，极端情况下，TCP报文段只含有1字节数据，在 IP层传输的数据报的开销至少有40字节（包括TCP报文段的首部和IP数据报的首 部）。这样，网络的利用率就不会超过1/41。若TCP报文段非常长，那么在IP层传 输时就有可能要分解成多个短数据报片。在终点要把收到的各个短数据报片装配成 原来的TCP报文段。当传输出错时还要进行重传，这些也都会使开销增大。因此 MSS应尽可能大，只要在IP层传输时不需要再分片就行。在连接建立过程中，双 方都把自己能够支持的MSS写入这一字段。 MSS只出现在SYN报文中。即：MSS 出现在SYN=1的报文段中 MTU和MSS值的关系：MTU=MSS+IP Header+TCP Header 通信双方最终的MSS值=较小MTU-IP Header-TCP Header

2 窗口扩大 为了扩大窗口，由于TCP首部的窗口大小字段长度是16位，所以其表示的最大数是 65535。但是随着时延和带宽比较大的通信产生（如卫星通信），需要更大的窗口 来满足性能和吞吐率，所以产生了这个窗口扩大选项 

3 时间戳 可以用来计算RTT(往返时间)，发送方发送TCP报文时，把当前的时间值放入时间 戳字段，接收方收到后发送确认报文时，把这个时间戳字段的值复制到确认报文中， 当发送方收到确认报文后即可计算出RTT。也可以用来防止回绕序号PAWS，也可 以说可以用来区分相同序列号的不同报文。因为序列号用32为表示，每2^32个序 列号就会产生回绕，那么使用时间戳字段就很容易区分相同序列号的不同报文

## TCP协议PORT

传输层通过port号，确定应用层协议

Port number: 

tcp：传输控制协议，面向连接的协议；通信前需要建立虚拟链路；结束后拆除链路 0-65535    客户端端口随机分配



udp：User Datagram Protocol，无连接的协议 0-65535 


IANA:互联网数字分配机构（负责域名，数字资源，协议分配） 0-1023：系统端口或特权端口(仅管理员可用) ，众所周知，永久的分配给固定的 系统应用使用，22/tcp(ssh), 80/tcp(http), 443/tcp(https) 1024-49151：用户端口或注册端口，但要求并不严格，分配给程序注册为某应 用使用，1433/tcp(SqlServer), 1521/tcp(oracle),3306/tcp(mysql),11211/tcp/udp (memcached) 49152-65535：动态端口或私有端口，客户端程序随机使用的端口 

`其范围的定义：/proc/sys/net/ipv4/ip_local_port_range`


```bash
[root@centos7 ~]# cat /proc/sys/net/ipv4/ip_local_port_range 
32768   60999
```
范例：win系统查看访问时使用的端口号
```bash
C:\Users\daizhe>netstat -nt | fandstr 端口号

活动连接

  协议  本地地址          外部地址        状态           卸载状态

  TCP    127.0.0.1:65197        127.0.0.1:65198        ESTABLISHED     InHost
  TCP    127.0.0.1:65198        127.0.0.1:65197        ESTABLISHED     InHost
  TCP    127.0.0.1:65215        127.0.0.1:65216        ESTABLISHED     InHost
  TCP    127.0.0.1:65216        127.0.0.1:65215        ESTABLISHED     InHost
  TCP    172.18.133.115:49171   52.230.84.0:443        ESTABLISHED     InHost
  TCP    172.18.133.115:49183   203.208.50.88:443      ESTABLISHED     InHost
  TCP    172.18.133.115:49190   125.39.128.245:443     ESTABLISHED     InHost
  TCP    172.18.133.115:49201   124.165.219.125:443    ESTABLISHED     InHos
```
范例：查看win运行进程列表
```bash
C:\Users\daizhe>tasklist

映像名称                       PID 会话名              会话#       内存使用
========================= ======== ================ =========== ============
System Idle Process              0 Services                   0          8 K
```
## 有限状态机FSM:Finite State Machine


CLOSED 没有任何连接状态

LISTEN 侦听状态，等待来自远方TCP端口的连接请求

SYN-SENT 在发送连接请求后，等待对方确认 

SYN-RECEIVED 在收到和发送一个连接请求后，等待对方确认 

ESTABLISHED 代表传输连接建立，双方进入数据传送状态

FIN-WAIT-1 主动关闭,主机已发送关闭连接请求，等待对方确认 

FIN-WAIT-2 主动关闭,主机已收到对方关闭传输连接确认，等待对方发送关闭 传输连接请求 

TIME-WAIT 完成双向传输连接关闭，等待所有分组消失 

CLOSE-WAIT 被动关闭,收到对方发来的关闭连接请求，并已确认 

LAST-ACK  被动关闭,等待最后一个关闭传输连接确认，并等待所有分组消失

CLOSING 双方同时尝试关闭传输连接，等待对方确认

## 有限状态机

客户端先发送一个FIN给服务端，自己进入了FIN_WAIT_1状态，这时等待接收 服务端的报文，该报文会有三种可能：

&ensp;&ensp;只有服务端的ACK

&ensp;&ensp;只有服务端的FIN

&ensp;&ensp;基于服务端的ACK，又有FIN

1、只收到服务器的ACK，客户端会进入FIN_WAIT_2状态，后续当收到服务端 的FIN时，回应发送一个ACK，会进入到TIME_WAIT状态，这个状态会持续 2MSL(TCP报文段在网络中的最大生存时间, RFC 1122标准的建议值是2min). 客户端等待2MSL，是为了当最后一个ACK丢失时，可以再发送一次。因为服务 端在等待超时后会再发送一个FIN给客户端，进而客户端知道ACK已丢失

2、只有服务端的FIN时，回应一个ACK给服务端，进入CLOSING状态，然后接 收到服务端的ACK时，进入TIME_WAIT状态 

3、同时收到服务端的ACK和FIN，直接进入TIME_WAIT状态

## 客户端的典型状态转移
客户端通过connect系统调用主动与服务器建立连接connect系统调用首先给服 务器发送一个同步报文段，使连接转移到SYN_SENT状态 此后connect系统调用可能因为如下两个原因失败返回： 

1、如果connect连接的目标端口不存在（未被任何进程监听），或者该端口仍 被处于TIME_WAIT状态的连接所占用（见后文），则服务器将给客户端发送一 个复位报文段，connect调用失败。 

2、如果目标端口存在，但connect在超时时间内未收到服务器的确认报文段， 则connect调用失败。 

connect调用失败将使连接立即返回到初始的CLOSED状态。如果客户端成功收 到服务器的同步报文段和确认，则connect调用成功返回，连接转移至 ESTABLISHED状态

当客户端执行主动关闭时，它将向服务器发送一个结束报文段，同时连接进入 FIN_WAIT_1状态。若此时客户端收到服务器专门用于确认目的的确认报文段， 则连接转移至FIN_WAIT_2状态。当客户端处于FIN_WAIT_2状态时，服务器处 于CLOSE_WAIT状态，这一对状态是可能发生半关闭的状态。此时如果服务器 也关闭连接（发送结束报文段），则客户端将给予确认并进入TIME_WAIT状态 

客户端从FIN_WAIT_1状态可能直接进入TIME_WAIT状态（不经过FIN_WAIT_2 状态），前提是处于FIN_WAIT_1状态的服务器直接收到带确认信息的结束报文 段（而不是先收到确认报文段，再收到结束报文段）

处于FIN_WAIT_2状态的客户端需要等待服务器发送结束报文段，才能转移至 TIME_WAIT状态，否则它将一直停留在这个状态。如果不是为了在半关闭状态 下继续接收数据，连接长时间地停留在FIN_WAIT_2状态并无益处。连接停留在 FIN_WAIT_2状态的情况可能发生在：客户端执行半关闭后，未等服务器关闭连 接就强行退出了。此时客户端连接由内核来接管，可称之为孤儿连接（和孤儿 进程类似） 


Linux为了防止孤儿连接长时间存留在内核中，定义了两个内核参数： 

/proc/sys/net/ipv4/tcp_max_orphans 指定内核能接管的孤儿连接数目 

/proc/sys/net/ipv4/tcp_fin_timeout 指定孤儿连接在内核中生存的时间

## sync半连接和accept全连接队列

 ss –lnt 
 
/proc/sys/net/ipv4/tcp_max_syn_backlog

未完成连接队列大小，建议调整大小为1024以上  

/proc/sys/net/core/somaxconn 

完成连接队列大小，建议调整大小为1024以上


三次握手四次挥手

范例：是否对icmp ping忽略吗?
```bash
[root@centos7 ~]# cat /proc/sys/net/ipv4/icmp_echo_ignore_all
0
0为否定，响应
1为不响应
[root@centos7 ~]# ping -c 1 -W 1 172.18.133.204
[root@centos7 ~]# ping  172.18.133.204
[root@centos6 ~]# tcpdump -i eth0 icmp
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
只接收不回应

0状态
04:08:47.878374 IP 172.18.133.162 > 172.18.133.204: ICMP echo request, id 24080, seq 12, length 64
1状态
04:10:33.142871 IP 172.18.133.204 > 172.18.133.162: ICMP echo reply, id 24191, seq 10, length 64

```



