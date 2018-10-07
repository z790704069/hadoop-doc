# 为什么使用 Strom 
Apache Storm 是一个免费开源的分布式实时计算系统。Storm 可以轻松处理大量的流数据。Storm 很简单，可以使用多种编程语言来使用它。

Storm 有很多使用场景：实时分析，在线机器学习，连续计算，分布式RPC，ETL 等等。Storm 很高效，每个节点每秒支持超过一百万 tuples 的计算。并且 Storm 可扩展、容错性高，能够保证你的数据都能得到运算。

Storm 整合了你熟悉的队列以及数据库。Storm topology 消费数据并以任意复杂的方式处理这些数据流。

# 概念
## 拓扑（Topologies）
实时应用程序的逻辑被打包到Storm拓扑中。Storm 拓扑类似于一个 MapReduce job。

