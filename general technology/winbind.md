### 概述
winbindd是一个守护进程，通过PAM和ntlm_auth向任意应用程序以及Samba本身提供大量服务。

Winbind使用Microsoft RPC调用，可插入身份验证模块（PAM）和名称服务开关（NSS）的UNIX实现，以允许Windows NT域用户在UNIX计算机上出现并作为UNIX用户运行

winbindd提供的服务称为“winbind”，可用于从Windows NT服务器解析用户和组信息。

### 功能
1. 验证用户凭据（通过PAM）。这样就可以使用Windows NT4（包括Samba域）或Active Directory域中的用户和组帐户登录UNIX / Linux系统。
2. 身份解析（通过NSS）。不使用winbind时，这是默认设置。
3. Winbind维护一个名为winbind_idmap.tdb的数据库，在其中存储UNIX UID，GID和NT SID之间的映射。此映射仅用于没有本地UID / GID的用户和组。它存储从已映射到NT SID的idmap uid/gid范围分配的UID / GID。如果idmap backend已将其指定为ldap:ldap://hostname[:389]，则Winbind将从LDAP数据库获取此信息，而不是使用本地映射。
