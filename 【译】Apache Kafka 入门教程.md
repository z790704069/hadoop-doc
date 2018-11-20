# 介绍
Apache Kafka 是一个分布式数据流平台，这究竟意味着什么呢？
一个数据流平台拥有三个关键能力：
* 发布和订阅记录流，类似消息队列或者企业消息系统。
* 以容错的持久方式存储记录流。
* 新数据产生时及时得到处理。

Kafka 主要用于两大类应用：
* 构建可在系统或应用程序之间可靠获取数据的实时流数据管道
* 构建转换和应对数据流的实时流应用程序

要了解Kafka如何做这些事情，让我们深入探讨Kafka的能力。

首先，有一些概念需要了解：
* Kafka 以集群的形式运行在一台或多态服务器上
* 数据按照类别存储在 Kafka 集群中，我们称这个类别为 topic
* 每条记录包含一个 key，一个 value 以及一个 timestamp

Kafka 有四个核心 API:
* [Producer API][1] 允许应用程序发布记录流到一个或多个 topic 中
* [Consumer API][2] 允许应用程序从一个或多个 topic 订阅以及消费数据
* [Streams API][3] 能让应用程序表现地像一个数据流的处理者：从一个或多个 topic 中消费数据；生产数据到一个或多个 topic 中；将输入流有效地转换成输出流。
* [Connector API][4]

[1]: http://kafka.apache.org/documentation.html#producerapi
[2]: http://kafka.apache.org/documentation.html#consumerapi
[3]: http://kafka.apache.org/documentation/streams
[4]: http://kafka.apache.org/documentation.html#connect

