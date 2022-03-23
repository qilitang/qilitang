---

---



##  介绍

![](https://gitee.com/qilitang/Img/raw/master/img/20200510205519.png)

## 体系结构

![](https://gitee.com/qilitang/Img/raw/master/img/20200510205610.png)

![](https://gitee.com/qilitang/Img/raw/master/img/20200510205710.png)

## 数据类型

![](https://gitee.com/qilitang/Img/raw/master/img/20200510210019.png)

![](https://gitee.com/qilitang/Img/raw/master/img/20200510205910.png)

## MongoDB 的特点

![](https://gitee.com/qilitang/Img/raw/master/img/20200510210125.png)

##  MongoDB 的两种启动方式

###  方式一

在解压目录中，手动建立一个目录用于存放数据文件， 如 data/db

![](https://gitee.com/qilitang/Img/raw/master/img/20200510212831.png)

### 方式二

![image-20200510213055490](https://gitee.com/qilitang/Img/raw/master/img/image-20200510213055490.png)

## shell 连接

![image-20200510214209691](https://gitee.com/qilitang/Img/raw/master/img/image-20200510214209691.png)

## MongoDB 配置文件

```bash
systemlog:
  # MongoDB 发送所有日志输出的目标指定的文件
  destination:file
  # mongod或 mongos应向其发送所有诊断日志记录信息的日志文件路径
  path: "/mongodb/single/log/mongod.log"
  # 当 mongodb 示例重启时， mongos或mongod会讲新的条目附加到现有日志文件末尾。
  logAppend: true
  
storage:
  # mongod 实例 存储其数据的目录。 storage.dbPath 仅适用于mongod
  dbPath: "/mongod/single/data/db"
  joural:
    # 启用或禁用持久性日志以确保数据文件保持有效可恢复
    # 容灾
    enabled: true
processMangement:
  # 启动在后台运行mongos或mongod进程的守护进程模式
  fork: true
net:
  # 服务实例绑定的ip 默认是localhost
  bindIp: localhost,192.168.0.2
  # bindIp
  # 绑定端口， 默认是 27017
  port: 27017
```

