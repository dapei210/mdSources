https://blog.51cto.com/chier11/1108385?source=drt

### 认证环境
linux的Samba服务使用kerberos实现windows AD域账户的认证

在Windows AD域环境中，使用kerberos做身份认证的服务器叫KDC（Key Distribution Center）


Windows Server作为域控制器，客户端提交的认证请求和授权过程是通过kerberos5协议来完成的，所以要让linux的Samba服务器支持域认证，需要经Kerberos配置好，配置文件为/etc/krb5.conf，配置如下：
```
# Configuration snippets may be placed in this directory as well
includedir /etc/krb5.conf.d/

[logging]
 default = FILE:/var/log/krb5libs.log
 kdc = FILE:/var/log/krb5kdc.log
 admin_server = FILE:/var/log/kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 1h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 pkinit_anchors = FILE:/etc/pki/tls/certs/ca-bundle.crt
 default_realm = QX.COM   ##指定域名
 default_ccache_name = KEYRING:persistent:%{uid}

[realms]
QX.COM = {
    kdc = 192.168.66.136
    admin_server = QX.COM
}

[domain_realm]
QX.COM = QX.COM
.QX.COM = QX.COM
```

配置完成后，通过kinit（需要安装krb5-workstation软件包）来验证kerberos协议是否可以正常运行

```
[root@AD-USER bin]# kinit ypp@QX.COM
Password for ypp@QX.COM: 
kinit: Password incorrect while getting initial credentials
```


#### 配置Winbind
Winbind是Samba的一个组件，实现了Windows的RPC调用、可插式验证模块和名字服务切换，通过这些功能可以使NT域用户能在linux主机上以linux用户身份进行操作。

在linux主机上可以查看NT域用户及组信息，就好象这些信息是UNIX本地的一样。当linux主机向操作系统查询任何用户或组名时，该查询都会被发往指定的域控制器，即KDC

从windind守护进程查询信息：
```
[root@AD-USER ~]# /usr/local/samba/bin/wbinfo -t
checking the trust secret for domain QX via RPC calls succeeded

```
说明Samba已经成功地加入到Windows server的AD域中了
如果要从域中删除Samba，执行命令：

`/usr/local/samba/bin/net ads leave -U administrator`


配置samba的配置文件/etc/samba/smb.conf
```
 See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.

[global]
        workgroup = QX   ## 指定域名或工作者名，即“.com”之前的名字
        security = ads 

        #passdb backend = tdbsam
        realm = QX.COM
        #printing = cups
        printcap name = cups
        load printers = yes
        cups options = raw

[homes]
        comment = Home Directories
        valid users = %S, %D%w%S
        browseable = No
        read only = No
        inherit acls = Yes

[printers]
        comment = All Printers
        path = /var/tmp
        printable = Yes
        create mask = 0600
        browseable = No

[print$]
        comment = Printer Drivers
        path = /var/lib/samba/drivers
        write list = @printadmin root
        force group = @printadmin
        create mask = 0664
        directory mask = 0775
```

security配置为ads模式后，将验证工作交给windows域控制器负责，使用这个模式，需要在samba服务器上安装Kerberos，在这个模式中Samba是作为一个域成员，而不是AD域控制器。