### 基本概念

#### 请求签署证书(CSR)
如果向证书颁发机构(CA)获取SSL证书，必须要生成一个CSR。CSR主要由一个密钥对的公钥和一些附加信息组成。当证书被签署时，这两部分会被插入到证书中。



### PKCS＃12
是




### 常用命令


#### openssl genrsa
用来生成RSA私钥，不会生成公钥，因为公钥提取自私钥，生成时可以指定私钥长度和密码保护

#### openssl req

## 证书
### 证书类型
证书有三种类型：X.509证书、PKCS#7证书、PKCS#12证书
#### X.509证书
X.509只包含公钥，没有私钥，这种证书一般公开发布，可用于放在客服端使用，用于加密、验签
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


