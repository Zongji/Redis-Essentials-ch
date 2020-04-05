# Redis 集群和Redis Sentinel(集体智慧)

## CAP理论

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


## Redis集群

## 总结



