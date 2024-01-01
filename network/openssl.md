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



