## 802.1x协议
802.1x协议是一种基于端口的网络接入控制协议，即指在局域网接入设备的端口这一级验证用户身份并控制其访问权限。

#### 优点

802.1X认证中为二层协议，不需要到达三层，对接入设备的整体性能要求不高，可以有效降低建网成本。
认证报文和数据报文通过逻辑接口分离，提高安全性

#### 认证系统

典型的CS架构，包括三个实体：客户端、接入设备以及认证服务器


**客户端**一般为用户终端设备，使用客户端软件发起802.1x认证，客户端必须要支持LAN上的可扩展认证协议EAPoL

**接入设备**为支持802.1x协议的网络设备，其为客户端提供接入局域网的端口，作为客户端和认证服务器之间的中介，接收客户端的身份认证信息，与认证服务器进行身份验证信息交互，最后根据身份认证的状态来控制其对网络的访问权限。

**认证服务器**为radius服务器，用于实现对用户进行认证、授权和计费。



## RADIUS

RADIUS远程认证拨入用户服务，是用于NAS和AAA服务器间通信的一种协议。RADIUS对AAA的三个组件都提供支持

当用户想要通过某网络与NAS建立连接从而获得访问其他网络的权利时，NAS可以在NAS上进行本地认证计费，或把用户信息传递给RADIUS服务器，由RADIUS进行认证计费。
RADIUS 协议规定了NAS与RADIUS 服务器之间如何传递用户信息和计费信息，即两者之间的通信规则，RADIUS服务器负责接收用户的连接请求，完成认证，并把用户所需的配置信息返回给NAS。
用户获得授权后，在其正常上线、在线和下线过程中，RADIUS服务器还完成对用户账号计费的功能。


### RADIUS主要特性
#### C/S模式
   
NAS作为RADIUS的客户端，负责将用户信息传递给指定的RADIUS服务器，并负责执行返回的响应。RADIUS服务器接收用户的连接请求，鉴别用户，并为客户端返回为用户提供服务的配置信息。一个RADIUS服务器可以作为其他的RADIUS Server或其他种类认证服务器的代理。

#### 网络安全

客户端和RADIUS服务器之间的事务是通过使用不会在网上传输的共享密钥机制进行鉴别的。另外，在客户端和RADIUS服务器之间的任何用户密码都是被加密后传输的。

#### 灵活的认证机制

PAP CHAP MSCHAP等认证

#### 可扩展协议(EAP)

RADIUS协议具有很好的扩展性。RADIUS数据包是由包头和一定数目的属性（Attribute）构成的。新的属性可以在不中断已存在协议执行的前提下进行增加。


![radius报文结构](radius报文结构.png)

**Code**(1个字节)

|字段值|类型|发送端|
|---|---|----|
|1|Access-Request|NAS|
|2|Access-Accept|Radius Server|
|3|Access-Reject|Radius Server|
|4|Accouting-Request|NAS|
|5|Accouting-Response|Radius Server|
|11|Access-Challenge|Radius Server|


**Identifier**(1个字节)：用来匹配请求报文和响应报文，以及检测在一段时间内重发的请求报文。接入设备发送请求报文后，服务器返回的响应报文中的Identifier值应与请求报文中的Identifier值相同。

**Length**(2个字节)：用来指定RADIUS报文的长度。超过Length取值的字节将作为填充字符而忽略。如果接收到的报文的实际长度小于Length的取值，则该报文会被丢弃。

**Authenticator**(16个字节)：根据数据包是来自接入设备还是来自服务器的响应而不同，它还取决于数据包类型，例如Access-Request或Accounting-Request。
如果是请求，则该字段称为请求认证码(Request Authenticator)；如果是响应，则该字段称为响应认证码(Response Authenticator)。
Request Authenticator的值是一个随机数，不会重复，Response Authenticator的值是响应报文中所有字段的MD5哈希值，包括接入设备端和服务端的共享密钥。

`ResponseAuth=MD5(Code+ID+Length+RequestAuth+Attributes+Secret)`

**Attributes**(0或多个字节)：属性是接入端和服务器之间的信息载体，接入端使用它们来提供客户端用户的信息，同时服务器用它们来构造响应信息，接入端则通过响应消息来控制用户的连接。属性是以属性对AVP(Attribute Value Pairs)存在的。

![radius attribute结构](radius-attribute结构.png)

**Type**(1个字节)：数值同属性相关联，将这些属性分配具体的数值由IANA控制，属性名通常具有足够的描述性，可以推断出其功能，如User-Name(1),User-Password(2), or NAS-IP-Address(4).

**Length**(1个字节)：通过它可以获取AVP结束位置。
**Value**(0或多个字节)：value字段可以包含以下数据类型之一：文本、字符串、IP、整形或时间


## EAP协议

![eap-packet](eap-packet.png)

**Code**(1个字节)：EAP包有四种类型，Request(1)   Response(2)   Success(3)   Failure(4)

**Identifier**(1个字节)：用于匹配Request和Response

**Length**(2个字节)：EAP数据包的长度，包括Code、ID、Length以及Data各字段。超过Length取值的字节将作为填充字符而忽略。如果接收到的报文的实际长度小于Length的取值，则该报文会被丢弃。

**Data**(0或多个字节)：其由Code的值来决定，Request和Response报文，Data包含Type、Type Data两个字段，其中Type为1个字节，Type Data为多个字节，内容由Type来决定；当Code为Success和Failure报文，Date为0字节。
其中Type常用取值如下：

|字段值|类型|说明|
|---|---|---|
|1|	Identity|客户端发送用户输入的用户名信息|
|3	|NAK	|仅用于Response帧，表示否定确认|
|4|	EAP-MD5	|认证方法为MD5质询法|
|13|	EAP-TLS|	
|21	|EAP-TTLS|	
|25|	EAP-PEAP|	
|26|	EAP-MSCHAPv2|



![eap协议类型比较](eap协议类型比较.png)


## 802.1x认证架构实现

802.1x认证是使用EAP来实现客户端、接入设备以及认证服务器之间的信息交互。
在客户端与接入设备之间，EAP报文使用EAPoL进行封装，直接承载于LAN环境。
接入设备与认证服务器之间，EAP报文直接封装到RADIUS。

### EAPoL消息

EAPoL是802.1x认证定义的一种报文封装格式，主要用于在客户端和接入设备端之间传送EAP协议报文，以允许EAP协议报文在LAN上传送。

![EAPoL报文](EAPoL报文.png)

PAE Ethernet Type：表示协议类型，为0x888E。

Protocol Version：表示EAPOL帧的发送方所支持的协议版本号。

Type：表示EAPOL数据帧类型。

|类型|	说明|
|---|---|
|EAP-Packet(0x00)| 	认证信息帧，用于承载认证信息，该帧在接入端重新封装并承载于RADIUS协议上，发往认证服务器|
|EAPOL-Start(0x01)|	认证发起帧|
|EAPOL-Logoff(0x02)|	退出请求帧|

Length(2个字节)：表示数据长度，也就是Packet Body字段的长度。

当Type为EAP-Packet时，Packet Body为EAP数据包。

![1x认证架构](1x认证架构.png)


### EAP over RADIUS

RADIUS为支持EAP认证增加了两个属性：EAP-Message（EAP消息）和Message-Authenticator（消息认证码）。

EAP-Message(Type数值为79)

Message-Authenticator(Type数值为80)，这个属性用于在使用EAP认证方法的过程中，避免接入请求包被窃听。在含有EAP-Message属性的数据包中，必须同时也包含Message-Authenticator，否则该数据包会被认为无效而被丢弃，而且服务器或用户收到报文后，还将使用EAP消息和共享密钥作为输入计算该值，如果与传过来的属性值不一致，也将丢弃这个报文。

对于Access-Request消息，需要使用共享密钥，Message-Authenticator计算方法如下：

`Message-Authenticator＝MD5(Code+Type+Identifier+Length+16 Zero Octets+Attributes+Secret)` 

对于Access-Challenge、Access-Accept、Access-Reject消息，Message-Authenticator计算方法：

`Message-Authenticator＝MD5(Code+Type+Identifier+Length+Request Authenticator+Attributes+Secret)`













