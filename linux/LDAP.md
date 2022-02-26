### LDAP
该协议用来访问目录数据库的，在计算机域中，目录服务数据库存储整个域内所有的计算机、用户等信息。目录服务=服务数据库+访问协议。
目录服务数据库本质上是一个数据库， 用来存储数据的，主要有以下两个特点：
1. 树状组织结构，类似文件目录
2. 为查询、浏览和搜索而优化的数据库，即LDAP的读性能特别强，但是写性能差，而且还不支持事务处理、回滚等复杂功能。

#### LDAP中的CN,OU,DC
CN(Common Name),OU(Organizational Unit),DC(Domain Component)
三者都是LDAP连接服务器端字符串的区分名DN(Distinguished Name),LDAP连接服务器的连接字符串格式为：ldap://servername/DN

