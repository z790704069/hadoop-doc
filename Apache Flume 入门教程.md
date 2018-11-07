# 概要
Apache Flume 是一个分布式，可靠且可用的系统，用于有效地从许多不同的源收集、聚合和移动大量日志数据到一个集中式的数据存储区。

Flume 的使用不只限于日志数据。因为数据源可以定制，flume 可以被用来传输大量事件数据，这些数据不仅仅包括网络通讯数据、社交媒体产生的数据、电子邮件信息等等。

Apache Flume 是 Apache 基金会的顶级项目，在加入 Apache 之前由 cloudera 开发维护。
Apache Flume 目前有两种版本： 0.9.x 和 1.x。 其中 0.9.x 的版本我们称之为 Flume OG（original generation）。2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation），也就是这里的 1.x 版本。

# 架构
## 数据流模型
一个 Flume 时间被定义为一个数据流单元。Flume agent 其实是一个 JVM 进程，该进程中可以包含完成任务所需要的各个组件，比如 Source、Chanel 以及 Slink。
![][1]

**Flume Source** 消费由外部源（如Web服务器）传递给它的事件。外部源以一定的格式发送数据给 Flume，这个格式的定义由目标 Flume Source 来确定。例如，一个 Avro Flume source 可以从 Avro（Avro是一个基于二进制数据传输高性能的中间件，是 hadoop 的一个子项目） 客户端接收 Avro 事件，也可以从其他 Flume agents （该 Flume agents 有 Avro sink）接收 Avro 事件。 同样，我们可以定义一个 Thrift Flume Source 接收来自 Thrift Sink、Flume Thrift RPC 客户端或者其他任意客户端（该客户端可以使用任何语言编写，只要满足 Flume thrift 协议）的事件。

**Flume channel** 可以理解为缓存区，用来保存从 Source 那拿到的数据，直到 Flume slink 将数据消费。file chanel 是一个例子，它将数据保存在文件系统中（当然你可以将数据放在内存中）。

**Flume slink** 从 channel 消费完数据就将数据从 channel 中清除，随后将数据放到外部存储系统例如 HDFS （使用 Flume HDFS sink）或发送到其他 Flume agent 的 source 中。不管是 Source 还是 Slink 都是异步发送和消费数据。

## 复杂的流
Flume 允许用户构建一个复杂的数据流，比如数据流经多个 agent 最终落地。It also allows fan-in and fan-out flows, contextual routing and backup routes (fail-over) for failed hops.

## 可靠性
事件被存储在每个 agent 的 channel 中。随后这些事件会发送到流中的下一个 agent 或者设备存储中（例如 HDFS）。只有事件已经被存储在下一个 agent 的 channel 或设备存储中时，当前 channel 才会清除该事件。这种机制保证了流在端到端的传输中具有可靠性。

Flume使用事务方法（transactional approach）来保证事件的可靠传输。在 source 和 slink 中，事件的存储以及恢复作为事务进行封装，存放事件到 channel 中以及从 channel 中拉取事件均是事务性的。这保证了流中的事件在节点之间传输是可靠的。

## 可恢复
事件在 channel 中进行，该 channel 负责管理事件从故障中恢复。Flume 支持一个由本地文件系统支持的持久化文件（文件模式：channel.type = "file"） channel。同样也只存内存模式（channel.type = "memmory"）,将事件保存在内存队列中。内存模式相对与文件模型性能更好，但是当 agent 进程不幸挂掉时，存储在 channel 中的事件将丢失，无法进行恢复。

# 构建

## 构建一个 agent
Flume agent 的配置保存在一个本地配置文件中。它是一个 text 文本，java 程序可以直接方便地读取属性。可以在同一配置文件中指定一个或多个 agent 的配置。配置文件指定了 agnet 中每个 source、channel、slink 的属性，以及三者如何组合形成数据流。

### 配置单个组件
流中的每一个组件（source、channel、slink）都有自己的名称、类型以及一系列配置属性。例如，一个 Avro source 需要配置 hostname (或者 IP 地址)以及端口号来接收数据。一个内存模式 channel 可以有最大队列长度的属性（"capacity": channel 中最大容纳多少事件）。一个 HDFS slink 则需要知道文件系统的 URL（hdfs://****）、文件落地的路径、文件回滚的评率（"hdfs.rollInterval": 每隔多少秒将零时文件回滚成最终文件保存到 HDFS 中）。所有这个关于各个组件的属性需要在配置文件中进行指定。

### 将各个部分组合起来
Agent 需要知道加载哪些组件以及如何将这些组件组合起来形成数据流。







[1]: http://flume.apache.org/_images/UserGuide_image00.png



