### web服务器预备知识

### GO搭建web服务器

1. Request: 用户请求信息，用来解析用户的请求信息，包括post,get,cookie,url等信息
2. Response: 服务器需要反馈给客户端的信息
3. Conn: 用户的每次请求链接
4. Handler: 处理请求和生成返回信息的处理逻辑


#### Conn
Go使用了goroutines来处理Conn的读写事件，这样每个请求都能保持独立，相互不会阻塞，可以高效地响应网络事件
```
c,err := srv.newConn(rw)
if err != nil {
    continue

}
go c.serve()
```

Conn的serve功能就是调用了http包默认的路由器，通过路由器将本次请求的信息传递到了后端的处理函数，其路由器结构如下：
```
type ServeMux struct {

    mu sync.RWMutex //由于请求涉及到并发处理，需要一个锁机制
    m  map[string]muxEntry //路由规则，一个string对应一个mux实体，string就是注册的路由表达式
}

type muxEntry struct {
    explicit bool  //是否精确匹配
    h        Handler //这个路由表达式对应哪个handler
}

type Handler interface {
    ServeHTTP(ResponseWriter, *Request) //路由实现器
}
```

Handler是一个接口，sayhelloName函数虽然没有实现ServeHTTP这个接口，但是HandlerFunc类型默认实现了ServeHTTP接口，调用了HandlerFunc(f),强制类型转换f成为HandlerFunc类型，这样f就有了ServeHTTP方法。

```
type HandlerFunc func(ResponseWriter, *Request)
func (f HandlerFunc) ServeHTTP(w ResponseWriter,r *Request) {
    f(w,r)

}
```