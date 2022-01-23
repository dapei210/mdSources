### rpctrans模块
#### 架构如下
rpctrans是rpc调用的框架，与控制台的jump_server建立websocket长连接，完成序列化与反序列化，使用的开源程序为python的websocket.
rpctrans创建两个队列read_queue和write_queue，前者用来保存控制台发送过来的请求，后者保存响应消息。
rpctrans创建四个线程：
1. skylarchecker_thread是负责检查skylar_ip和mid是否与当前配置文件（/usr/local/nac/script/config_mgr.json）一致的守护线程。
2. read_pump从websocket收到控制台发送的数据包放入到read_queue中。
3. write_pump从write_queue中取出响应消息，并通过websocket发送给控制台。
4. handler_thread从read_queue中读取请求，并生成的响应消息放到write_queue中

#### rpchandler模块
rpchandler解析请求中数据，并调用具体的业务处理模块
taskapp和rpcapi03中定义每个具体业务的实现


### config_mgr
周期性地检查控制台配置的策略是否发生变化，如果策略发生变化，将请求控制台获取变化的策略，然后添加上topic，发送给nsqd


