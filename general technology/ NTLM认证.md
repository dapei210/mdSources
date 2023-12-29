### 本地认证
Windows不存储用户的明文密码，它会将用户的明文密码经过加密后存储在 SAM (Security Account Manager Database，安全账号管理数据库)中。

`SAM文件的路径是 %SystemRoot%\system32\config\sam`

在进行本地认证的过程中，当用户登录时，系统将用户输入的明文密码加密成NTLM Hash，与SAM数据库中的 NTLM Hash 进行比较，从而实现认证

`winlogon.exe -> 接收用户输入 -> lsass.exe -> (认证)`

用户注销、重启、锁屏后，操作系统会让 winlogon显示登录界面，也就是输入框，接收输入后，将密码交给 lsass进程，这个进程中会存一份明文密码，将明文密码加密成NTLM Hash，对比SAM数据库中的Hash进行验证

### NTLM和NTLM Hash
NTLM是一种网络认证协议，与NTLM Hash的关系就是：NTLM网络认证协议是以 NTLM Hash作为根本凭证进行认证的协议

#### NTLM网络认证机制
NTLM是一种基于质询/应答 （Challenge/Response ）消息交换模式的认证机制，常用于工作组和域环境下登录场景的身份认证

#### NTLM认证方式
1. 交互式认证：使用域账号登录到客户端；涉及两部分（客户端、域控）
2. 非交互式认证：在已经登陆的客户端上使用SSO的方式访问一台服务器；涉及三部分（客户端、域控、服务器）

#### NTLM认证流程
NTLM协议的认证过程有三步：

1. 协商：主要用于确认双方协议版本（NTLMv1、NTLMv2等）
2. 质询：质询/应答 （Challenge/Response）模式，用于消息交换
3. 验证：验证身份合法性，通常由Server端或DC完成这个过程

大致流程如下：
1. 使用用户名和密码尝试登录客户端
2. 客户端对密码进行哈希处理并缓存密码hash，丢弃实际的明文密码(不存储)，然后将用户名发送到服务器，发起认证请求
3. 服务器生成一个16字节的随机数，称为质询 (Challenge) 或随机数 (nonce.aspx)，并发送给客户端
4. 客户端使用缓存的用户密码的Hash对此Challenge进行加密，加密结果为Response（暂且叫做Challenge1），然后将结果发送给服务器
5. 服务器发送三项数据给域控制器。用户名；发送给客户端的Challenge(已加密)；从客户端接收到的Response。
6. 域控服务器使用用户名从AD域找那个检索该用户密码的NTLM Hash，并使用此NTLM Hash来加密Challenge，然后把这个值和客户端计算的响应值（Challenge1）进行比较。如果它们相同，则验证成功。
注意：经过NTLM Hash加密Challenge的结果在网络协议中称之为Net NTLM Hash。在以上流程中，用户的登录密码Hash即为NTLM Hash ，Response中则包含Net-NTLM Hash。

