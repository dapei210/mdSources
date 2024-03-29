### TCP Socket
net包中的类型TCPConn用来作为客户端和服务器交互的通道，有两个主要函数：
```
func (c *TCPConn) Write(b []byte) (n int, err os.Error)
func (c *TCPConn) Read (b []byte) (n int, err os.Error)
```
TCPAddr类型用来表示一个TCP的地址信息
```
type TCPAddr struct {
    IP    IP
    Port  int
}
```
ResolveTCPAddr函数用来获取一个TCPAddr
```
func ResolveTCPAddr(net, addr string) (*TCPAddr,os.Error)
// 其中net参数"tcp4","tcp6","tcp"; 
// addr表示域名或者IP地址，如“www.google.com:80”或者“127.0.0.1:80”.
```


### TCP Client
net包中的DialTCP函数用来建立一个TCP连接，并返回TCPConn类型的对象，当连接建立成功，服务器端也创建了一个同类型的对象，C/S可以通过各自拥有的TCPConn对象来进行数据交换
```
func DialTCP(net string, laddr, raddr *TCPAddr) (c *TCPConn,err os.Error)
// laddr表示本机地址，一般设为nil；raddr表示服务器地址

```


### TCP Server
```
func ListenTCP(net string, laddr *TCPAddr) (l *TCPListener, err os.Error)

func (l *TCPListener) Accept() (c Conn, err os.Error)
```

### 控制TCP连接
```
func (c *TCPConn) SetTimeout(nsec int64) os.Error

func (c *TCPConn) SetKeepAlive(keepalive bool) os.Error
```

### UDP Socket
```
func ResolveUDPAddr(net, addr string) (*UDPAddr, os.Error)

func DialUDP(net string, laddr, raddr *UDPAddr) (c *UDPConn, err os.Error)

func ListenUDP(net string, laddr *UDPAddr) (c *UDPConn, err os.Error)

func (c *UDPConn) ReadFromUDP(b []byte) (n int, addr *UDPAddr, err os.Error)

func (c *UDPConn) WriteToUDP(b []byte, addr *UDPAddr) (n int, err os.Error)
```



### WebSocket
痛点：

1. 早期，为了实现推送技术，采用轮询，即每隔一段时间向服务器发送请求，然后服务器返回最新的数据给客户端


概念

Websocket 使用 ws 或 wss 的统一资源标志符（URI），其中 wss 表示使用了 TLS 的 Websocket

WebSocket 与 HTTP 和 HTTPS 使用相同的 TCP 端口，可以绕过大多数防火墙的限制


采用了一些特殊的报头，使得客户端和服务器只需要一个握手动作，就可以在C/S之间建立一条连接通道，且此连接会保持在活动状态，相比传统HTTP有如下好处：
1. 较少的控制开销：在连接创建后，服务器和客户端之间交换数据时，用于协议控制的数据包头部相对较小
2. 更强的实时性：由于协议是全双工的，所以服务器可以随时主动给客户端下发数据
3. 保持连接状态：与 HTTP 不同的是，WebSocket 需要先创建连接，这就使得其成为一种有状态的协议，之后通信时可以省略部分状态信息
4. 更好的二进制支持：WebSocket 定义了二进制帧，相对 HTTP，可以更轻松地处理二进制内容
5. 可以支持扩展：WebSocket 定义了扩展，用户可以扩展协议、实现部分自定义的子协议
6. 一个web客户端只建立了一个TCP连接
   
应用领域



###  Websocket和http2.0


### RPC
实现了函数调用模式的网络化，内部操作的步骤如下：
1. 调用客户端句柄，执行传送参数
2. 调用本地系统内核发送网络信息
3. 消息传送到远程主机
4. 服务器句柄得到消息并取得参数
5. 执行远程过程并将结果返回服务器句柄
6. 调用远程系统内核将消息传回本地主机
7. 客户句柄由内核接收消息
8. 客户接收句柄返回的数据