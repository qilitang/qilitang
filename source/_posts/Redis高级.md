---
title: Redis高级
date: 2019-08-02
comments: false
toc: true
categories: "Redis"
tags: [redis]
---







## 1 Redis 的持久化

###  1.1 什么是持久化

持久化就是讲数据保存到磁盘中，机器宕机或者重启数据不丢失， 因为储存在内存中的数据会丢失。

![image-20200422193922463](https://gitee.com/qilitang/Img/raw/master/img/image-20200422193922463.png)

### 1.2 RDB

![image-20200422194135169](https://gitee.com/qilitang/Img/raw/master/img/image-20200422194135169.png)

#### 1.2.1 配置

##### 1.2.1.1、持久化条件配置

![image-20200422194353876](https://gitee.com/qilitang/Img/raw/master/img/image-20200422194353876.png)

![image-20200422194409637](https://gitee.com/qilitang/Img/raw/master/img/image-20200422194409637.png)



##### 1.2.1.2、 指定快照文件

当持久化的任意条件出发， 就会在配置文件指定位置生成一个 后缀为 .rdb 的文件， 里面保存所有的数据，。 因此也称之为快照

![image-20200422194613575](https://gitee.com/qilitang/Img/raw/master/img/image-20200422194613575.png)

![image-20200422194641557](https://gitee.com/qilitang/Img/raw/master/img/image-20200422194641557.png)

#### 1.2.2 创建快照

##### 1.2.2.1、快照创建的过程

![image-20200422195008447](https://gitee.com/qilitang/Img/raw/master/img/image-20200422195008447.png)

##### 1.2.2.2 、手动创建快照

如果没有出发自动快照的创建， 可以使用save 和 bgsave 手动创建快照

![image-20200422195252835](https://gitee.com/qilitang/Img/raw/master/img/image-20200422195252835.png)

在redis 高级版本中 一旦服务重启， 会自动做 RDB

##### 1.2.2.3 、 快照注意事项

![image-20200422195621959](https://gitee.com/qilitang/Img/raw/master/img/image-20200422195621959.png)

##### 1.2.2.4 、存储文件压缩

![image-20200422195816559](https://gitee.com/qilitang/Img/raw/master/img/image-20200422195816559.png)

压缩与不压缩的优缺点：

​	压缩：

​			优点：减少磁盘的储存空间使用

​			缺点：消耗cpu资源

​	不压缩：

​			优点： 不消耗cpu资源

​			缺点:    占用磁盘空间多

如何选择需要看需求和服务器资源情况。

redis 默认是开启压缩的

#### 1.2.3 快照的恢复

![image-20200422200056126](https://gitee.com/qilitang/Img/raw/master/img/image-20200422200056126.png)

### 1.3 AOF

#### 1.3.1 、开启AOF

![image-20200422200336795](https://gitee.com/qilitang/Img/raw/master/img/image-20200422200336795.png)

指定aof文件的名字：

![image-20200422200444500](https://gitee.com/qilitang/Img/raw/master/img/image-20200422200444500.png)

#### 1.3.2、 AOF的工作方式

![image-20200422200604647](https://gitee.com/qilitang/Img/raw/master/img/image-20200422200604647.png)

#### 1.3.3、 测试AOF

当客户端向服务器发送redis 命令时， redis会将所执行的命令记录到aof文件中

![image-20200422200854795](https://gitee.com/qilitang/Img/raw/master/img/image-20200422200854795.png)

当 redis 服务器重启后， 会将执行该aof文件， 达到数据恢复的目的

#### 1.3.4、AOF 文件的重写

在以上的案例中， 有两次对age 进行操作，都记录在了AOF文件中， 其实对于一个变量的多次操作， 只需要记录一次即可。

AOF 重写机制会自动对AOF 文件进行优化， 当文件过大时， 就会把文件中多余（对一个key的重复操作）命令移除，从而保证文件体积。 自动重写的规则， 可以在配置文件中设置。

![image-20200422203953900](https://gitee.com/qilitang/Img/raw/master/img/image-20200422203953900.png)

手动开启重启

![image-20200422204053453](https://gitee.com/qilitang/Img/raw/master/img/image-20200422204053453.png)

目的： 去除数据的中间执行过程， 保留组一种数据命令即可。

#### 1.3.5、文件同步策略

![image-20200422225216429](https://gitee.com/qilitang/Img/raw/master/img/image-20200422225216429.png)



注意： 即使每秒做文件同步也可能导致数据丢失。

## 2 Redis 主从复制(读写分离)

主从复制的好处有2点：

- 避免redis单点故障
- 构建读写分离架构， 满足读多写少的应用场景

### 2.1 主从架构

![image-20200422230519833](https://gitee.com/qilitang/Img/raw/master/img/image-20200422230519833.png)

#### 2.1.1 、 启动实例

redis 是单进程应用，所以一台机器可以启动多个redis实例， 只需要启动时指定不同的配置文件即可

```python
# 注意
# 更改配置文件中的 port 和 pidfile /var/run/redis_6379.pid  文件
# 端口号要不一样，  pid 文件要不一样 避免冲突
```

#### 2.1.2 设置主从

-  设置主从的两种方式

  1、 在redis.conf 中设置 slaveof 配置

  ​	a)  slaveof <masterip><masterport>

  2、 使用 redis-cli 客户端连接到 redis 服务， 执行 slaveof 命令

  ​	a) slaveof <masterip><masterport>

第二种方式在重启后将失去主从复制关系。

```python
# 在 redis-cli 客户端连接后 使用
# INFO replication  查看redis数据库的信息
# Replication
role:master
connected_slaves:0
master_replid:efe95faf98856187743d45c516050b70cbbef92b
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

```

#### 2.1.3 从库设置可写（一般用不到）

**注意** 

读写分离中， 主库可读写，  从库 只能读。

如果有需求， 从库可以配置可写: 

```python
slave-read-only no  
```

### 2.2 主从复制的过程和原理

1. 当从库和主库建立ms关系后， 从库会向主数据库发送PSYNC 命令；
2. 主库接收到PSYNC 命令后 开始在后台保存快照（RDB持久化过程）， 并将期间接收到的写命令缓存起来；
3. 当快照完成后， 主redis 会将快照文件和所有缓存的写命令发送给从redis
4. 从redis 接收到后， 会载入快照文件并且执行收到的缓存命令；
5. 之后， 主redis 每当接收到写命令时， 就会将命令发送给从redis， 从而保证数据的一致

**一旦玩集群， 主就无需设置RDB或AOF了**

问题：

​	第一次需要执行RDB持久化， 会经过多次硬盘IO, 网络IO 所以 效率很低， 由此， 会有下面这个方式。

### 2.3 无磁盘复制

原理：

​	redis 在与从数据库进行复制初始化时 不再将快照存储到磁盘， 而是直接通过网络发送给从数据库，避免io性能差的问题

​	redis 在 2.8.18 版本开始实现了无磁盘复制功能 

```bash
# 开启无磁盘复制：
repl-diskless-sync yes
```

### 2.4、从库宕机

- 从库需要做AOF 防止宕机后 全量的读主库
- 从库有偏移量  master_rep1_offset:  100  
- 在重新建立关系后， 从库发送的PSYNC命令会携带以上参数，  主库会进行增量复制

### 2.5、主库宕机

相对较复杂

1. 在从数据库中执行 slaveof no one 命令， 断开主从关系并提升为主库继续服务
2. 主库重新启动后， 执行 slaveof 命苦， 讲其设置为其他库的从库， 这时的数据就能更新回来。

**问题**

主库宕机， 很麻烦， 所以 提供了 哨兵功能！

## 3 哨兵

### 3.1、 什么是哨兵

哨兵的作用就是对Redis的系统的运行情况进行监控， 它是一个独立的进程， 有2个功能：

1. 监控主从数据库是否运行征程
2. 主数据库出现故障后， 自动将从数据库转化为主数据库；

### 3.2、哨兵原理

- 单哨兵架构

  ![image-20200422235404953](https://gitee.com/qilitang/Img/raw/master/img/image-20200422235404953.png)

- 多哨兵架构

  ![image-20200422235432920](https://gitee.com/qilitang/Img/raw/master/img/image-20200422235432920.png)

多个哨兵， 不仅能同时监控主从数据库， 而且哨兵之间互为监控。

### 3.3、 配置哨兵

#### 3.3.1、配置文件

 redis 安装后在安装目录下会有 哨兵的配置文件， sentinel.conf

在配置文件中添加配置  sentinel monitor myredis 127.0.0.1 6379 1

说明：

​	myredis： 监控主数据库的名称， 自定义即可， 

​	127.0.0.1： 监控主数据库的ip

​	6379 ： 监控主数据库的端口

​	1： 最低通过票数

#### 3.3.2 、启动哨兵进程

redis-sentinel ./sentinel.conf

![image-20200423000337729](https://gitee.com/qilitang/Img/raw/master/img/image-20200423000337729.png)

由上图可以看到：

1. 哨兵已经启动，并且id为：905.,...
2.  为master数据库添加了一个监控
3. 发现2个slave （由此可以看出， 哨兵无需配置slave，只需要指定master， 哨兵会自动发现slave）

3.3.3、 主库宕机 哨兵控制台打印信息如下

![image-20200423000712562](https://gitee.com/qilitang/Img/raw/master/img/image-20200423000712562.png)



#### 3.3.4、多哨兵

使用哨兵文件启动多个哨兵。

## 4 分片集群

即使有了主从复制, 每个数据都要保存整个集群中的数据, 容易形成木桶效应( 就是, 当数据量超过一条redis 服务器的内存大小的时候,  redis受制于服务器的性能, 无法实现水平扩展, 这时候就需要用到集群)

以前使用的是jedis实现分片集群, 游客户端控制那些key数据保存到那个数据中, 如果在水平扩容时就必须手动进行数据迁移. 需要将整个集群服务停止, 这样做非常不好

在redis3.0 版本一大特性就是集群(Cluster)

### 4.1、 架构

![image-20200423145413957](https://gitee.com/qilitang/Img/raw/master/img/image-20200423145413957.png)

1. 所有的redis节点彼此互联（PING-PONG机制）， 内部使用二进制协议优化传输和宽带
2. 节点的fail是通过集群中超过半数点的失效才失效（确认节点是否宕机）
3. 客户端直接与 redis节点直连， 不需要中间proxy层， 客户端不需要连接集群中所有的节点， 连接集群中一个可用节点即可
4. redis-cluster 把所有的物理节点映射到[0-16383]slot（插槽）上， cluster 负责维护 node<->slot<->value

### 4.2 、 集群配置

配置集群要将原所有的dump文件或者 aof 文件 删除

**配置**

进入3个redis目录， 分别修改 redis.conf配置

1. 设置不同的端口， 6379、6380、6381
2. 开启集群， cluster-enable yes
3. 指定集群配置文件 ， cluster-config-file "nodes-xxxx.comf"
4. 启动每个redis

![image-20200423185117383](https://gitee.com/qilitang/Img/raw/master/img/image-20200423185117383.png)

### 4.3、创建集群

#### 4.3.1、 安装ruby 环境

需要使用 redis-trib.rb 脚本文件创建集群， 因为脚本是使用ruby编写， 所有需要安装ruby环境

```bash
yum -y install zlib ruby rubygems
gem install redis
```

手动安装：

```bash
yum -y install zlib ruby rubygems
rz 上传 redis-3.2.1.gem
gem install -l redis-3.2.1.gem
```

#### 4.3.2、创建集群

使用脚本创建集群

![image-20200423185830218](https://gitee.com/qilitang/Img/raw/master/img/image-20200423185830218.png)



![image-20200423185912769](https://gitee.com/qilitang/Img/raw/master/img/image-20200423185912769.png)

```bash
cluster nodes

# 查看集群
```

4.3.3、 测试

![image-20200423190533122](https://gitee.com/qilitang/Img/raw/master/img/image-20200423190533122.png)

为什么会出现  (error) MOVED 7638 127.0.0.1：6380 ？

因为 abs 的hash槽位信息是在 6380 上， 现在使用redis-cli链接的 6379 无法完成set操作， 需要客户端追踪冲顶先

![image-20200423191041651](https://gitee.com/qilitang/Img/raw/master/img/image-20200423191041651.png)

**解决办法**

```bash
#  使用redis-cli -p 6379 是单点玩法
#  使用redis-cli -p 6379 -c    集群玩法
```



### 4.4、 插槽的分配

通过 cluster nodes 命令可以查看当前集群信息：

![image-20200423191348362](https://gitee.com/qilitang/Img/raw/master/img/image-20200423191348362.png)

该信息反映出了集群中的每个节点的  id、 身份、 连接数、插槽数

当我们执行 set abc 123 命令时， redis 是如何将数据保存到集群中的呢？

1. 接收命令 set abc 123
2. 通过key（abc） 计算出插槽值， 然后根据插槽值找到对应的节点。（abc的插槽值为：7638）
3. 冲顶下载那个到该节点执行命令

整个Redis 提供了 16384 个插槽， 也就是说 集群中的每个节点分得的插槽数总和为16384

./redis-trib.rb  脚本实现了是讲16384个插槽平均分配给了N 个节点

注意： 如果插槽数有部分是没有指定到节点的。 name这部分插槽所对应的key 将不能使用

### 4.5、 插槽和key的关系

计算key的插槽值：

key的**有效部分**使用CRC16算法计算出哈希值， 再讲哈希值对16384取余，得到插槽值

什么是有效部分：

1. 如果key中包含了 `{` 且在`{`之后存在`}` 并且 `{}` 之间至少有一个字符， 则有效部分是指`{}`之间的部分

   a) key={hello}_taobao  则有效部分是 hello

2. 如果不满足上一条情况， 整个key都是有效部分；

   a) key=hello_taobao  则有效部分是 hello

应用场景

用有效key 将同类的数据 存储到同一台机器上， 增加速度

### 4.6、新增集群节点

再开启一天集群redis实例。

#### 4.6.1、 添加节点

执行 脚本

```bash
./redis-trib.rb add-node 192.168.56.102:6382 192.168.56.102:6379
```

![image-20200423195444680](https://gitee.com/qilitang/Img/raw/master/img/image-20200423195444680.png)



 查看集群信息

![image-20200423195540161](https://gitee.com/qilitang/Img/raw/master/img/image-20200423195540161.png)

此时虽然添加进集群， 但是此节点不工作 是个看戏的， 因为没有分配 slot  槽点

#### 4.6.2、 分配插槽

没有插槽数

接下来给6382 分配插槽， 将6379 的一部分（1000）个分配给6382

![image-20200423195758141](https://gitee.com/qilitang/Img/raw/master/img/image-20200423195758141.png)

![image-20200423195818586](https://gitee.com/qilitang/Img/raw/master/img/image-20200423195818586.png)

查看节点情况

![image-20200423195850118](https://gitee.com/qilitang/Img/raw/master/img/image-20200423195850118.png)

总结步骤：

1. 启动 一个新的redis服务
2. 运行角门 添加实例到集群中， 使用 redis-trib.rb  add-node
3. 重新分配槽位， 使用 redis-trib.rb reshard

### 4.7、 删除集群节点

要删除集群节点中的某一个节点， 要严格执行2步

1. 将 这个基点的所有插槽专业到其他节点上；

2. 执行脚本 ：./redis-trib.rb 192.168.56.102.6380

3. 选择需要转移的插槽数量， 讲所有插槽转移 redis-trib.rb reshard  

4. 使用 redis.-trib.rb 删除节点

   a) ./redis-trib.rb del-node 192.168.56.102:6380 + id

   b) del-node host:port node_id

### 4.8、 故障转移

当集群中一个节点的 宕机， 则整个集群瘫痪

![image-20200423220936447](https://gitee.com/qilitang/Img/raw/master/img/image-20200423220936447.png)

![image-20200423220949839](https://gitee.com/qilitang/Img/raw/master/img/image-20200423220949839.png)

#### 4.8.1、故障机制

1. 集群中的每个节点都会定期向其他的节点发送PING 命令， 并且通过有没有收到回复判断目标节点是否下线；

2. 集群中每一秒就会随机选择5个节点，然后选择其中最久没有响应的节点放PING 命令；

3. 如果一定时间内目标节点没有响应， 那么该节点就认为目标节点疑似下线；

4. 当集群中的节点超过半数认为该目标节点疑似下线， 那么该节点就会被标记为下线（fail）；

5. 当集群中的任何一个节点下线，就会导致插槽区空档， 不完整，那么该集群不可用

6. 如何解决上述问题？

   a) 在redis集群中科院使用主从模式实现一个节点的高可用

   b) 当该节点（master）宕机后， 集群将改节点的从数据库（slave）转变为（master）继续完成集群服务

#### 4.8.2、集群中的主从复制架构

![image-20200423221059221](https://gitee.com/qilitang/Img/raw/master/img/image-20200423221059221.png)

![image-20200423221146076](https://gitee.com/qilitang/Img/raw/master/img/image-20200423221146076.png)

#### 4.8.3、 创建主从集群

 ![image-20200423222252554](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222252554.png)

![image-20200423222320496](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222320496.png)

![image-20200423222338721](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222338721.png)

![image-20200423222353322](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222353322.png)

![image-20200423222412844](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222412844.png)

#### 4.8.4、 测试从宕机

![image-20200423222537580](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222537580.png)

不会影响集群的工作

#### 4.8.4、 测试主宕机

![image-20200423222639595](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222639595.png)

![image-20200423222722319](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222722319.png)

![image-20200423222756650](https://gitee.com/qilitang/Img/raw/master/img/image-20200423222756650.png)

自动启动  用哨兵

主宕机重启后  自动变成从

### 4.9 使用集群注意事项



1、 多键的命令操作，如（mget， mset） 如果每个键都位于同一个节点， 则可以正常使用， 否则会提示错误

2、 集群中的节点只能使用0号库， 如果执行select 切换数据库则会提示错误。



