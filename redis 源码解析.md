# redis 源码解析（上）

### redis 6.0.5 源码下载

```
http://download.redis.io/releases/redis-6.0.5.tar.gz
```



one thread one loop/cycle

```
while (exitFlag) {
	//1.检测定时器，处理定时器事件
	
	//2. 调用IO复用函数，检测各种读写、出错事件
	
	//3. 处理IO事件
	
	//4. 做一些其他事情1
	
	//5. 做一些其他事情2
}
```

> see: https://blog.csdn.net/analogous_love/article/details/53033793

编译禁用优化的redis源码

```
make CFLAGS="-g -O0"
```



epoll_wait 

epollfd = epoll_create

listenfd 

clientfd



### 开启侦听过程

1. 创建listenfd，socket
2. bind
3. listen



### epollfd 如何创建的

epollfd存储在eventLoop->apidata字段中



### 把listenfd挂载到epollfd上

initServer()







listenfd 读事件触发，处理：接受连接，产生clientfd

将clientfd（cfd）挂载到epollfd



通用做法：

线程1  epollfd listenfd

线程2~5 clientfd



侦听fd的读事件处理函数：acceptTcpHandler



### clientfd 挂载到epollfd上

connSocketSetReadHandler

readQueryFromClient



connSocketSetReadHandler 在这个函数里面挂载

读事件回调函数：readQueryFromClient



![](redis流程图1.png)

### clientfd 从epollfd 上移除或者更改



### redis 通信协议

https://redis.io/topics/protocol

http://redisdoc.com/topic/protocol.html



```
*3\r\n$3\r\nSET\r\n$5\r\nmykey\r\n$7\r\nmyvalue\r\n
```

```
SET mykey myvalue
```



### 解包流程

```
while ()
{
	if (当前接受缓冲区的数据 < 一个包大小)
		break;
		
	//取出第一个包大小，解包处理
	

}
```

定时器设计