# 目的
该文档是用户使用Hadpoop分布式文件系统(HDFS)的起点，不管是作为hadoop集群的一部分来使用还是独立的通用分布式文件系统。虽然在很多场景下HDFS被设计成“正常工作”即可，但是掌握更多的HDFS工作机制将有利于更好的配置以及诊断。

# 概述
HDFS是使用Hadoop程序来实现的分布式存储系统。一个HDFS集群主要包含管理文件系统命名空间的Namenode以及存储实际数据的Datanode。HDFS架构指南已经详细介绍了HDFS。本用户指南主要负责用户、管理员与HDFS集群之间交互的介绍。HDFS架构图描述了Namenode、Datanode以及客户端之间的基本交互。客户端联系NameNode以获取文件元数据或文件修改，并直接使用DataNodes执行实际文件I/O。

下面可能是许多用户感兴趣的一些显著特性。

* Hadoop，包括HDFS，适合使用一些商用主机进行分布式存储以及分布式计算。它容错率高并且很容易扩展。MapReduce因其简单性和适用于大量分布式应用程序而闻名，是Hadoop不可或缺的一部分。
* HDFS是高度可配置的，同时提供了默认配置。大多数时候，只有在面对非常大的集群时才需要进行一些配置。
* Hadoop使用JAVA编写并支持在所有主流平台上运行。
* Hadoop支持类似shell一样的命令行与HDFS交互。
* Namenode和Datanode内置web服务,便于检查当前集群状态。
* 在HDFS中定期实现新的特性和改进。下面即为部分有用的特性：
  * 文件权限以及认证
  * 机架感知：在计划任务和分配存储时考虑节点的物理位置
  * 安全模式：一种维护管理模式
  * fsck：文件系统健康状态诊断，发现丢失的文件或块
  * fetchdt：获得Delegation token的工具，并且存储到本地文件系统的文件中
  * Balancer：平衡集群的工具，当数据在Datanode中不均匀分布的时候
  * 升级以及回滚：当软件升级后，HDFS可能需要回滚到升级前的状态以预防一些位置问题
  * Secondary NameNode：定期检查命名空间，并保持HDFS修改日志的文件大小在Namenode限定范围内。
  * 检查点节点：定期检查命名空间，并最小化Namenode上HDFS修改日志的文件大小。充当以前由Secondary NameNode担任的角色，虽然它还没有被增强。只要没有向系统注册备份节点，NameNode就可以同时允许多个Checkpoint节点。
  * 备份节点：检查点节点的扩展。除了检查功能外，它还以数据流的形式接收来自Namanode的所有操作。备份节点在自身内存中保存命名空间的备份，并且这个备份跟活跃Namenode的命名空间保持同步。Namenode只允许一个备份节点注册进来。
  

# 先决条件
下面的文档介绍如何安装和启动一个Hadoop集群：
* 针对先手上路的 [单节点][1]搭建
* 分布式[集群][2]搭建

接下来的文档假设用户有能力搭建并运行至少有一个Datanode的HDFS。完成本文档的学习，用户可以将Namenode和Datanode放在同一台物理机上。

# Web接口
Namenode和Datanode内部都运行了web服务，用来展示集群当前状态的基本信息。默认配置下，Namenode的前端页面请访问http://namenode-name:50070/。该页面展示Datanode列表以及集群的基本统计信息。这个web接口同样可以用来浏览文件系统（点击页面中的“Browse the file system”）。


# Shell 命令
Hadoop包含丰富的类 shell 命令，用来跟HDFS和其他Hadoop支持的文件系统进行直接交互。运行“bin/hdfs dfs -help”命令将列出Hadoop支持的命令。此外，运行“bin/hdfs dfs -help command-name” 展示“command-name”命令的详细信息。这些命令支持大部分普通的文件系统操作，例如拷贝文件，修改文件权限等等。它还支持一些HDFS的特定操作，例如改变文件复制。详细信息请查看 [File System Shell Guide][3]

## DFSAdmin 命令




[1]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html
[2]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html
[3]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html

