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


