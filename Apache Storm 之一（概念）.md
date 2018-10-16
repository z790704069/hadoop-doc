
![扫码关注微信公众号"Kooola大数据"][14]
<font color=grey size=1 >扫码关注微信公众号号"Kooola大数据"，一起聊聊大数据</font>

本文列出 Storm 的几个主要概念，并会给出相关资源的链接以便你获取更多信息，概念主要如下：
* Topologies
* Streams
* Spouts
* Bolts
* Stream groupings
* Reliability
* Tasks
* Workers



# 拓扑（Topologies）
实时应用程序的逻辑被打包到 Storm 拓扑中。一个 Storm 拓扑类似于一个 MapReduce 任务。关键的区别在于 MapReduce 任务最终会结束，而拓扑会一直运行（当然，除非你强制 kill 掉拓扑相关的进程）。拓扑可以理解为通过数据流（Stream Grouping）将 Spout 和 Bolt 相互连接而组成的图状结构的程序。spouts 和 bolts 的概念会在下文介绍。

相关资源：
* [TopologyBuilder][1]: 构造 topologies 的 Java 类
* [在生成环境的集群上运行 topologies][2]
* [本地模式][3]: 学习如何在本地模式开发以及测试 topologies

# 流（Streams）
流是 Storm 的核心抽象。Storm中，一个流指的是在分布式环境中被并行创建以及处理的元组（tuple）序列集。流是无限的元组（tuple）序列，以分布式方式并行创建和处理。流往往有固定的模式（我们称之为“fields”），不同模式由不同的元组（tuple）类型以一定的方式组成。通常，元组（tuple）可以包含 integers, longs, shorts, bytes, strings, doubles, floats, booleans, 以及 byte arrays。当然，你也可以通过定义可序列化的对象来实现自定义的元组类型。

相关资源：
* [Tuple][4]: 流由元组组成
* [OutputFieldsDeclarer][5]: 用来指定流以及它的模式
* [Serialization][6]: 元组的动态类型以及自定义序列化

# 数据源（Spouts）
在拓扑中， spout 是流的来源。通常情况，Spouts 会从外部源（例如消息队列或者 Twitter API）读取数据并将数据发送到拓扑中。Spouts 既可以是可靠的，也可以是不可靠的。可靠的情况是如果数据流没有被 Storm 处理，Spouts 将重新发送数据。不可靠的情况则是对发送过的数据不予确认。

Spouts 一次可以发送多个流。为了实现多流发送，我们可以使用（实现）  OutputFieldsDeclarer 接口中的 declareStream 方法来指定多个流，并使用（实现）  SpoutOutputCollector 接口中的 emit 方法进行发送。

nextTuple 是 Spouts 中的主要方法。nextTuple 方法要么发送一个新的元组到 topology 中，要么直接返回（如果没有新的元组需要发送）。需要注意的是，nextTuple 不应该被 Spout 的任何其他方法所阻塞，否则会导致数据流的停止接入，这是因为 Spout 的所有方法是在一个线程中执行。

ack 和 fail 是 Spouts 中另外两个重要的方法。Spouts 为可靠模式时，Storm 会检测每一个从 Spouts 发送出去的元组是否成功，成功调用 ack，失败调用 fail。当然，在不可靠模式下，是不会调用这两个方法的。

相关资源：
* [IRichSpout][7]: 自定义的 spouts 必须实现这个接口
* [消息的可靠性处理][8]

# 处理组件（Bolts）
topologies 所有的处理都是在 bolts 中进行。bolts 可以做很多事情，例如：过滤流、逻辑处理、聚合、连接、数据库交互等等。

bolts 可以从事简单的数据流转换。处理复杂的数据流转换通常需要将流程分成多步，这也就意味着我们可以使用多类（个） bolt。例如，从微博数据流中得出一个趋势图，实现这个需求我们至少需要两步：第一个 bolt 计算每个图片的点击数，第二个 bolt 在第一个基础上得出 TOP X 的图片（当然为了流程可扩展，我们可以使用更多的 bolt,不仅限于两个）。

bolts 一次可以发送多个流。为了实现多流发送，我们可以使用（实现）  OutputFieldsDeclarer 接口中的 declareStream 方法来指定多个流，并使用（实现）  OutputCollector 接口中的 emit 方法进行发送（跟 spout 类似）。

在定义 Bolt 的输入数据流时，你需要从其他的 Storm 组件中订阅指定的数据流。如果你需要从其他所有的组件中订阅数据流，你就必须要在定义 Bolt 时分别注册每一个组件。对于声明为默认 id 的数据流，[InputDeclarer][9] 接口有订阅此类数据流的语法糖。调用 <font color=orange size=2 >declarer.shuffleGrouping("1") </font> 将订阅来自 id 为“1” 的组件（spout/bolt）产生的数据流，其等价于调用 <font color=orange size=2 >_declarer.shuffleGrouping("1", DEFAULT_STREAM_ID)_</font>

execute 是 bolt 的主要方法，它接收新的元组作为输入。bolt 使用 [OutputCollector][10] 对象来发送新的元组。bolt 必须为每个经由它处理的元组调用 OutputCollector 中的 ack 方法，这样以便 Storm 知道这些元组什么时候被处理完成（最终判断对原始 spout 元组的响应是否合适）。处理元组的一般情况是，我们可以发送多个元组或者直接不发送，然后响应下一个输入元组，我们可以实现 [IBasicBolt][11] 接口来完成 bolt 操作。

我们可以在 bolt 任务中开启一个新的线程来完成异步操作。[OutputCollector][12] 线程安全并且可以随时被调用。

相关资源：
* [IRichBolt][7]: bolt 的主要接口.
* [IBasicBolt][11]: 另外一个简单接口，帮助用实现过滤以及其他功能
* [OutputCollector][12]: 使用这个类来发送元组到输出流中
* [消息的可靠性处理][8]


# 流分组（Stream groupings）
定义一个 topology 的重要一部分是指定每个 bolt 应该接收哪些流作为输入。流分组（stream grouping）定义了流如何分发到各个 bolt 中。

Storm 提供了 8 种流分组策略。当然，你也可以通过实现 CustomStreamGrouping 接口来实现一个用户自定义的流分组：
* **Shuffle grouping** : 元组被随机分发到各个 bolt 任务中，也就是说每个 bolt 接收到大致相同数目的元组。
* **Fields grouping** : 根据指定的 field 进行分组 ，同一个 field 的值一定会被发送到同一个 task 上。例如，如果流按照 "user-id" 这个 field 进行分组，那么相同的 "user-id" 值会进入相同的任务（task），如果不同，则进入不同的任务。
* **Partial Key grouping** : 与 Fields grouping 类似，根据指定的 field 的一部分进行分组分发，能够很好地实现 load balance，将元组发送给下游的 bolt 对应的 task，特别是在存在数据倾斜的场景，使用 Partial Key grouping 能够更好地提高资源利用率
* **All grouping** : 流复制到所有 bolt task 上。
* **Global grouping**: 所有的流都指向一个 bolt 的同一个 task，也就是Task ID最小的。
* **None grouping** : 使用这个分组，用户不用关心流是如何进行分组的。目前，这个分组类似于 Shuffle grouping。不过未来 Storm 可能会考虑通过这种分组来让 Bolt 和它所订阅的 Spout 或 Bolt 在同一个线程中执行。 
* **Direct grouping** : 由 tupe 的生产者来决定发送给下游的哪一个 bolt 的 task ，这个要在实际开发编写 bolt 代码的逻辑中进行精确控制。
* **Local or shuffle grouping** : 如果目标 bolt 有1个或多个 task 都在同一个 worker 进程对应的 JVM 实例中，则 tuple 只发送给这些 task。

# 可靠性（Reliability）
Storm 保证每个 spout 元组都能在拓扑中被处理。通过跟踪由 Spout 发出的每个元组构成的元组树可以确定元组是否已经完成处理。每个拓扑都有与之相关的消息超时。如果在超时时间内没有检测到元组是否被完整处理，该原则将会被标记并重新发送。

想要使用 Storm 这个可靠性功能，你必须在元组创建以及处理完成时告诉 Storm。你可以使用用于发送数据流的 OutputCollector  对象，并使用 ack 方法表明你已经完成了元组的处理。

# 任务（Tasks）
集群中，每一个 spout 和 bolt 运行了多个任务。每个任务对应一个执行线程，流分组定义如何将元组从一组任务发送到另一组任务。你可以使用 TopologyBuilder 中的 setSpout 和 setBolt 方法来设置任务并行度。

# Workers
一个拓扑中运行了一个或多个 worker 进程。每个进程都是一个物理 JVM，并且拓扑中的所有 task 都在这些进程中执行。例如，如果并行度为 300，我们有 50 个worker 进程，那么每个进程将处理 6 个 task。Storm 有其机制致力于将所有任务尽量平均地分配到每个进程中。

相关资源：
[Config.TOPOLOGY_WORKERS][13]: 设置 worker 数量的配置

[1]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/topology/TopologyBuilder.html
[2]: http://storm.apache.org/releases/1.0.6/Running-topologies-on-a-production-cluster.html
[3]: http://storm.apache.org/releases/1.0.6/Local-mode.html
[4]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/tuple/Tuple.html
[5]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/topology/OutputFieldsDeclarer.html
[6]: http://storm.apache.org/releases/1.0.6/Serialization.html
[7]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/topology/IRichSpout.html
[8]: http://storm.apache.org/releases/1.0.6/Guaranteeing-message-processing.html
[9]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/topology/InputDeclarer.html
[10]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/task/OutputCollector.html
[11]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/topology/IBasicBolt.html
[12]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/task/OutputCollector.html
[13]: http://storm.apache.org/releases/1.0.6/javadocs/org/apache/storm/Config.html#TOPOLOGY_WORKERS
[14]: https://www.kooola.com/upload/2018/10/ofjvdo746uj67pmdue0iho59c5.png


