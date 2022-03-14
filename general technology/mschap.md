
https://datatracker.ietf.org/doc/html/rfc2759.html

###  MS-CHAP-Challenge and MS-CHAP2-Response
MS-CHAP-Challenge是客户端生成的随机16字节数据
#### MS-CHAP-Response
50字节的MS-CHAP-Response属性：
```
[0,1]  Flags  \x00\x00
[2,17] PeerChallenge
[18,25] Reserved \x00\x00\x00\x00\x00\x00\x00\x00
[26,49] NtResponse
```
随机生成的16字节PeerChallenge，连同MS-CHAP-Challenge、UserName、Password作为输入参数，调用方法GenerateNTResponse 得到 NtResponse
`GenerateNTResponse(MS-CHAP-Challenge, PeerChallenge, UserName, Password) `

将结果同MS-CHAP-Response消息中获取的NtResponse做比较，相等则验证成功

### 认证响应
由于认证成功后响应消息属性MS-CHAP2-Success=AuthenticatorResponse
而其依赖password（nt-hash-hash），需要auth_center模块校验成功后，响应消息中带password（nt-hash-hash）



