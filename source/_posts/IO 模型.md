## IO （input-output）

为什么要对IO 优化， 因为cpu的处理速度是 硬盘的几千万倍 所以需要对IO进行优化

### BIO - Blocking IO（阻塞IO）

![image-20200427173724741](https://gitee.com/qilitang/Img/raw/master/img/image-20200427173724741.png)

来一个io 就起一个 threading 进行io的处理 所以这是  BIO

```python
import socket
server = socket.socket()
server.bind(("127.0.0.1",8888))
while True:
  
```



### NIO

![image-20200428173705643](https://gitee.com/qilitang/Img/raw/master/img/image-20200428173705643.png)

NIO 单线程IO  selector 不仅会支持客户端与服务端的链接， 而且还会**轮询**进行客户端与服务端消息的收发 

### NIO-reactor 模式

![image-20200428174728604](https://gitee.com/qilitang/Img/raw/master/img/image-20200428174728604.png)



### AIO 

![image-20200428175219125](https://gitee.com/qilitang/Img/raw/master/img/image-20200428175219125.png)

### netty 用的是 NIO 为什么不用AIO （JAVA）

在linux 上， NIO 和AIO 都是基于 epoll 模型实现的。 epoll模型的底层是轮询。 所有使用NIO 和AIO 是没有本质区别的， 在win上AIO的实现是真实的 异步IO

![image-20200428180812053](https://gitee.com/qilitang/Img/raw/master/img/image-20200428180812053.png)