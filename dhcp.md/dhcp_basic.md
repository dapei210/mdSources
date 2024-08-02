<<<<<<< HEAD


在客户端正式分配了IP地址之前的第一次IP地址请求过程中，所有DHCP报文都是以广播方式发送的，包括客户端发送的DHCP Discover和DHCP Request报文，以及DHCP服务器发送的DHCP Offer、DHCP ACK和DHCP NAK报文。当然，如果是由DHCP中继器转的报文，则都是以单播方式发送的。另外，IP地址续约、IP地址释放的相关报文都是采用单播方式进行发送的
=======
### ISC DHCP

#### 报文类型
__DHCP Discover__

DHCP客户端在请求IP地址时并不知道DHCP服务器的位置，因此DHCP客户端会在本地网络内以广播方式发送Discover请求报文，以发现网络中的DHCP服务器。所有收到Discover报文的DHCP服务器都会发送应答报文，DHCP客户端据此可以知道网络中存在的DHCP服务器的位置

__DHCP Offer__
DHCP服务器收到Discover报文后，就会在所配置的地址池中查找一个合适的IP地址，加上相应的租约期限和其他配置信息（如网关、DNS服务器等），构造一个Offer报文，发送给DHCP客户端，告知用户本服务器可以为其提供IP地址。但这个报文只是告诉DHCP客户端可以提供IP地址，最终还需要客户端通过ARP来检测该IP地址是否重复

__DHCP Request__

DHCP客户端可能会收到很多Offer应答报文，所以必须在这些应答中选择一个。通常是选择第一个Offer应答报文的服务器作为自己的目标服务器，并向该服务器发送一个广播的Request请求报文，通告选择的服务器，希望获得所分配的IP地址。另外，DHCP客户端在成功获取IP地址后，在地址使用租期达到50%时，会向DHCP服务器发送单播Request请求报文请求续延租约，如果没有收到ACK报文，在租期达到87.5%时，会再次发送广播的Request请求报文以请求续延租约。

__DHCP ACK__

DHCP服务器收到Request请求报文后，根据Request报文中携带的用户MAC来查找有没有相应的租约记录，如果有则发送ACK应答报文，通知用户可以使用分配的IP地址。
客户端在收到服务器返回的DHCP_ACK确认报文后，会以广播的方式发送免费ARP报文(该报文中，源IP地址和目标IP地址都是本机IP地址，源MAC地址是本机MAC地址，目的MAC地址是广播MAC地址)，探测是否有主机使用服务器分配的IP地址，如果在规定的时间内没有收到响应，才使用此地址。否则，客户端回发送DHCP_DECLINE报文给DHCP服务器，并重新申请IP地址。

__DHCP NACK__

如果DHCP服务器收到Request请求报文后，没有发现有相应的租约记录或者由于某些原因无法正常分配IP地址，则向DHCP客户端发送NAK应答报文，通知用户无法分配合适的IP地址。

__DHCP Release__
当DHCP客户端不再需要使用分配IP地址时，就会主动向DHCP服务器发送RELEASE请求报文，告知服务器用户不再需要分配IP地址，请求DHCP服务器释放对应的IP地址。

__DHCP Decline__

DHCP客户端收到DHCP服务器ACK应答报文后，通过地址冲突检测发现服务器分配的地址冲突或者由于其他原因导致不能使用，则会向DHCP服务器发送Decline请求报文，通知服务器所分配的IP地址不可用，以期获得新的IP地址


__DHCP Inform__
DHCP客户端如果需要从DHCP服务器端获取更为详细的配置信息，则向DHCP服务器发送Inform请求报文；DHCP服务器在收到该报文后，将根据租约进行查找到相应的配置信息后，向DHCP客户端发送ACK应答报文。目前基本上不用了

![dhcp报文格式](dhcpd%E5%8D%8F%E8%AE%AE%E6%8A%A5%E6%96%87.png)

Op： 报文类型，分为 两大类： Request（1） 和 Reply（2）

HW Type： 硬件类型，一般是以太网：1

HW Len： 硬件地址长度，单位字节。对应以太网：6（mac地址长度为6字节48bit）

Transaction ID：事务ID，随机数，有客户端生成，服务器Reply时，会把Request中的Transaction拷贝到Reply报文中。

Secs： 距离第一次发射IP请求或Renew请求过去的秒数

Flags：标志位，目前仅第一个bit有使用，置1 标明广播
(通过discover或request报文通知dhcp server是通过单播方式还是广播方式通过DHCP offer或dhcp ack响应报文)

Client IP Address：当前客户端的IP地址，如果当前客户端没有IP地址，则置0

Your IP Address： 服务器想客户端提供IP地址时，会把IP地址填入本字段

（Next）Server IP Address：客户端引导时需要的另一个服务器的IP地址

Gateway （Relay） IP Address： 网关（中继）IP地址，有DHCP 中继器在转发DHCP报文的时候填入

Server Name： Server名字，有64bytes，一般不使用，填充为0

Boot File name： boot file的路径，128bytes， 一般不使用，填充为0

Option： 选项，不定长度。 DHCP报文中比较重要的字段。

如果Option没有值，则只有标志位之类的内容，则以一个字节表示

如果Opiton有值，即Opiton是以下name-value对，则Opiton需要多个字节表示，其中第一个字节表示 option的名字，第二字节表示value的长度，第三个字节开始表示value。

常用option字段如下：


Option      名称         描述

0       Pad  填充位

1       Subnet Mask    子网 掩码

3       Router Address        路由器地址

6       DNS DNS server

15     DN   域名

50     Requested IP Address     请求的IP地址

51     Address Lease Time         地址租约时间

53     DHCP Message Type        DHCP 消息类型，如Discover、Request、Offer、ACK等

54     Server Identifier       服务器标识

55     Parameter Request List  参数请求列表

56     DHCP Error Message       DHCP 错误消息

58     Lease Renewal Time        租约续期时间

59     Lease Rebinding Time      租约重新设定的时间

61     Client Identifier        客户标识

119  Domain Search List 域名查找列表

255  End  结束



IP的回收主要是通过两种途径。一是当client不需要这个IP时（如关机），通过发送DHCPRELEASE包给server来结束lease；其次是server会定期检查已分配的IP lease，将过期lease的IP回收。



#### DHCP中继代理服务
因为客户端初次从DHCP服务器获取IP地址过程中，所有从DHCP客户端发出的请求报文和所有由DHCP服务器返回的应答报文都是以广播方式进行发送的，
所以DHCP服务只适用于客户端和服务器处于同一子网的情况，因为广播包是不能穿越子网的。

基于DHCP服务以上的限制，如果DHCP客户端与服务器之间隔了路由设备，就不能通过服务器获取IP地址。这也意味着，如果想要多个子网中的主机进行动态IP地址分配，就需要在
网络中的所有子网都设置一个DHCP服务器，很没有必要。

DHCP中继功能可以很好地解决以上问题。通过DHCP中继代理服务，与DHCP服务器不在同一子网的DHCP客户端可以通过DHCP中继代理（通常由路由器、三层交换机担当，但需要开启DHCP中继功能）与位于其他网段的DHCP服务器通信，最终使DHCP客户端获取到从DHCP服务器上分配而来的IP地址。此时的DHCP中继代理位于客户端和服务器之间，负责广播DHCP报文的转发

DHCP中继代理必须至少有一个端口连接了DHCP客户端所在子网，另一端又直接连接了DHCP服务器的一个端口所在子网。当然，一个DHCP中继代理可以同时连接多个子网，作为多个DHCP客户端子网的中继代理，这样，多个子网上的DHCP客户端又可以使用同一个DHCP服务器来进行IP地址的自动分配了，既节省了成本，又便于进行集中管理。
>>>>>>> a03b1dfb004bdb8a6fddf081d8e114bf6346e79c

