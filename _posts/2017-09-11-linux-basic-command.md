---
layout: post
title:  OpenSSL及创建私有CA
date:   2017-09-11 12:00:00
categories: Linux 
tags:  搭建私有CA  openssl
---

## OpenSSL及创建私有CA

### OpenSSL 

> OpenSSL，是一个开放源代码的软件库包，应用程序可以使用这个包来进行安全通信，避免窃听，同时确认另一端连接者的身份。这个包广泛被应用在互联网的网页服务器上。
> 
> 接下来对openssl基本命令进行介绍


> openssl 非常强大其子命令很多，这里只是对完成某些操作进行部分解释

> #### openssl 的基本格式为

```shell

openssl enc -ciphername [-in filename] [-out filename] [-e] [-d] ...

-ciphername :指定加密算法，例如：-base64

-in filename :指定要加密的文件，filename为该文件的文件名

-out filename :指定将加密后的数据保存到filename指定的文件中

-e :指定为加密过程，此为默认过程

-d :指定为解密过程

```

>  
> ### 加密

#### openssl对称加密

> enc命令

> ##### 加密

```shell

	openssl enc -e des3  -a salt  -in testfile  -out  testfile.cipher

```

> ##### 解密

```shell

	openssl enc -d des3 -a salt  –in testfile.cipher  -out testfile
	
```

#### 单项加密

> dgst命令

```shell 

	openssl dgst  -md5  testfile 
	
```

#### 生成用户密码

> 生成用户密码：
> passwd命令:
```shell

	openssl   passwd  -1  -salt  SALT(最多8位)
	openssl   passwd  -1  –salt  centos

```
> 生成随机数：

```shell

	openssl   rand   -base64|-hex NUM

```
> NUM: 表示字节数；-hex时，每个字符为十六进制，相当于4位二进制，出现的字符数为NUM*2


## 创建私有CA

### 什么是CA

> 一种数字证书，如果用户想得到一份属于自己的证书，他应先向 CA 提出申请。在 CA 判明申请者的身份后，便为他分配一个公钥，并且 CA 将该公钥与申请者的身份信息绑在一起，并为之签字后，便形成证书发给申请者。
如果一个用户想鉴别另一个证书的真伪，他就用 CA 的公钥对那个证书上的签字进行验证，一旦验证通过，该证书就被认为是有效的。证书实际是由证书签证机关（CA）签发的对用户的公钥的认证。
证书的内容包括：电子签证机关的信息、公钥用户信息、公钥、权威机构的签字和有效期等等。目前，证书的格式和验证方法普遍遵循X.509 国际标准。


> 在制作私有CA之前我们先来了解一下，与CA相关的配置文件，在/etc/pki/tls/下的openssl.cnf文件
> 
> 我们来看一下,部分内容如下图：

![openssl.cnf文件](https://thumbnail0.baidupcs.com/thumbnail/7c275fbdda536366e8517ecdbe1b121e?fid=2905626949-250528-643450039677197&time=1505109600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-eYXXu5PGkj5NDjMtjFUoqTzZKJA%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5882163115569843257&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

```shell

#CA的默认设置
[ CA_default ]

dir             = /etc/pki/CA     	       		# 默认工作目录
certs           = $dir/certs          	    	#服务器证书存放位置
crl_dir         = $dir/crl                  	#证书吊销列表
database        = $dir/index.txt        		#证书数据列表文件
#unique_subject = no                   			# Set to 'no' to allow creation of
                                        		# several ctificates with same subject.
new_certs_dir   = $dir/newcerts      			#  新证书存放位置

certificate     = $dir/cacert.pem      			#CA自己的证书存放处
serial          = $dir/serial           		#序列号（十六进制数）为下一个证书的生成编号
crlnumber       = $dir/crlnumber      			#吊销列表的工作号
												# must be commented out to leave a V1 CRL
crl                     = $dir/crl.pem          #证书吊销列表文件
private_key       = $dir/private/cakey.pem		#CA私钥
RANDFILE        = $dir/private/.rand            # 随机数文件

x509_extensions = usr_cert             		 	# 


default_days    = 365                   		# how long to certify for 证书时间
default_crl_days= 30                    		# how long before next CRL 吊销列表的时间
default_md      = default               		# use public key default MD 单相加密算法
preserve        = no                    		# keep passed DN ordering


```

> 我们来看一下/etc/pki/CA下的文件

```shell

[root@Centos6 /etc/pki/CA]#ls
certs  crl  newcerts  private

```
> 对比配置文件，可以看到缺少index.txt  serial文件，缺少的文件是需要我们自己创建出来的

> CA policy (匹配的策略)是否需要相同
![匹配策略](https://thumbnail0.baidupcs.com/thumbnail/ac0e33e4b5e3a85f9b2c364492f6145f?fid=2905626949-250528-551472515013528&time=1505109600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-UH5aktopuuropB8pnLxeb91cpoo%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5882204161151315375&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

> 在配置的设定中，国家、省份及公司名称是必须要一致的，否则在创建CA时会提示不匹配错误，当然也可以指定为都不匹配

###  创建CA

#### 1、创建所需要的文件

> 在上面提到过的缺少的文件需要创建

```shell 

	touch /etc/pki/CA/index.txt  生成证书索引数据库文件
	echo 01 > /etc/pki/CA/serial 指定第一个颁发证书的序列号,以后不需重新指定

```

#### 2、CA自签证书

> 那么谁会给CA本身颁发证书呢，CA本身就是最高的认证，这就需要自己给自己认证了

> #### (1)、生成私钥

> 在配置文件中，已经定义了私钥的存放位置，以及名称，这里要注意与配置文件openssl.cnf当中的指定路径一致

```shell
	
cd  /etc/pki/CA 
	
执行命令，并设定生成的文件权限为600

[root@Centos6 /etc/pki/CA]#(umask 077; openssl  genrsa  -out  /etc/pki/CA/private/cakey.pem  -des  1024)

Generating RSA private key, 1024 bit long modulus
.++++++
..............................++++++
e is 65537 (0x10001)
Enter pass phrase for /etc/pki/CA/private/cakey.pem:
Verifying - Enter pass phrase for /etc/pki/CA/private/cakey.pem:

```

> #### (2)、生成自签名证书

> 生成自签名证书，需要自己给自己颁发，openssl有专门的命令选项x509可以使用

```shell

#执行命令后，填写信息表即可

[root@Centos6 /etc/pki/CA]#openssl  req -new -x509 -key  /etc/pki/CA/private/cakey.pem -days 7300 -out  /etc/pki/CA/cacert.pem

Enter pass phrase for /etc/pki/CA/private/cakey.pem:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:	CN
State or Province Name (full name) []:	henan   
Locality Name (eg, city) [Default City]:	zhengzhou
Organization Name (eg, company) [Default Company Ltd]:	magedu            
Organizational Unit Name (eg, section) []:	tech
Common Name (eg, your name or your server's hostname) []:	magedu.com
Email Address []:

``` 

**注意：这里所填的国家、省份及公司名称要记得，接下来在提交申请证书时会用到** 


> 相关的命令参数

```shell

-req 	: requst   申请
-new	: 生成新证书签署请求
-x509	: 专用于CA生成自签证书
-key	: 生成请求时用到的私钥文件
-days n	:证书的有效期限
-out /PATH/TO/SOMECERTFILE: 证书的保存路径

```

> #### (3)、颁发证书

> - A、在需要使用证书的主机生成证书请求

```shell

#给web服务器生成私钥

[root@centos7 ~]#(umask 077; openssl genrsa -out   /etc/pki/tls/private/test.key 1024)

Generating RSA private key, 1024 bit long modulus
..........++++++
......++++++
e is 65537 (0x10001)

#生成证书申请文件

[root@centos7 /etc/pki/tls]#openssl req -new -key  /etc/pki/tls/private/test.key  -days 365  -out  /etc/pki/tls/test.csr

You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN
State or Province Name (full name) []:henan
Locality Name (eg, city) [Default City]:zhengzhou
Organization Name (eg, company) [Default Company Ltd]:magedu
Organizational Unit Name (eg, section) []:tech
Common Name (eg, your name or your server's hostname) []:magedu.com   
Email Address []: 

(命令里的时间制定其实没什么意义，最终是要颁发者指定的)

```

> 以上申请证书的文件已经完成，可以提交了

> - B、向CA提交证书申请

```shell

[root@centos7 /etc/pki/tls]#scp test.csr 172.18.18.18:/etc/pki/CA

root@172.18.18.18's password: 
test.csr                                             100%  647     0.6KB/s   00:00  

```

> - C、签署证书

```shell

[root@Centos6 /etc/pki/CA]#openssl ca  -in /etc/pki/CA/test.csr -out  /etc/pki/CA/certs/test.crt  -days 3650

Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for /etc/pki/CA/private/cakey.pem:
Check that the request matches the signature
Signature ok

#申请证书的详细信息

Certificate Details:
        Serial Number: 1 (0x1)
        Validity
            Not Before: Sep 10 09:41:12 2017 GMT
            Not After : Sep  8 09:41:12 2027 GMT
        Subject:
            countryName               = CN
            stateOrProvinceName       = henan
            organizationName          = magedu
            organizationalUnitName    = tech
            commonName                = magedu.com
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Comment: 
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier: 
                AC:FB:56:45:E0:A8:B5:EA:82:15:14:6E:9F:FB:18:F4:FB:78:67:FA
            X509v3 Authority Key Identifier: 
                keyid:C1:37:D1:E8:53:EB:C9:02:0B:E9:71:1A:60:42:0F:33:C9:11:ED:7A

Certificate is to be certified until Sep  8 09:41:12 2027 GMT (3650 days)

#是否签署文件
Sign the certificate? [y/n]:y

1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated

```

> 查看证书的信息及状态，可以执行以下命令

```shell

openssl x509 -in app.crt -noout -text|issuer|subject|dates

text代表全部信息
issuer代表颁发者
subject代表主题
dates代表有效期 

openssl ca -status SERIAL

```

> 到此证书的签署就已经完成了，之后就可以颁发给申请者了

> 我们可以看一下完成一系列流程的CA目录

```shell

#CA的目录树
[root@Centos6 /etc/pki/CA]#tree

.
├── cacert.pem
├── certs
│   └── test.crt
├── crl
├── index.txt
├── index.txt.attr
├── index.txt.old
├── newcerts
│   └── 01.pem
├── private
│   └── cakey.pem
├── serial
└── serial.old

```


#### 吊销证书

> 当证书过期或者不受信任时需要对签署的证书进行吊销，可以执行以下命令

> - (1)、吊销证书

```shell

[root@Centos6 /etc/pki/tls]#openssl ca -revoke /etc/pki/CA/newcerts/01.pem

Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for /etc/pki/CA/private/cakey.pem:
Revoking Certificate 01.
Data Base Updated

```
> - (2)、指定吊销证书的编号

```shell

echo 01 > /etc/pki/CA/crlnumber

```

> **注意：第一次更新吊销列表前，才需要执行，以后不需要了**

> - (3)、更新证书吊销列表

```shell

[root@Centos6 /etc/pki/CA]#openssl ca -gencrl -out /etc/pki/CA/crl/crl.pem

Using configuration from /etc/pki/tls/openssl.cnf
Enter pass phrase for /etc/pki/CA/private/cakey.pem:

#查看更新后的crl文件

[root@Centos6 /etc/pki/CA]#openssl   crl  -in   /etc/pki/CA/crl/crl.pem   -noout    -text
Certificate Revocation List (CRL):
        Version 2 (0x1)
    Signature Algorithm: sha1WithRSAEncryption
        Issuer: /C=CN/ST=henan/L=zhengzhou/O=magedu/OU=tech/CN=magedu.com
        Last Update: Sep 10 12:42:49 2017 GMT
        Next Update: Oct 10 12:42:49 2017 GMT
        CRL extensions:
            X509v3 CRL Number: 
                1
Revoked Certificates:
    Serial Number: 01
        Revocation Date: Sep 10 12:38:03 2017 GMT
    Signature Algorithm: sha1WithRSAEncryption
         6b:06:d1:c2:9c:c8:9a:81:21:09:10:f4:aa:28:6c:bf:a3:64:
         以下省略
		 ...

```
> 通过crl文件可以看出，确实将申请的证书吊销了


> 到此创建私有的证书及颁发完毕，不足及错误之处还望指正






























































