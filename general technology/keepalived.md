### 原理
Keepalived采用VRRP热备份协议实现linux服务器的多机热备功能。
VRRP，虚拟路由冗余协议，是针对路由器的一种备份解决方案，为了解决静态路由的高可用。

VRRP协议基本架构如下：
虚拟路由器由多个路由器组成，每个路由器都有各自的IP和共同的VRID(0-255)，其中一个VRRP路由器通过竞选成为MASTER，占有VIP，对外提供路由服务，其余的成为BACKUP，MASTER以IP组播（组播地址：224.0.0.18）形式发送VRRP协议包，与BACKUP保持心跳连接，若MASTER不可用（或BACKUP接收不到VRRP协议包），则BACKUP通过竞选产生新的MASTER并继续对外提供路由服务，从而实现高可用。

```
[root@localhost freeradius-server-2.2.5]# tcpdump -i ha -nvv vrrp
tcpdump: listening on ha, link-type EN10MB (Ethernet), capture size 262144 bytes
10:25:03.524705 IP (tos 0xc0, ttl 255, id 53334, offset 0, flags [none], proto VRRP (112), length 40)
    1.1.1.5 > 224.0.0.18: vrrp 1.1.1.5 > 224.0.0.18: VRRPv2, Advertisement, vrid 88, prio 200, authtype simple, intvl 3s, length 20, addrs: 192.168.100.105 auth "skylar6^@"
10:25:06.525000 IP (tos 0xc0, ttl 255, id 53335, offset 0, flags [none], proto VRRP (112), length 40)
    1.1.1.5 > 224.0.0.18: vrrp 1.1.1.5 > 224.0.0.18: VRRPv2, Advertisement, vrid 88, prio 200, authtype simple, intvl 3s, length 20, addrs: 192.168.100.105 auth "skylar6^@"
10:25:09.524927 IP (tos 0xc0, ttl 255, id 53336, offset 0, flags [none], proto VRRP (112), length 40)
    1.1.1.5 > 224.0.0.18: vrrp 1.1.1.5 > 224.0.0.18: VRRPv2, Advertisement, vrid 88, prio 200, authtype simple, intvl 3s, length 20, addrs: 192.168.100.105 auth "skylar6^@"
10:25:12.525024 IP (tos 0xc0, ttl 255, id 53337, offset 0, flags [none], proto VRRP (112), length 40)
    1.1.1.5 > 224.0.0.18: vrrp 1.1.1.5 > 224.0.0.18: VRRPv2, Advertisement, vrid 88, prio 200, authtype simple, intvl 3s, length 20, addrs: 192.168.100.105 auth "skylar6^@"
10:25:15.525096 IP (tos 0xc0, ttl 255, id 53338, offset 0, flags [none], proto VRRP (112), length 40)
    1.1.1.5 > 224.0.0.18: vrrp 1.1.1.5 > 224.0.0.18: VRRPv2, Advertisement, vrid 88, prio 200, authtype simple, intvl 3s, length 20, addrs: 192.168.100.105 auth "skylar6^@"
10:25:18.525218 IP (tos 0xc0, ttl 255, id 53339, offset 0, flags [none], proto VRRP (112), length 40)
    1.1.1.5 > 224.0.0.18: vrrp 1.1.1.5 > 224.0.0.18: VRRPv2, Advertisement, vrid 88, prio 200, authtype simple, intvl 3s, length 20, addrs: 192.168.100.105 auth "skylar6^@"


```

### 工作方式
工作类型分为抢占式和非抢占式：

抢占式：MASTER从故障中恢复后，会将VIP从BACKUP节点中抢占过来

非抢占式：MASTER恢复后不抢占BACKUP升级为MASTER后的VIP, 通过配置nopreempt实现



### 配置文件

抢占式

```
vrrp_script check_services { #设置监测脚本
script "/usr/local/nac/script/keepalived_monitor_process.sh"
interval 60
fall 5
rise 2
}

vrrp_instance skylar { #定义VRRP热备实例
state BACKUP      #热备状态
interface ha      #绑定vrrp的内部接口，用于发送心跳（组播）
track_interface {  #配置要监控的网络接口，一旦接口出现故障，转为fault状态
ha
mgt
}
virtual_router_id 88  #虚拟路由器的ID号，每个热备组要保持一致
priority 200      #优先级，数值越大优先级越高
advert_int 3      #通告间隔秒数(心跳频率)
garp_master_delay 5
authentication {  #认证信息，热备组要保持一致
auth_type PASS   #认证类型
auth_pass skylar6 #密码字串
}
virtual_ipaddress {  #指定漂移地址(VIP)，
192.168.66.135/24 dev mgt
}
preempt_delay 10   #抢占模式下，节点上线后触发新选举操作的延迟时间

track_script { 
    check_services
}
notify_master "/usr/local/nac/script/keepalived_notify.py -m"
notify_backup "/usr/local/nac/script/keepalived_notify.py -b"
notify_fault "/usr/local/nac/script/keepalived_notify.py -f"
notify_stop  "/usr/local/nac/script/keepalived_notify.py -s"
}


```

vrrp_script 用于设置监控脚本，主要是检查 keepalived 程序自身是否正常。 

track_script 用于添加一个跟踪脚本到 VRRP 实例中，这个脚本就是 vrrp_script 配置块中定义的，用于监控 VRRP 实例是否正常。
