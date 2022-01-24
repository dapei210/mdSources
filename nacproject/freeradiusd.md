### freeradius2*
#### 编译

`./configure --prefix=/usr/local/ --with-udpfromto --with-openssl-includes=/usr/local/ssl/include --with-openssl-libraries=/usr/local/ssl/lib/`
`make&&make install`

### freeradius3*
#### 编译
openssl 1.1.1c版本

`./config  shared zlib-dynamic  --prefix=/usr/local/openssl/`
`make&&make install`

`./configure --prefix=/usr/local/rad3/ --with-udpfromto --with-openssl-include-dir=/usr/local/openssl/include/  --with-openssl-lib-dir=/usr/local/openssl/lib/`

`make&&make install`

