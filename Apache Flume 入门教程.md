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


![][2]

[1]: http://flume.apache.org/_images/UserGuide_image00.png



