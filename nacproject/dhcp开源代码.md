## dhcpd
### /etc/dhcpd.conf
/etc/dhcpd.conf通常包括三部分：parameters、declarations 、option

`shared-network` 用来告知是否一些子网络分享网络 
`subnet` 描述一个IP是否属于该子网
`range` 提供动态分配IP的范围
`fixed-address ip` 分给客户端一个固定IP
`allow unknown-clients;deny unknown-client` 是否动态分配IP给未知的使用者。 
`allow bootp;deny bootp` 是否响应激活查询。 
`allow booting;deny booting` 是否响应使用者查询

#### option
配置dhcp的可选参数，全部使用option关键字作为开始

`routers` 为客户端指定网关
`subnet-mask` 为客户端设定子网掩码 
`domain-name` 为客户端指明DNS名字。 
`domain-name-servers` 为客户端指明DNS服务器IP地址。 
`host-name` 为客户端指定主机名称。 
`routers` 为客户端设定默认网关。 
`broadcast-address` 为客户端设定广播地址。 
`ntp-server` 为客户端设定网络时间服务器IP地址。 
`time－offset` 为客户端设定和格林威治时间的偏移时间，单位是秒


### dhcp_policy


 


## coredhcp
