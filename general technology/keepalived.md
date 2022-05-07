### 原理
Keepalived采用VRRP热备份协议实现linux服务器的多机热备功能。
VRRP，虚拟路由冗余协议，是针对路由器的一种备份解决方案，为了解决静态路由的高可用。

VRRP协议基本架构如下：
虚拟路由器由多个路由器组成，每个路由器都有各自的IP和共同的VRID(0-255)，其中一个VRRP路由器通过竞选成为MASTER，占有VIP，对外提供路由服务，其余的成为BACKUP，MASTER以IP组播（组播地址：224.0.0.18）形式发送VRRP协议包，与BACKUP保持心跳连接，若MASTER不可用（或BACKUP接收不到VRRP协议包），则BACKUP通过竞选产生新的MASTER并继续对外提供路由服务，从而实现高可用。

### 工作方式
工作类型分为抢占式和非抢占式：

抢占式：MASTER从故障中恢复后，会将VIP从BACKUP节点中抢占过来

非抢占式：MASTER恢复后不抢占BACKUP升级为MASTER后的VIP

### 配置文件