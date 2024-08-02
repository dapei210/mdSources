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

Go支持外部实现的路由器，ListenAndServe的第二个参数就是用以配置外部路由器的，它是一个Handler接口，即外部路由器只要实现了Handler接口就可以，可以在自己实现的路由器的ServeHTTP里面实现自定义路由功能。

实现的简易路由器
```
package main

import (
    "fmt"
    "net/http"

)

type MyMux struct {

}

func (p *MyMux) ServeHTTP(w http.ResponseWriter, r *http.Request) {

    if r.URL.Path == "/" {
        sayHello(w,r)
        return
    }
    http.NotFound(w,r)
    return

}

func sayHello(w http.ResponseWriter, r *http.Request) {

    fmt.Fprint(w,"hello myroute")

}

func main() {
    mux := &MyMux{}
    http.ListenAndServe(":9090",mux)

}
```

#### 执行流程

**首先调用Http.HandleFunc**

1. 调用了DefaultServerMux的HandleFunc
2. 调用了DefaultServerMux的Handle
3. DefaultServeMux的map[string]muxEntry中增加对应的handler和路由规则

**其次调用http.ListenAndServe()**

4. 实例化Server
5. 调用Server的ListenAndServe()
6. 调用net.Listen("tcp",addr)监听端口
7. 启动一个for循环，在循环体中Accept请求
8. 对每个请求实例化一个Conn,并且启动一个goroutine为每个请求进行服务go c.serve()
9. 读取每一个请求的内容w, err := c.readRequest()
10. 判断handler是否为空，如果没有设置handler,handler就设置为DefaultServeMux
11. 调用handler的ServeHttp
`mux.handler(r).ServeHTTP(w,r)`



### session和cookie


### xml,json文本处理

#### xml
解析xml
`func Unmarshal(data []byte, v interface{}) error`



### 模板

1111


