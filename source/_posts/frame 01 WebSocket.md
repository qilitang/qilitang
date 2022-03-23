---
title: WebSocket
date: 2017-05-23
comments: false
toc: true
categories: "网络编程"
---

### 实现服务端给客户端推送消息

众所周知, http协议是无连接,基于请求响应的, 所以 经http协议是无法服务端直接给客户端推送消息的

那么 我们就需要找到一种方法, 实现这种需求.

- 轮询(效率低基本不用)

  ```python
  """
  实现思路:
  让浏览器定时向服务端请求,索要数据, 
  
  缺点:
  	消息会有延迟
  	消耗资源
  """
  ```

- 长轮询(兼容性好, 大公司喜欢用 微信网页版)

  ```python
  """
  给每个客户端维护一个队列 之后客户端通过ajax请求索要数据
  如果没有数据会出现pending状态 我们还利用q.get(timeout=10)加异常处理
  实现反复询问
  
  优点
  	消息基本没有延迟
  	消耗资源较低
  	
  网页版的微信qq都在使用该机制
  """
  ```

- websocket( 主流浏览器都支持)

  ```python
  """
  网络协议
  	HTTP  不加密传输
  	HTTPS  加密传输
  		上面两个都是无链接的(短链接)
  			
  	WebSocket  加密传输
  		长链接
  		它的诞生真正意义上实现了服务端给客户端主动推送消息
  """
  ```

### websocket 实现原理

**握手环节**

```python
# 校验双方是否都支持websocket协议
"""
需要在前端浏览器上写一段代码
var ws = new WebSocket('ws://127.0.0.1:8080/')

浏览器访问服务端之后，会立刻在本地生成一个随机字符串

本地保留一份，给服务端也发送一份(请求头)

双方拿到随机字符串之后做相同的处理
	1.先与magic string做字符串的拼接操作
	2.在用sha1和base64加密处理

服务端将处理好的结果返回给客户端(响应头)

浏览器获取比对两者生成的结果是否一致
"""
```

**数据交互**

```python
"""
先读取数据第二个字节的后七位(payload)
根据payload转化成十进制的数字大小做不同的处理操作
=127  再读8个字节
=126  再读2个字节
<=125  不再读取

上述操作完成后，继续往后读取4个字节的数据(masking-key)
基于该数据解析出真实数据
具体解析 公式:位运算
"""
```



### 后端框架实现websocket

```python
"""
并不是所有的后端框架默认就支持websocket！！！

django
	默认不支持
	第三方:channles模块

flask
	默认不支持
	第三方:geventwebsocket模块

tornado
	默认支持
"""
```

#### Django实现websocket

**安装**

```python
pip3 install channels == 2.3

# 注意

	# 解释器版本建议使用python3.6
  # channels 模块版本建议使用2.3
```

**基本配置**

- 配置文件中注册app

  ```python
  INSTALLED_APPS = [
    "channles",
  ]
  ```

- 配置文件中自定义配置

  ```python
  ASGI_APPLICATION = '项目名同名文件名.对应应用下的py文件名(routing).变量名(application)'
  ```

- 在全局新建routing.py 文件(进行路由的分发,  类似url)

  ```python
  from channels.routing import ProtocolTypeRouter,URLRouter
  from django.conf.urls import url
  from app01 import consumers
  
  application = ProtocolTypeRouter({
      'websocket':URLRouter([
          # websocket相关的路由
          url(r'^chat/',consumers.ChatConsumer)
      ])
  })
  ```

- 在对应应用下新建 consumers.py文件,  书写WebSocket的视图类

  ```python
  from channels.generic.websocket import WebsocketConsumer
  
  
  class ChatConsumer(WebsocketConsumer):
      def websocket_connect(self, message):
          """客户端请求建立链接时 自动触发"""
          pass
  
  
      def websocket_receive(self, message):
          """客户端发送数据过来  自动触发"""
          pass
  
  
      def websocket_disconnect(self, message):
          """客户端断开链接之后  自动触发"""
          pass
  ```

- 配置完成后支持http和WebSocket 的原因

  ```python
  class ProtocolTypeRouter:
      """
      Takes a mapping of protocol type names to other Application instances,
      and dispatches to the right one based on protocol name (or raises an error)
      """
      def __init__(self, application_mapping):
          self.application_mapping = application_mapping
          if "http" not in self.application_mapping:
              self.application_mapping["http"] = AsgiHandler
  ```

  ```python
  """
  http协议
  	index路径		index视图函数
  	访问:浏览器窗口直接输入的地址的
  
  websocket协议
  	chat路径		ChatConsumer视图类
  	访问:new WebSocket对象访问
  """
  ```

##### 方法总结

**后端**

```python
# 后端  3个
class ChatConsumer(WebsocketConsumer):
    def websocket_connect(self, message):
        """客户端请求建立链接时 自动触发"""
        self.accept()  # 建立链接  并且自动帮你维护每一个客户端

    def websocket_receive(self, message):
        """客户端发送数据过来  自动触发"""
        # print(message)  # message = {'type': 'websocket.receive', 'text': 'hello world！'}
        text = message.get('text')  # 真正的数据
        # 给客户端发送消息
        self.send(text_data='介绍女朋友')


    def websocket_disconnect(self, message):
        """客户端断开链接之后  自动触发"""
        raise StopConsumer()
```

**前端**

```js
# 前端  4个
var ws = new WebSocket('ws://127.0.0.1:8000/chat/');

    // 1 握手环节验证成功之后 自动触发  onopen
    ws.onopen = function () {
        console.log('握手成功')
    }

    // 2 给服务端发送消息  send
    function sendMsg() {
        ws.send($('#txt').val())
    }

    // 3 一旦服务端有消息 自动触发  onmessage
    ws.onmessage = function (args) {
        // console.log(args)  // args是一个对象
        // 获取发送的数据
        console.log(args.data)
    }

    // 4 断开链接之后  自动触发  onclose
    ws.onclose = function () {
        ws.close()
    }
```



[toc]





