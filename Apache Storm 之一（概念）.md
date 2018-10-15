本文列出 Storm 的几个主要概念，并会给出相关资源的链接以便你获取更多信息，主要如下：
* Topologies
* Streams
* Spouts
* Bolts
* Stream groupings
* Reliability
* Tasks
* Workers



# 拓扑（Topologies）
实时应用程序的逻辑被打包到Storm拓扑中。Storm 拓扑类似于一个 MapReduce 任务。一个关键的区别是 MapReduce 任务会最终会结束，但是 topology 会一直运行（当然，除非你强制 kill 掉进程）。拓扑可以理解为通过数据流（Stream Grouping）将 Spout 和 Bolt 相互连接而组成的图庄结构。spouts 和 bolts 的概念会在下文介绍。

相关资料：
* [TopologyBuilder][1]: 构造 topologies 的 Java 类
* [在生成环境的集群上运行 topologies][2]
* [本地模式][3]: 学习如何在本地模式开发以及测试topologies

# 流（Streams）
流是 Storm 的核心抽象。Storm中，一个流指的是在分布式环境中被并行创建以及处理的元组（tuple）序列集。流是无限的元组（tuple）序列，以分布式方式并行处理和创建。流往往有固定的模式（我们称之为“fields”），不同模式由不同的元组（tuple）以一定的方式组成。通常，元组（tuple）可以包含 integers, longs, shorts, bytes, strings, doubles, floats, booleans, 以及 byte arrays。当然，你也可以通过定义可序列化的对象来实现自定义的元组类型。

相关资料：
* [Tuple][4]: 流由元组组成
* [OutputFieldsDeclarer][5]: 用来指定流以及它的模式
* [Serialization][6]: 元组的动态类型以及自定义序列化

# 数据源（Spouts）
在拓扑中， spout 是流的来源。通常情况，Spouts 会从外部源（例如消息队列或者 Twitter API）读取数据并将数据发送到拓扑中。Spouts 既可以是可靠的，也可以是不可靠的。可靠的情况是如果数据流没有被 Storm 处理，Spouts 将重新发送数据。不可靠的情况则是对发送过的数据不予确认。

Spouts 一次可以发送多个流。为了实现多流发送，我们可以使用（实现）  OutputFieldsDeclarer 接口中的 declareStream 方法来指定多个流，并使用（实现）  SpoutOutputCollector 接口中的 emit 方法进行发送。

nextTuple 是 Spouts 中的主要方法。nextTuple 方法要么发送一个新的元组到 topology 中，要么直接返回（如果没有新的元组需要发送）。需要注意的是，nextTuple 不应该被 Spout 的任何其他方法所阻塞，否则会导致数据流的停止接入，因为 Spout 的所有方法是在一个线程中执行。




[1]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/topology/TopologyBuilder.html
[2]: http://storm.apache.org/releases/1.0.6/Running-topologies-on-a-production-cluster.html
[3]: http://storm.apache.org/releases/1.0.6/Local-mode.html
[4]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/tuple/Tuple.html
[5]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/topology/OutputFieldsDeclarer.html
[6]: http://storm.apache.org/releases/1.0.6/Serialization.html


