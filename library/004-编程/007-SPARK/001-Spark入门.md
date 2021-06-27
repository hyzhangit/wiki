
新事物的诞生必然是因为不满于现状，在学习Spark之前先了解一下Hadoop，将会有助于我们了解Spark的起源和要解决的问题。

[Hadoop入门](index.html?file=004-编程/006-Hadoop/001-Hadoop入门)

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

### DAG


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


### Partition

#### HashPartitioner 


#### RangePartitioner




### RDD血缘关系 & 宽窄依赖

RDD 的最重要的特性之一就是血缘关系（Lineage )，它描述了一个 RDD 是如何从父 RDD 计算得来的。如果某个 RDD 丢失了，则可以根据血缘关系，从父 RDD 计算得来。

当进行Action操作时，Spark 才会根据 RDD 的依赖关系生成 DAG，并从起点开始真正的计算。

> 根据不同的转换操作，RDD 血缘关系的依赖分为窄依赖和宽依赖。

> 窄依赖：父 RDD 的每个分区只被子 RDD 的一个分区所依赖--分区收敛, 一对一 && 一对固定(对父RDD依赖的分区不会随着数据规模的改变而改变)。

> 宽依赖：父 RDD 的每个分区都被多个子 RDD 的分区所依赖--分区发散，多对一 && 多对多。

1. 窄依赖

1）子 RDD 的每个分区依赖于常数个父分区（即与数据规模无关)。

2）输入输出一对一的算子，且结果 RDD 的分区结构不变，如 map、flatMap。

3）输入输出一对一的算子，但结果 RDD 的分区结构发生了变化，如 union。

4）从输入中选择部分元素的算子，如 filter、distinct、subtract、sample。

2. 宽依赖

1）子 RDD 的每个分区依赖于所有父 RDD 分区。

2）对单个 RDD 基于 Key 进行重组和 reduce，如 groupByKey、reduceByKey。

3）对两个 RDD 基于 Key 进行 join 和重组，如 join。


join 操作有两种情况，如果 join 操作中使用的每个 Partition 仅仅和固定个 Partition 进行 join，则该 join 操作是窄依赖，其他情况下的 join 操作是宽依赖。

Spark 的这种依赖关系设计，使其具有了天生的容错性，大大加快了 Spark 的执行速度。RDD 通过血缘关系记住了它是如何从其他 RDD 中演变过来的。当这个 RDD 的部分分区数据丢失时，它可以通过血缘关系获取足够的信息来重新运算和恢复丢失的数据分区，从而带来性能的提升。

相对而言，窄依赖的失败恢复更为高效，它只需要根据父 RDD 分区重新计算丢失的分区即可，而不需要重新计算父 RDD 的所有分区。而对于宽依赖来讲，单个结点失效，即使只是 RDD 的一个分区失效，也需要重新计算父 RDD 的所有分区，开销较大。

宽依赖操作就像是将父 RDD 中所有分区的记录进行了“洗牌”（Shuffle），数据被打散，然后在子 RDD 中进行重组。


### Shuffle


### Job && Task

### Master & Worker

### Driver & Executor

### Spark算子

Spark算子可以分为Transformation算子和Action算子两类：

1. Transformation--延迟计算，有Action才会触发
2. Action：立即计算，触发 SparkContext 提交 Job 作业

[Spark常用算子](https://blog.csdn.net/qq_32595075/article/details/79918644)

## 拓展

[Spark生态系统](https://blog.csdn.net/broadview2006/article/details/80127731)



# 其他

## CAP

CAP原则又称CAP定理，指的是在一个分布式系统中，一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）。CAP 原则指的是，这三个要素最多只能同时实现两点，不可能三者兼顾。

