
新事物的诞生必然是因为不满于现状，在学习Spark之前先了解一下Hadoop，将会有助于我们了解Spark的起源和要解决的问题。

[Hadoop入门](index.html?file=004-编程/006-Hadoop/001-Hadoop入门)

## Spark

Apache Spark 是专为大规模数据处理而设计的快速通用的计算引擎。Spark是UC Berkeley AMP lab (加州大学伯克利分校的AMP实验室)所开源的类Hadoop MapReduce的通用并行框架，Spark，拥有Hadoop MapReduce所具有的优点；但**不同于MapReduce的是——Job中间输出结果可以保存在内存中，从而不再需要读写HDFS**，因此Spark能更好地适用于数据挖掘与机器学习等需要迭代的MapReduce的算法。

Spark 是一种与 Hadoop 相似的开源集群计算环境，但是两者之间还存在一些不同之处，这些有用的不同之处使 Spark 在某些工作负载方面表现得更加优越，换句话说，Spark启用了内存分布数据集，除了能够提供交互式查询外，它还可以优化迭代工作负载。

Spark 是在 Scala 语言中实现的，它将 Scala 用作其应用程序框架。与 Hadoop 不同，Spark 和 Scala 能够紧密集成，其中的 Scala 可以像操作本地集合对象一样轻松地操作分布式数据集。

### 特点

1. 易用性--提供80+高级运算符和高级API，开发者可以专注于应用所要做的计算本身
2. 通用性--SQL查询、文本处理、机器学习...在 Spark出现之前，一般需要学习各种引擎来分别处理这些需求
3. 高性能--内存计算，支持交互式计算和复杂算法，Spark 比 Hadoop 快100倍
4. 生态系统--Spark Core、Spark SQL、Spark Streaming、MLlib、GraphX...以及Java生态 

### 概念

#### RDD

#### DAG

#### 宽窄依赖

#### Job && Task

#### Master & Worker

#### Driver & Executor

#### Spark算子

Spark算子可以分为Transformation算子和Action算子两类：

1. Transformation--延迟计算，有Action才会触发
2. Action：立即计算，触发 SparkContext 提交 Job 作业

[Spark常用算子](https://blog.csdn.net/qq_32595075/article/details/79918644)

### 拓展

[Spark生态系统](https://blog.csdn.net/broadview2006/article/details/80127731)



## 其他

### CAP

CAP原则又称CAP定理，指的是在一个分布式系统中，一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）。CAP 原则指的是，这三个要素最多只能同时实现两点，不可能三者兼顾。

