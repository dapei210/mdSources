## 基本概念
### 密码套件格式

密钥交换算法 + 签名算法 + 对称加密算法 + 摘要算法

### 数字证书和CA机构

一个数字证书通常包含以下：
1. 公钥
2. 持有者信息
3. 证书认证机构（CA）的信息
4. 证书有效期
5. 还有一些其他额外信息

数字证书的作用，是用来认证公钥持有者的身份，以防止第三方进行冒充。说简单些，证书就是用来告诉客户端，该服务端是否是合法的，因为只有证书合法，才代表服务端身份是可信的。

签名的作用可以避免中间人在获取证书时对证书内容的篡改


CA 签发证书的过程
1. 首先 CA 会把持有者的公钥、用途、颁发者、有效时间等信息打成一个包，然后对这些信息进行 Hash 计算，得到一个 Hash 值；
2. 然后 CA 会使用自己的私钥将该 Hash 值加密，生成 Certificate Signature，也就是 CA 对证书做了签名；
3. 最后将 Certificate Signature 添加在文件证书上，形成数字证书；

客户端校验服务端的数字证书的过程如下：
1. 首先客户端会使用同样的 Hash 算法获取该证书的 Hash 值 H1；
2. 通常浏览器和操作系统中集成了 CA 的公钥信息，浏览器收到证书后可以使用 CA 的公钥解密 Certificate Signature 内容，得到一个 Hash 值 H2 ；
3. 最后比较 H1 和 H2，如果值相同，则为可信赖的证书，否则则认为证书不可信



为了确保根证书的绝对安全性，将根证书隔离地越严格越好，不然根证书如果失守了，那么整个信任链都会有问题


### 密钥交换算法

考虑到性能的问题，所以双方在加密应用信息时使用的是对称加密密钥，而对称加密密钥是不能被泄漏的，为了保证对称加密密钥的安全性，所以使用非对称加密的方式来保护对称加密密钥的协商，这个工作就是密钥交换算法负责的

#### RSA ![RSA](https://mp.weixin.qq.com/s/U9SRLE7jZTB6lUZ6c8gTKg)
在将 TLS 证书部署服务端时，证书文件中包含一对公私钥，其中公钥会在 TLS 握手阶段传递给客户端，私钥则一直留在服务端，一定要确保私钥不能被窃取

在 RSA 密钥协商算法中，客户端会生成随机密钥，并使用服务端的公钥加密后再传给服务端。根据非对称加密算法，公钥加密的消息仅能通过私钥解密，这样服务端解密后，双方就得到了相同的密钥，再用它加密应用消息

RSA算法的缺陷

使用 RSA 密钥协商算法的最大问题是不支持前向保密。因为客户端传递随机数（用于生成对称加密密钥的条件之一）给服务端时使用的是公钥加密的，服务端收到到后，会用私钥解密得到随机数。所以一旦服务端的私钥泄漏了，过去被第三方截获的所有 TLS 通讯密文都会被破解。

#### DH密钥协商算法

客户端和服务端各自会生成随机数，并以此作为私钥，然后根据公开的 DH 计算公示算出各自的公钥，通过 TLS 握手双方交换各自的公钥，这样双方都有自己的私钥和对方的公钥，然后双方根据各自持有的材料算出一个随机数，这个随机数的值双方都是一样的，这就可以作为后续对称加密时使用的密钥。

DH 密钥交换过程中，即使第三方截获了 TLS 握手阶段传递的公钥，在不知道的私钥的情况下，也是无法计算出密钥的，而且每一次对称加密密钥都是实时生成的，实现前向保密。

但因为 DH 算法的计算效率问题，后面出现了 ECDHE 密钥协商算法，我们现在大多数网站使用的正是 ECDHE 密钥协商算法


#### TLS第三次握手

客户端验证完证书后，认为可信则继续往下走。接着，客户端就会生成一个新的随机数 (pre-master)，用服务器的 RSA 公钥加密该随机数，通过「Change Cipher Key Exchange」消息传给服务端
服务端收到后，用 RSA 私钥解密，得到客户端发来的随机数 (pre-master)。

至此，客户端和服务端双方都共享了三个随机数，分别是 Client Random、Server Random、pre-master。
于是，双方根据已经得到的三个随机数，生成会话密钥（Master Secret），它是对称密钥，用于对后续的 HTTP 请求/响应的数据加解密

生成完会话密钥后，然后客户端发一个「Change Cipher Spec」，告诉服务端开始使用加密方式发送消息

然后，客户端再发一个「Encrypted Handshake Message（Finishd）」消息，把之前所有发送的数据做个摘要，再用会话密钥（master secret）加密一下，让服务器做个验证，验证加密通信是否可用和之前握手信息是否有被中途篡改过

可以发现，「Change Cipher Spec」之前传输的 TLS 握手数据都是明文，之后都是对称密钥加密的密文

#### TLS第四次握手

服务器也是同样的操作，发「Change Cipher Spec」和「Encrypted Handshake Message」消息，如果双方都验证加密和解密没问题，那么握手正式完成。

最后，就用「会话密钥」加解密 HTTP 请求和响应了


### 请求签署证书(CSR)
如果向证书颁发机构(CA)获取SSL证书，必须要生成一个CSR。CSR主要由一个密钥对的公钥和一些附加信息组成。当证书被签署时，这两部分会被插入到证书中。



### PKCS＃12

### 常用命令


#### openssl genrsa
用来生成RSA私钥，不会生成公钥，因为公钥提取自私钥（即可以通过私钥获取公钥），生成时可以指定私钥长度和密码保护

`openssl genpkey -algorithm rsa -out rsa_private.key` //生成rsa私钥

`openssl -in rsa_private.key -out public rsa_pub.key` //生成rsa公钥


#### openssl req

## 证书
### 证书类型
证书有三种类型：X.509证书、PKCS#7证书、PKCS#12证书
#### X.509证书
X.509只包含公钥，没有私钥，这种证书一般公开发布，可用于放在客服端使用，用于加密、验签
X.509证书文件的不同后缀PEM, DER, CRT,CER其实对应两种编码方案
1. Base64编码
2. ASN1.1 DER编码

PEM 文件是一个文本文件，采用了Base64 ASCII 编码

PEM文件的后缀可以是：.crt, .pem, .cer, and .key

`openssl x509 -in *.pem -noout -text`  显示可读的明文

`openssl x509 -outform der -in *.pem -out *.der`  PEM或CER文件转换为DER文件

`openssl x509 -in *.der -inform DER -outform PEM -out *.pem`  转换DER文件为PEM或CER文件

`openssl x509 -inform der -in *.der -noout -text` 将der文件显示可读明文


通过公钥和私钥进行加解密

公钥加密，私钥解密

1. `echo "just a test" >text`
2. `openssl rsautl -encrypt -in text -inkey rsa_pub.key -pubin -out text.en` //采用公钥对文件进行加密
3. `openssl rsautl -decrypt -in text.en -inkey rsa_private.key` //采用私钥解密文件


私钥加密，公钥解密，其中私钥加密使用-sign选项，公钥解密使用-verify

1. `openssl rsautl -sign -in text -inkey rsa_private.key -out text.en` //用私钥对文件进行加密(签名)

2. `openssl rsautl -verify -in text.en -inkey rsa_pub.key -pubin`  //用公钥对文件进行解密(校验)

可以将PEM文件转为PKCS#7文件，后缀为p7b
1. PKCS#7文件是含有多个证书，形成证书链。
2. -certfile MORE.pem用来指定链接的 ntermediate 或 root 证书；
3. KCS#7文件不含私钥。
4. PKCS#7文件通常用于Windows and Java server contexts

`openssl crl2pkcs7 -nocrl -certfile *.pem -certfile MORE.pem -out *.p7b`

可以将PEM文件转换为PKCS#12文件

1. PKCS#12文件是含有多个证书，形成证书链
2. PKCS#12文件含有私钥
3. 后缀为.p12或者 pfx
4. -inkey PRIVATEKEY.key 用来增加私钥
5. -certfile MORE.pem用来指定链接的 ntermediate 或 root 证书；
6. 运行命令过程中会提示设置password来保护PKCS#12文件

`openssl pkcs12 -export -out CERTIFICATE.pfx -inkey PRIVATEKEY.key -in CERTIFICATE.crt -certfile MORE.crt`

DER 文件是公钥证书或者私钥通过DER编码以后生成的二进制文件，其后缀为der或cer

`openssl x509 -inform der -in *.der -text -noout`  显示可读的明文

可以将DER文件转换为PEM

`openssl x509 -inform der -in *.der -out *.pem`

其实证书或者私钥，都是先根据规范给ASN.1描述，然后进行DER编码，生成二进制文件（DER），然后对二进制文件再Base64编码，即可以生成ASCII码文件（PEM）。所以DER到PEM转换，其实就是Base64编码，反之，则是Base64解码

### 制作证书

1. 生成RSA



#### AD域服务器制作证书

[AD服务器证书制作](https://zhuanlan.zhihu.com/p/558286846?utm_id=0)

### CRL


验证CRL

```openssl crl -in crl.crl  -CAfile demoCA/cacert.pem -noout```

PEM格式的CRL文件转换为DER格式

```openssl crl -in crl.crl -outform DER -out crl.der```

查看CRL信息

```openssl crl -in crl.crl -text -issuer -hash -lastupdate -nextupdate```


crl格式文件转为pem格式

```openssl crl -in your_current.crl -inform DER -out crl.pem```

```openssl crl -in crl.pem  -noout -text```



### OCSP
认证请求证书

```openssl ocsp -issuer cacert.pem -cert clicert.pem -text -url http://ocsp.digicert.com```

如果



### 使用OCSP, LDAP和HTTP进行证书检查

当证书被吊销时，它会被添加到由CA或CA授权的证书吊销列表(CRL)中

#### LDAP
CA支持LDAP CRL发布

#### HTTP
使用web服务器发布CRL消息

#### OCSP 
LDAP和HTTP机制都是通过共享CRL消息工作，存在两个关键问题：
1. CRL可能很大
2. 实时快速响应

OCSP通过C/S协议解决此类问题
OCSP



### 制作证书

1. 生成RSA



#### AD域服务器制作证书

[AD服务器证书制作](https://zhuanlan.zhihu.com/p/558286846?utm_id=0)

### CRL

验证CRL

```openssl crl -in crl.crl  -CAfile demoCA/cacert.pem -noout```

PEM格式的CRL文件转换为DER格式

```openssl crl -in crl.crl -outform DER -out crl.der```

查看CRL信息

```openssl crl -in crl.crl -text -issuer -hash -lastupdate -nextupdate```



吊销证书

openssl ca -revoke /path/to/certificate.pem -keyfile /path/to/private/key.pem -cert /path/to/ca-certificate.pem

/path/to/certificate.pem: Path to the certificate you want to revoke.
/path/to/private/key.pem: Path to the private key associated with the certificate.
/path/to/ca-certificate.pem: Path to the CA certificate used to sign the certificate.

.crl格式转为pem
openssl crl -in crl.crl -inform DER -out crl.pem

校验证书是否被吊销
[root@localhost ~]# openssl verify -crl_check -CRLfile new.pem -CAfile ca.pem server.pem
server.pem: OK


[root@localhost ~]# openssl verify -crl_check -CRLfile new.pem -CAfile ca.pem client.pem
client.pem: C = FR, ST = Radius, O = Example Inc., CN = user@example.org, emailAddress = user@example.org
error 23 at 0 depth lookup:certificate revoked




## openssl源码




## ASN( Abstract Syntax Notation) 抽象语法标记

ASN.1的编码格式有很多种: BER、CER、DER、XER，可以编码成XML格式，不仅仅是常用的二进制流。BER、CER、DER，是ASN.1的三种最常用的编码格式

### CER、DER、CRT、PEM的关系
1. 所有X.509都是DER编码，DER是指ASN.1的编码规则，.der证书文件一般是二进制文件
2. CER可用于PKCS#7证书(p7b)的编码，但一般是指证书的文件后缀，.cer证书可以是纯BASE64文件或二进制文件
3. PEM通常也是指文件的后缀，为内容使用BASE64编码且带头带尾的特定格式，二进制的文件不应该命名为pem
4. CRT是微软的证书后缀名，和.CER是一回事

#### PEM
PEM是最常用的X.509证书文件后缀， 其是一个文本文件， 采用了Base64 ASCII编码， 一个PEM文件可以包含公钥证书/私钥， 或者能形成证书链的多个证书

PEM文件的后缀可以是： .crt, .pem , .cer, .key



测试服务器是否支持某种密码套件
openssl s_client -cipher "ECDHE-SM2-WITH-SMS4-SM3" -connect sm2test.ovssl.cn:443 -tls1_2 -servername sm2test.ovssl.cn



