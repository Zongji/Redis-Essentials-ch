# Redis集群和Redis Sentinel
## aaa

Redis最初被被设计得非常轻量级、非常快。Redis之前唯一的拓扑结构是master/slave结构，在这种架构中，master接收所有的写请求，并复制变更内容到slave节点，不支持自动故障转移和数据分片。这种架构形式适合以下几种场景：
- master节点有足够的内存保存所有的数据
- 可以添加更多的从节点增加读性能，或者网络带宽成为瓶颈时
- 当维护master节点所在机器时，允许停止Redis应用
- 数据冗余可以只通过slave进行冗余

但是不适合以下其他场景：

## CAP理论
大多数分布式系统的分析都是使用CAP理论，该理论中分布式系统不能同时满足一下几种：
- Consistency 一致性： 读操作会保证能读到足最新的写入
- Availability 可用性：任何操作都会返回操作成功或者失败的结果
- Partition tolerance 分区容忍性： 当网络分区出现时系统可以继续工作


## Redis Sentinel
当主节点出现问题，从某个节点被选取为主节点，其他从节点需要重新配置指向新的主节点。在Sentinel出现之前，这种故障切换处理只能通过手动切换，很不可靠。

当现有的主节点失效的时候，Redis Sentinel自动选取其中一个从节点为主节点。Sentinel不会自动对数据进行分片，因为主节点保存了全部数据，每个从节点都有完整的一份数据拷贝， 也就是说Sentinel不是一个分布式数据存储系统。

最常见的架构模式是每个Redis服务都对应安装一个Sentinel服务。Sentinel是一个独立的进程，监听自己的端口。

> 如果你机器上安装有防火墙，需要允许Sentinel其他的Sentinel进程和Redis进程。

假设部署架构中有1个主节点，2个复制节点， 可以用下图的方式搭建Sentinel。
![](./images/9-1.png)

使用Sentinel的主要的不同点在在于Sentinel实现了不同的接口方式，这需要客户端支持。客户端需要向Sentinel查询自己将要连接到那个Redis实例。

下载的Redis源码包中包含了一份Sentinel配置文件，文件名：sentinel.conf。在初始配置中，只需列出master节点的配置，Sentinel会自动从主节点获取所有的从节点信息。Sentinel一旦发现了所有的从节点或故障转移发生时，就会重写Sentinel配置。

所有的Sentinel之间的通信是通过master节点上的名为 `__sentinel__:hello`的Pub/Sub通道进行的。

### Sentinel基本配置
Sentinel配置包含主节点的IP,端口号，和唯一名称。该名称就是Redis集群的名称。下面的例子中，名称为`mymaster`。
```
sentinel monitor mymaster 127.0.0.1 6379 2
sentinel down-after-milliseconds mymaster 30000
sentinel failover-timeout mymaster 180000
sentinel parallel-syncs mymaster 1
```
该配置指定了Redis主节点的名称mymaster，IP地址127.0.0.1，端口号6379，quorum值为2。 quorum表示参与同意认为主节点失效的最少的sentinel节点个数，主节点失效后开始进行新的master选举。

master节点宕机（无法响应PING消息）超过指定毫秒数后，Sentinel节点才会通知其他Sentinel节点。这个毫秒数在`down-after-milliseconds`指令中指定。


### 连接到Sentinel
Ruby  官方Redis客户端给出了简单的例子演示如何连接Redis Sentinel。
```ruby
SENTINEL = {
    {:host => "127.0.0.1", :port => 23680},
    {:host => "127.0.0.1", :port => 23681}
}
 redis = Redis.new(:url => "redis://mymaster", :sentinels => SENTINELS, :role => :master)
```
### 网络分区（脑裂）




## Redis集群

## 总结



