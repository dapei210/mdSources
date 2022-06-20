涉及到两个配置文件clients.conf以及proxy.conf
## 配置文件 

### proxy.conf
proxy.conf主要是用来配置被代理的radius server（也叫home server） 和 realm， 以及他们之间的映射关系，也就是request转发到那个home server。该配置文件中主要有三个配置项目:

1. home_server ： 主要用来配置home server的属性， 也就是被代理的radius server的属性，主要包括home server的类型（认 证，计费，认证计费等）、ip地址、端口、密码（server和client之间的密码）、是否需要authenticator、重启时间等等。

2.  home_server_pool ： 用来定义home server集。 它可以把多个home server放到一个home_server_pool中，然后定义这些home  server之间的协作关系，可以是load-balance，fail-over等

3. realm : 用来定义域。 在该项目中可以指定 home_sever_pool，这样就把该域的请求转发到home_server_pool中的一个home server上。如果realm中不指定home_server_pool，那么该请求就会在本地处理。

总之， 一个home server指定了一台被代理的radius服务器，然后home_server_pool又将一些home server放到一个pool中，在realm中指定home_server_pool，这样也就把域和radius server联系在一起了


在代理freeRadius server上，收到一个请求后，就会检查该请求的域，然后去匹配proxy.conf中定义的realm，然后将请求转发到相应的radius server上去。没有域的请求会使用realm NULL来处理，如果没有找到匹配的 会使用realm DEFAULT来处理。

### client.conf
client.conf 主要是用来配置radius server的客户端的，server的客户端的概念是相对的，可以是NAS，直接的client设备，也可以是代理radius server。 如果某一台freeRADIUS启用了代理，那么对于被他代理的radius server 来说他就是client。 所以，也需要在被代理的radius server的client.conf中，添加上代理radius server。


### 用例说明
代理freeRadius（172.18.2.32），被代理freeRADIUS server（172.18.2.31，用来处理mydomain.com的请求）

1. 代理freeradius上配置

```
#配置home server 

home_server localhost{  

type = auth  

ipaddr = 172.18.2.31  #home server ip地址  

port = 1812  

secret = testing123   #密码（client和server间的）  

require_message_authenticator = no  

response_window = 20  

zombie_period = 40  

revive_interval = 120  

status_check = status-server  

check_interval = 30  

num_answers_to_alive = 3  

max_outstanding = 65536

 coa {  

 irt = 2  

 mrt = 16  

 mrc = 5  

 mrd = 30  

 }  

}  

#配置 home server pool  

home_server_pool myProxyPool {  

type = fail-over  

home_server = localhost    

#指定home_server 可以指定多个  

}  

#配置realm  

realm mydomain.com {  

auth_pool = myProxyPool # 指定home_server_pool  

}   



clients.conf中添加

client 172.18.2.0/24{  

 secret = testing123

 shortname = myproxy  

 nastype     = other

 require_message_authenticator = no   

}  

```

2. 被代理freeradius server上配置

```
#proxy.conf设置，添加如下

realm mydomain.com{  

auth_pool = myProxyPool

}  


#clients.conf配置
client 172.18.2.32{  

 secret = testing123

 shortname = myproxy  

 nastype     = other

 require_message_authenticator = no   

}  

```
