
新事物的诞生必然是因为不满于现状，在学习Spark之前先了解一下Hadoop，将会有助于我们了解Spark的起源和要解决的问题。

[Hadoop入门](index.html?file=003-编程/021-大数据/001-Hadoop/001-Hadoop入门)

# Spark

Apache Spark 是专为大规模数据处理而设计的快速通用的计算引擎。Spark是UC Berkeley AMP lab (加州大学伯克利分校的AMP实验室)所开源的类Hadoop MapReduce的通用并行框架，Spark，拥有Hadoop MapReduce所具有的优点；但**不同于MapReduce的是——Job中间输出结果可以保存在内存中，从而不再需要读写HDFS**，因此Spark能更好地适用于数据挖掘与机器学习等需要迭代的MapReduce的算法。

Spark 是一种与 Hadoop 相似的开源集群计算环境，但是两者之间还存在一些不同之处，这些有用的不同之处使 Spark 在某些工作负载方面表现得更加优越，换句话说，Spark启用了内存分布数据集，除了能够提供交互式查询外，它还可以优化迭代工作负载。

Spark 是在 Scala 语言中实现的，它将 Scala 用作其应用程序框架。与 Hadoop 不同，Spark 和 Scala 能够紧密集成，其中的 Scala 可以像操作本地集合对象一样轻松地操作分布式数据集。

## 特点

1. 易用性--提供80+高级运算符和高级API，开发者可以专注于应用所要做的计算本身
2. 通用性--SQL查询、文本处理、机器学习...在 Spark出现之前，一般需要学习各种引擎来分别处理这些需求
3. 高性能--内存计算，支持交互式计算和复杂算法，Spark 比 Hadoop 快100倍
4. 生态系统--Spark Core、Spark SQL、Spark Streaming、MLlib、GraphX...以及Java生态 

## 概念

### Application

Spark应用程序，包含一个Driver和若干个Executor。
### SparkContext

Spark应用上下文，其代表与spark集群的连接，能够用来在集群上创建RDD、累加器、广播变量。每个JVM里只能存在一个处于激活状态的SparkContext，在创建新的SparkContext之前必须调用stop()来关闭之前的SparkContext。

每一个Spark应用都是一个SparkContext实例，可以理解为一个SparkContext就是一个spark application的生命周期，一旦SparkContext创建之后，就可以用这个SparkContext来创建RDD、累加器、广播变量，并且可以通过SparkContext访问Spark的服务，运行任务。spark context设置内部服务，并建立与spark执行环境的连接。

Spark架构组成图

![](assets/003/021/002/001-1624811053934.png)

SparkContext组成

![](assets/003/021/002/001-1624811088538.png)

通过这两张图可以发现，SparkContext在spark应用中起到了master的作用，掌控了所有Spark的生命活动，统筹全局，除了具体的任务在executor中执行，其他的任务调度、提交、监控、RDD管理等关键活动均由SparkContext主体来完成。

### Master  & Worker

搭建spark集群的时候我们就已经设置好了master节点和worker节点，一个集群有多个master节点和多个worker节点。

master节点常驻master守护进程，负责管理worker节点，我们从master节点提交应用。

worker节点常驻worker守护进程，与master节点通信，并且管理executor进程。

PS：一台机器可以同时作为master和worker节点
### Driver & Executor

Driver和Executor是进程，在程序运行时候，向Master请求资源。

### Driver

Driver: Driver进程就是应用的main()函数并且构建sparkContext对象，当我们提交了应用之后，便会启动一个对应的driver进程。

Driver可以运行在master上，也可以运行worker上（根据部署模式的不同）。Driver首先会向集群管理者（standalone、yarn，mesos）申请spark应用所需的资源，也就是executor，然后集群管理者会根据spark应用所设置的参数在各个worker上分配一定数量的executor，每个executor都占用一定数量的cpu和memory。在申请到应用所需的资源以后，Driver就开始调度和执行我们编写的应用代码了。Driver进程会将我们编写的spark应用代码拆分成多个stage，每个stage执行一部分代码片段，并为每个stage创建一批tasks，然后将这些tasks分配到各个executor中执行。


Executor：Executor进程位于worker节点上，一个worker可以有多个executor。每个executor持有一个线程池，每个线程可以执行一个task，executor执行完task以后将结果返回给driver，每个executor执行的task都属于同一个应用。此外executor还有一个功能就是为应用程序中要求缓存的 RDD 提供内存式存储，RDD 是直接缓存在executor进程内的，因此任务可以在运行时充分利用缓存数据加速运算。


### ApplicationMaster

YarnCluster模式下，Driver的启动类, 由其启动Driver线程。

YarnClient模式下，Driver在客户端。

### DAGScheduler

Spark内置的DAG生成器和调度器，根据Job构建基于Stage的DAG，并将Stage中的TaskSet提交给TaskScheduler执行。
每个TaskSet任务集中的任务是相互独立的，这些任务可以基于集群上的已有数据直接运行。
### TaskSchedule

将Stage中的TaskSet提交给WorkNode集群并返回结果。
### RDD

Spark的核心是建立RDD之上的，通过对RDD进行一系列操作类完成大数据处理。

RDD（Resiliennt Distributed Datasets）弹性分布式数据集。

1. 弹性--内存磁盘自动切换
2. 分布式--集群运行
3. 数据集--**移动数据不如移动计算**, RDD不存储数据, 只存储数据引用和计算函数 

特点：
1. 只读--数据稳定
2. 分区--并行计算
3. 进度--血缘关系

[RDD概念介绍](https://zhuanlan.zhihu.com/p/68028744)

[Spark RDD是什么](https://blog.csdn.net/dsdaasaaa/article/details/94181269)

### DAG

DAG(Direct Acyclic Graph)有向无环图，由顶点和有向边构成的一种图状结构，是一个没有有向循环的、有限的有向图。用于表达Spark任务的流程图。

![](assets/003/021/002/001-1624807835142.png)

### Partition


#### HashPartitioner 


#### RangePartitioner




### RDD血缘关系 & 宽窄依赖

RDD 的最重要的特性之一就是血缘关系（Lineage )，它描述了一个 RDD 是如何从父 RDD 计算得来的。如果某个 RDD 丢失了，则可以根据血缘关系，从父 RDD 计算得来。

当进行Action操作时，Spark 才会根据 RDD 的依赖关系生成 DAG，并从起点开始真正的计算。

根据不同的转换操作，RDD 血缘关系的依赖分为窄依赖和宽依赖。

> 窄依赖：父 RDD 的每个分区只被子 RDD 的一个分区所依赖--分区收敛, 一对一 && 一对固定(对父RDD依赖的分区不随着数据规模的改变而改变)。

> 宽依赖：父 RDD 的每个分区都被多个子 RDD 的分区所依赖--分区发散，多对一 && 多对多。

![](assets/003/021/002/001-1624808381273.png)

**窄依赖**

1）子 RDD 的每个分区依赖于常数个父分区（即与数据规模无关)。

2）输入输出一对一的算子，且结果 RDD 的分区结构不变，如 map、flatMap。

3）输入输出一对一的算子，但结果 RDD 的分区结构发生了变化，如 union。

4）从输入中选择部分元素的算子，如 filter、distinct、subtract、sample。

**宽依赖**

1）子 RDD 的每个分区依赖于所有父 RDD 分区。

2）对单个 RDD 基于 Key 进行重组和 reduce，如 groupByKey、reduceByKey。

3）对两个 RDD 基于 Key 进行 join 和重组，如 join。


join 操作有两种情况，如果 join 操作中使用的每个 Partition 仅仅和固定个 Partition 进行 join，则该 join 操作是窄依赖，其他情况下的 join 操作是宽依赖。

Spark 的这种依赖关系设计，使其具有了天生的容错性，大大加快了 Spark 的执行速度。RDD 通过血缘关系记住了它是如何从其他 RDD 中演变过来的。当这个 RDD 的部分分区数据丢失时，它可以通过血缘关系获取足够的信息来重新运算和恢复丢失的数据分区，从而带来性能的提升。

相对而言，窄依赖的失败恢复更为高效，它只需要根据父 RDD 分区重新计算丢失的分区即可，而不需要重新计算父 RDD 的所有分区。而对于宽依赖来讲，单个结点失效，即使只是 RDD 的一个分区失效，也需要重新计算父 RDD 的所有分区，开销较大。

宽依赖操作就像是将父 RDD 中所有分区的记录进行了“洗牌”，数据被打散，然后在子 RDD 中进行重组。

### Shuffle

Shuffle，字面意思洗牌，在这里可以理解为数据重新分区的过程。窄依赖没有产生分区变化，不会产生shuffle，宽依赖发生了分区变化，会产生shuffle。

shuffle依赖是两个stage的分界点。shuffle操作一般都是任务中最耗时耗资源的部分。因为数据可能存放在HDFS不同的节点上，下一个stage的执行首先要去拉取上一个stage的数据（shuffle read操作），保存在自己的节点上，就会增加网络通信和IO。

### Job && Stage && Task

Job：一个Spark作业，在Action操作后启动

Stage: 一个Job对应一到多个Stage，DAGScheduler根据shuffle依赖对Job进行Stage划分

Task：Spark调度的最小执行单元，一个Stage对应一到多个Task。

下图是一个JOB对应多个Stage，每个Stage又对应一些Task的示意图。

![](assets/003/021/002/001-1624808041649.png)

[Spark Stage的划分](https://blog.csdn.net/zhyooo123/article/details/82703723)

### Spark算子

Spark算子可以分为Transformation算子和Action算子两类：

1. Transformation--延迟计算，有Action才会触发
2. Action：立即计算，触发 SparkContext 提交 Job 作业

[Spark常用算子](https://blog.csdn.net/qq_32595075/article/details/79918644)

## 拓展

[Spark生态系统](https://blog.csdn.net/broadview2006/article/details/80127731)

[Spark入门实战系列--1.Spark及其生态圈简介](https://blog.csdn.net/yirenboy/article/details/47292871)

[Spark入门实战系列--4.Spark运行架构](https://blog.csdn.net/yirenboy/article/details/47441465?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-19.base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-19.base)

[SparkContext详解](https://blog.csdn.net/weixin_43878293/article/details/90020221)

[Spark源码分析之Driver的分配启动和executor的分配启动](https://blog.csdn.net/qq_21050291/article/details/77943339?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-7.base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-7.base)
# 其他

## CAP

CAP原则又称CAP定理，指的是在一个分布式系统中，一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）。CAP 原则指的是，这三个要素最多只能同时实现两点，不可能三者兼顾。

