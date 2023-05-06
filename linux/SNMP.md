基于TCP/IP的SNMP网络管理框架由3个主要部分组成，即管理信息结构SMI（Structure ofManagement Information）、管理信息库MIB和管理协议SNMP


1. SMI：定义了SNMP框架所用信息的组织和标识，为MIB定义管理对象及使用管理对象提供模板

2. MIB：定义了可以通过SNMP进行访问的管理对象的集合

3. SNMP协议：定义了网络管理者如何对代理进程的MIB对象进行读写操作


## SNMP管理模型
SNMP的管理模型由SNMP管理站和SNMP代理组成，对网络的管理与维护是通过SNMP管理站与SNMP代理之间的交互工作完成的。SNMP管理站是一个中心节点，负责收集维护各个SNMP元素的信息，并对这些信息进行处理，最后反馈给网络管理员；而SNMP代理是运行在被管理的网络节点之上，负责统计该节点的各项信息，并且与SNMP管理站交互，接收并执行管理站的命令，上传各种本地的网络信息

下图是SNMP的管理模型，包括四个部分：网络管理系统NMS（Network Management Station）、代理进程Agent、被管对象Management object和管理信息库MIB（Management Information Base）四部分组成


