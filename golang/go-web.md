## http

post请求三种编码方式

1. application/x-www-form-urlencoded

消息体格式：FirstName=Hello&SecondName=World

2. multipart/form-data

消息体格式： 分隔符----****

3. application/json


## net/http接口

1. Handler：处理器接口，定义在 net/http 包中。实现该接口的类型，其对象可以注册到多路复用器中；
2. Handle：注册处理器的方法；
3. HandleFunc：注册处理器函数的方法；
4. HandlerFunc：底层类型为func (w ResponseWriter, r *Request)的新类型，实现了Handler接口。它连接了处理器函数与处理器。

如果注册的 URL 不是以/结尾的，那么它只能精确匹配请求的 URL



## gin

