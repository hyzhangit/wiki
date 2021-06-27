
# Hadoop

Hadoop是一个由Apache基金会所开发的分布式系统基础架构。用户可以在不了解分布式底层细节的情况下，开发分布式程序。充分利用集群的威力进行高速运算和存储。Hadoop实现了一个分布式文件系统（ Distributed File System），其中一个组件是HDFS（Hadoop Distributed File System）。HDFS有高容错性的特点，并且设计用来部署在低廉的（low-cost）硬件上；而且它提供高吞吐量（high throughput）来访问应用程序的数据，适合那些有着超大数据集（large data set）的应用程序。HDFS放宽了（relax）POSIX的要求，可以以流的形式访问（streaming access）文件系统中的数据。Hadoop的框架最核心的设计就是：HDFS和MapReduce。HDFS为海量的数据提供了存储，而MapReduce则为海量的数据提供了计算。

分布式存储系统HDFS （Hadoop Distributed File System ）

创始人：Doug cutting<br/>
名字由来：Doug cutting的小孩给他的棕黄色大象玩具起的名字<br/>
Logo: 黄色小象<br/>

思想起源：Google三大论文
- [GFS 文件存储](https://blog.csdn.net/OpenNaive/article/details/7483523)
- [Map-Reduce 计算](https://blog.csdn.net/OpenNaive/article/details/7514146)
- [Bigtable 列式存储](https://blog.csdn.net/opennaive/article/details/7532589)

## 特点

1. 可靠性--多副本
2. 性能优--并行计算
3. 拓展性--横向拓展可伸缩
4. 低成本--廉价PC
5. 跨平台--JVM

优点: 高容错、低成本、批处理、高吞吐<br/>
缺点：编程模型复杂，大量IO操作性能受限（为Spark埋下伏笔）<br/>

## 概念

### NameNode

NameNode 是一个通常在 HDFS 实例中的单独机器上运行的软件。它负责管理文件系统名称空间和控制外部客户机的访问。NameNode 决定是否将文件映射到 DataNode 上的复制块上。对于最常见的 3 个复制块，第一个复制块存储在同一机架的不同节点上，最后一个复制块存储在不同机架的某个节点上。

实际的 I/O事务并没有经过 NameNode，只有表示 DataNode 和块的文件映射的元数据经过 NameNode。当外部客户机发送请求要求创建文件时，NameNode 会以块标识和该块的第一个副本的 DataNode IP 地址作为响应。这个 NameNode 还会通知其他将要接收该块的副本的 DataNode。

NameNode 在一个称为 FsImage 的文件中存储所有关于文件系统名称空间的信息。这个文件和一个包含所有事务的记录文件（这里是 EditLog）将存储在 NameNode 的本地文件系统上。FsImage 和 EditLog 文件也需要复制副本，以防文件损坏或 NameNode 系统丢失 。

NameNode本身不可避免地具有SPOF（Single Point Of Failure）单点失效的风险，主备模式并不能解决这个问题，通过Hadoop Non-stop namenode才能实现100% uptime可用时间  。

###  DataNode

DataNode 也是一个通常在 HDFS实例中的单独机器上运行的软件。Hadoop 集群包含一个 NameNode 和大量 DataNode。DataNode 通常以机架的形式组织，机架通过一个交换机将所有系统连接起来。Hadoop 的一个假设是：机架内部节点之间的传输速度快于机架间节点的传输速度 。

DataNode 响应来自 HDFS 客户机的读写请求。它们还响应来自 NameNode 的创建、删除和复制块的命令。NameNode 依赖来自每个 DataNode 的定期心跳（heartbeat）消息。每条消息都包含一个块报告，NameNode 可以根据这个报告验证块映射和其他文件系统元数据。如果 DataNode 不能发送心跳消息，NameNode 将采取修复措施，重新复制在该节点上丢失的块。

## 拓展
[Hadoop系列文章之小象诞生](https://blog.csdn.net/weixin_41634974/article/details/100904671)

[Hadoop简介](https://blog.csdn.net/weixin_41634974/article/details/100904671)