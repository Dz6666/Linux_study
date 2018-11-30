---
title: 私有CA
tags: linux
categories: linux
---
## OpenSSL 

PKI：Public Key Infrastructure  

&ensp;&ensp;CA  

&ensp;&ensp;RA  

&ensp;&ensp;CRL  

&ensp;&ensp;证书存取库 

建立私有CA:  

&ensp;&ensp;OpenCA  

&ensp;&ensp;openssl 

证书申请及签署步骤：  

&ensp;&ensp;1、生成申请请求  

&ensp;&ensp;2、RA核验  

&ensp;&ensp;3、CA签署  

&ensp;&ensp;4、获取证书 

## 创建CA和申请证书 

`创建私有CA：`  

&ensp;&ensp;openssl的配置文件：/etc/pki/tls/openssl.cnf  

&ensp;&ensp;三种策略：匹配、支持和可选  

&ensp;&ensp;匹配指要求申请填写的信息跟CA设置信息必须一致，支持指必须填写这项申 请信息，可选指可有可无 

`1、创建所需要的文件`  

&ensp;&ensp;touch /etc/pki/CA/index.txt 生成证书索引数据库文件  

&ensp;&ensp;echo 01 > /etc/pki/CA/serial 指定第一个颁发证书的序列号 

`2、 CA自签证书  `

&ensp;&ensp;生成私钥  

&ensp;&ensp;cd /etc/pki/CA/  

&ensp;&ensp;(umask 066; openssl genrsa -out  /etc/pki/CA/private/cakey.pem 2048) 

生成自签名证书   

openssl req -new -x509 –key

/etc/pki/CA/private/cakey.pem  -days  7300  -out    

/etc/pki/CA/cacert.pem  

&ensp;&ensp;-new: 生成新证书签署请求  

&ensp;&ensp;-x509: 专用于CA生成自签证书  

&ensp;&ensp;-key: 生成请求时用到的私钥文件  

&ensp;&ensp;-days n：证书的有效期限  

&ensp;&ensp;-out /PATH/TO/SOMECERTFILE: 证书的保存路径 

`3、颁发证书 `

在需要使用证书的主机生成证书请求  

&ensp;&ensp;给web服务器生成私钥   

&ensp;&ensp;(umask 066; openssl genrsa -out      

&ensp;&ensp;/etc/pki/tls/private/test.key 2048)  

生成证书申请文件 

&ensp;&ensp;openssl req -new -key /etc/pki/tls/private/test.key    -days 365 -out etc/pki/tls/test.csr 

将证书请求文件传输给CA 

CA签署证书，并将证书颁发给请求者  

&ensp;&ensp;openssl ca -in /tmp/test.csr –out    /etc/pki/CA/certs/test.crt -days 365  

注意：默认国家，省，公司名称三项必须和CA一致 

查看证书中的信息：  

&ensp;&ensp;openssl x509 -in /PATH/FROM/CERT_FILE -noout   -text|issuer|subject|serial|dates  

openssl  ca -status SERIAL  查看指定编号的证书状态 

`4、吊销证书 `

在客户端获取要吊销的证书的serial  

&ensp;&ensp;openssl x509 -in /PATH/FROM/CERT_FILE   -noout   -serial  -subject 

在CA上，根据客户提交的serial与subject信息，对比检验是否与index.txt文件中的信息一致， 吊销证书：  

&ensp;&ensp;openssl ca -revoke /etc/pki/CA/newcerts/SERIAL.pem 

指定第一个吊销证书的编号,注意：第一次更新证书吊销列表前，才需要执行  

&ensp;&ensp;echo 01 > /etc/pki/CA/crlnumber 

更新证书吊销列表  

&ensp;&ensp;openssl ca -gencrl -out /etc/pki/CA/crl.pem 

查看crl文件：  

&ensp;&ensp;openssl crl -in /etc/pki/CA/crl.pem -noout -text

范例：自创CA ，现有两台虚拟机centos6、centos7，centos7搭建CA服务器，centos作为申请证书的主机，想在centos6上搭建一台http服务器域名为https：//www.9527dz.com,最终申请的CA中要标识出网站的名称
```bash
建立私有CA，参考配置文件：/etcpki/tls/openssl.cnf

解析参考配置文件
[root@centos7 ~]# cat /etc/pki/tls/openssl.cnf 
[ ca ]
default_ca	= CA_default		# The default ca section（默认CA、可以修改为下面的anything）

####################################################################
[ CA_default ]                     （默认CA）

dir		= /etc/pki/CA	     	# Where everything is kept(CA的所有的文件都是在此文件夹下)
certs		= $dir/certs		# Where the issued certs are kept（旧的发布的证书放置在/etc/pki/CA/crl文件下）
crl_dir		= $dir/crl		    # Where the issued crl are kept（被吊销证书工作列表在/etc/pki/CA/crl下）
database	= $dir/index.txt	# database index file.（颁发证书的数据库文件存放在/etc/pki/CA/index.html需手动创建）
#unique_subject	= no			# Set to 'no' to allow creation of
					# several ctificates with same subject.
new_certs_dir	= $dir/newcerts		# default place for new certs.(新颁发的刚刚颁发的证书放置在/etc/pki/CA/newcerts)

certificate	= $dir/cacert.pem 	# The CA certificate(CA的证书放置在/etc/pki/CA/cacert.pem文件)
serial		= $dir/serial 		# The current serial number(下一个颁发证书的序列号放置在/etc/pki/CA/serial文件16进制数)（默认没有，需手动创建，以后自动递增）
crlnumber	= $dir/crlnumber	# the current crl number（吊销证书的吊销编号放置在/etc/pki/CA/serial文件中）
					# must be commented out to leave a V1 CRL
crl		= $dir/crl.pem 		# The current CRL
private_key	= $dir/private/cakey.pem # The private key（CA的私钥）
RANDFILE	= $dir/private/.rand	# private random number file
....
default_days	= 365			# how long to certify for(证书默认有效期)
default_crl_days= 30			# how long before next CRL（下一个吊销列表多长时间公示）
default_md	= sha256		# use SHA-256 by default（机密算法）
preserve	= no			# keep passed DN ordering

policy		= policy_match  （填写CA的信息）

# For the CA policy                match就是要作为用户要与CA服务器的签名要相同
[ policy_match ]                  系统默认
countryName		= match           哪个国家的CA
stateOrProvinceName	= match       哪个省的CA
organizationName	= match组织名
organizationalUnitName	= optional部门CA
commonName		= supplied        哪个网站CA
emailAddress		= optional    邮箱CA
策列可选
[ policy_anything ]
countryName		= optional
stateOrProvinceName	= optional
localityName		= optional
organizationName	= optional
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

第一步：在centos7建立有效私钥

[root@centos7 CA]# pwd
/etc/pki/CA
[root@centos7 CA]# tree
.
├── certs
├── crl
├── newcerts
└── private
[root@centos7 CA]# (umask 066;openssl genrsa -out /etc/pki/CA/private/cakey.pem 2048)
Generating RSA private key, 2048 bit long modulus
..........................+++
..........................................................................................+++
e is 65537 (0x10001)
[root@centos7 CA]# tree
.
├── certs
├── crl
├── newcerts
└── private
    └── cakey.pem

第二步：在centos7生成自签名的证书
[root@centos7 CA]# (umask 066;openssl genrsa -out /etc/pki/CA/private/cakey.pem 2048)
Generating RSA private key, 2048 bit long modulus
..........................+++
..........................................................................................+++
e is 65537 (0x10001)
[root@centos7 CA]# tree
.
├── certs
├── crl
├── newcerts
└── private
    └── cakey.pem

4 directories, 1 file
[root@centos7 CA]# ll
total 0
drwxr-xr-x. 2 root root  6 Apr 11  2018 certs
drwxr-xr-x. 2 root root  6 Apr 11  2018 crl
drwxr-xr-x. 2 root root  6 Apr 11  2018 newcerts
drwx------. 2 root root 23 Nov 13 20:41 private
[root@centos7 CA]# ll private/
total 4
-rw-------. 1 root root 1679 Nov 13 20:41 cakey.pem
[root@centos7 CA]# openssl req -new -x509 -key /etc/pki/CA/private/cakey.pem -out /etc/pki/CA/cacert.pem -days 3650
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:beijing
Locality Name (eg, city) [Default City]:beijing
Organization Name (eg, company) [Default Company Ltd]:zanshiwu
Organizational Unit Name (eg, section) []:ops
Common Name (eg, your name or your server's hostname) []:ca.9527dz.com
Email Address []:1284808408@qq.com  
[root@centos7 CA]# tree
.
├── cacert.pem
├── certs
├── crl
├── newcerts
└── private
    └── cakey.pem
此时可以将生成的自签名证书cacert.pem传入windows改成.crt后缀的文件
windows查看到
由于CA 根证书不在“受信任的根证书颁发机构”存储区中，所以它不受信任。

第三步：在centos6客户端需要搭建http服务，需要申请证书
申请证书第一步：生成私钥（如果搭建多个服务可以多次申请证书）
[root@centos6 ssl]# pwd
/data/ssl
[root@centos6 ssl]# (umask 066;openssl genrsa -out http.key 1024)
Generating RSA private key, 1024 bit long modulus
.........++++++
..........++++++
e is 65537 (0x10001)
[root@centos6 ssl]# ll
total 4
-rw-------. 1 root root 887 Nov 13 21:00 http.key
申请证书第二步：利用私钥生成证书申请
[root@centos6 ssl]# openssl req -new -key http.key -out http.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:beijing
Locality Name (eg, city) [Default City]:beijing
Organization Name (eg, company) [Default Company Ltd]:zanshiwu
Organizational Unit Name (eg, section) []:ops
Common Name (eg, your name or your server's hostname) []:*.9527dz.com
Email Address []:1284808408@qq.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
[root@centos6 ssl]# ls
http.csr  http.key

第四步：将centos6客户端的申请证书发送给centos7CA服务器
[root@centos6 ssl]# scp /data/ssl/http.csr 192.168.131.178:/etc/pki/CA
root@192.168.131.178's password: 
http.csr                                    100%  700     0.7KB/s   00:00  
[root@centos7 CA]# tree
.
├── cacert.pem
├── certs
├── crl
├── http.csr
├── newcerts
└── private
    └── cakey.pem

第五步：创建CA数据路文件，创建证书编号起始
[root@centos7 CA]# touch /etc/pki/CA/index.txt
[root@centos7 CA]# echo 0F > /etc/pki/CA/serial
[root@centos7 ~]# tree /etc/pki/CA
/etc/pki/CA
├── cacert.pem
├── certs
│   └── http.crt
├── crl
├── http.csr
├── index.txt
├── newcerts
├── private
│   └── cakey.pem
└── serial
[root@centos7 ~]# ll /etc/pki/CA/certs/http.crt 
-rw-r--r--. 1 root root 0 Nov 13 21:18 /etc/pki/CA/certs/http.crt

第六步：审查centos6客户端提交的资料进行颁发
[root@centos7 CA]# openssl ca -in http.csr -out /etc/pki/CA/certs/http.crt -days 1000
Using configuration from /etc/pki/tls/openssl.cnf
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number: 15 (0xf)
        Validity
            Not Before: Nov 13 13:18:21 2018 GMT
            Not After : Aug  9 13:18:21 2021 GMT
        Subject:
            countryName               = CN
            stateOrProvinceName       = beijing
            organizationName          = zanshiwu
            organizationalUnitName    = ops
            commonName                = *.9527dz.com
            emailAddress              = 1284808408@qq.com
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Comment: 
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier: 
                BF:5B:06:AA:38:89:73:DD:6D:B5:89:9E:0A:C5:3D:61:9D:6A:D2:4C
            X509v3 Authority Key Identifier: 
                keyid:80:E2:6A:01:2B:57:66:EB:08:4D:59:05:19:25:31:9B:FB:59:6B:C5

Certificate is to be certified until Aug  9 13:18:21 2021 GMT (1000 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
[root@centos7 ~]# ll /etc/pki/CA/certs/http.crt 
-rw-r--r--. 1 root root 3869 Nov 13 21:21 /etc/pki/CA/certs/http.crt
[root@centos7 ~]# tree /etc/pki/CA
/etc/pki/CA
├── cacert.pem
├── certs
│   └── http.crt
├── crl
├── http.csr
├── index.txt
├── index.txt.attr
├── index.txt.old
├── newcerts
│   └── 0F.pem
├── private
│   └── cakey.pem
├── serial
└── serial.old
证书内容
[root@centos7 CA]# diff certs/http.crt newcerts/0F.pem 
可以传到windows查看
[root@centos7 CA]# sz certs/http.crt 
导入windows上级证书的CA
[root@centos7 CA]# sz cacert.pem 

第七步：审核通过将证书发送给centos6客户端使用
[root@centos7 CA]# scp /etc/pki/CA/certs/http.crt 192.168.131.129:/data/ssl
The authenticity of host '192.168.131.129 (192.168.131.129)' can't be established.
RSA key fingerprint is SHA256:UXGG1lxW5SEZHzcXUY+uxvN/TfHiWAsxFwxotFUhFdM.
RSA key fingerprint is MD5:81:4f:63:e3:15:95:f8:86:2e:91:0b:55:4f:26:db:9e.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.131.129' (RSA) to the list of known hosts.
root@192.168.131.129's password: 
http.crt                                    100% 3869     7.2MB/s   00:00    
[root@centos6 ssl]# ls
http.crt  http.csr  http.key
[root@centos6 ssl]# openssl x509 -in http.crt -noout -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 15 (0xf)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=CN, ST=beijing, L=beijing, O=zanshiwu, OU=ops, CN=ca.9527dz.com/emailAddress=1284808408@qq.com
        Validity
            Not Before: Nov 13 13:18:21 2018 GMT
            Not After : Aug  9 13:18:21 2021 GMT
        Subject: C=CN, ST=beijing, O=zanshiwu, OU=ops, CN=*.9527dz.com/emailAddress=1284808408@qq.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (1024 bit)
...........................................


查看服务器端信息
[root@centos7 CA]# tree
.
├── cacert.pem
├── certs
│   └── http.crt
├── crl
├── http.csr
├── index.txt
├── index.txt.attr      
├── index.txt.old    
├── newcerts
│   └── 0F.pem
├── private
│   └── cakey.pem
├── serial
└── serial.old

4 directories, 10 files
[root@centos7 CA]# cat index.txt
V	210809131821Z		0F	unknown	/C=CN/ST=beijing/O=zanshiwu/OU=ops/CN=*.9527dz.com/emailAddress=1284808408@qq.com
V代表有效，吊销表示为R

下一个有效证书的编号
[root@centos7 CA]# cat serial
10

现在颁发证书的备份
[root@centos7 CA]# cat serial.old 
0F

主题
[root@centos7 CA]# cat index.txt.attr 
unique_subject = yes （信息主题唯一，不可冲突，可以变成no ，就支持相同的主题了）
（ Subject: C=CN, ST=beijing, O=zanshiwu, OU=ops, CN=*.9527dz.com/emailAddress=1284808408@qq.com
        Subject Public Key Info:）
```
范例：用CA的证书验证客户端的完整性
```bash
[root@centos7 CA]# openssl verify -CAfile cacert.pem certs/http.crt 
certs/http.crt: OK
```