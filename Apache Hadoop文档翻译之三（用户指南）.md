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

# Web 接口
Namenode和Datanode内部都运行了web服务，用来展示集群当前状态的基本信息。默认配置下，Namenode的前端页面请访问http://namenode-name:50070/。 该页面展示Datanode列表以及集群的基本统计信息。这个web接口同样可以用来浏览文件系统（点击页面中的“Browse the file system”）。


# Shell 命令
Hadoop包含丰富的类 shell 命令，用来跟HDFS和其他Hadoop支持的文件系统进行直接交互。运行“bin/hdfs dfs -help”命令将列出Hadoop支持的命令。此外，运行“bin/hdfs dfs -help command-name” 展示“command-name”命令的详细信息。这些命令支持大部分普通的文件系统操作，例如拷贝文件，修改文件权限等等。它还支持一些HDFS的特定操作，例如改变文件复制。详细信息请查看 [File System Shell Guide][3]

## DFSAdmin 命令
“bin/hdfs dfsadmin” 命令支持一些跟HDFS管理员相关的操作。“bin/hdfs dfsadmin -help” 命令会列出当前支持的所有命令。例如：
* -report: HDFS基础统计信息报告。报告中的一些信息同样可以在Namenode网页上看到。
* -safemode: 虽然通常不需要，但管理员可以手动地方式进入或离开安全模式。
* -finalizeUpgrade: 删除上次集群升级过程中所做的备份。
* -refreshNodes:
* -printTopology :

更多信息，请查看 [dfsadmin][4]

# Secondary NameNode
Namenode以日志的形式存储更新信息，该日志信息追加到本地文件edits中。当Namenode启动，它从一个称为“FsImage”的image文件中读取HDFS状态，然后使用编辑日志文件中的修改信息。随后，写入新的HDFS状态到FsImage文件中并使用一个新的空的的edits文件开始常规操作。因为Namenode只在启动的时候才合并Fsimage和edits文件，在一个工作频繁的集群上edits日志文件会变得非常庞大，这会导致Namenode下次重启时会花费很长时间。

Secondary NameNode定期合并Fsimage和edits文件，并保持edits文件大小在一个限定范围内。Secondary NameNode通常运行在与主Namenode不同的机器上，因为Secondary NameNode需要相同大的内存来保证其运行。

启动运行在Secondary NameNode的检查点，主要被被两个配置参数控制：
* dfs.namenode.checkpoint.period，默认被设置为1小时，指定两个检查点之间的最大间隔时间
* dfs.namenode.checkpoint.txns，默认设置为一百万，定义Namenode上未检查的事务数量，当达到该数量时进行强制检查，即使是检查间隔时间未到。

Secondary NameNode将最新的检查点保存在与主Namenode项目的文件路径下。所以，当需要时NameNode可以随时读取Secondary NameNode上的检查点image文件。

详细信息，请查看[Secondary NameNode][5]


# 检查点节点
Namenode通过两种文件来维持命名空间：fsimage以及journal (log)。其中，fsimage是命名空间的最大检查点以及编辑文件，journal则记录自上一个检查点之后的更新操作。当NameNode启动时，它会合并fsimage和edits journal以提供文件系统元数据的最新视图。NameNode然后用新的HDFS状态覆盖fsimage并开始一个新的编辑日志。

检查点节点定期创建命名空间的检查点。它从活跃的Namenode上下载fsimage和edits,然后在本地进行合并，最后上传最新的fsimage到活跃的Namenode上。检查点节点通常运行在与主Namenode不同的机器上，因为检查点节点需要相同大的内存来保证其运行。用户可以在配置文件指定的节点上运行“bin/hdfs namenode -checkpoint”来启动检查点节点。

“dfs.namenode.backup.address”和“dfs.namenode.backup.http-address”配置属性可以指定检查点节点的位置以及其web接口地址。

两个配置参数影响检查点的运行：
* dfs.namenode.checkpoint.period，默认被设置为1小时，指定两个检查点之间的最大间隔时间
* dfs.namenode.checkpoint.txns，默认设置为一百万，定义Namenode上未检查的事务数量，当达到该数量时进行强制检查，即使是检查间隔时间未到。

检查点节点将最新的检查点保存在与主Namenode项目的文件路径下。所以，当需要时NameNode可以随时读取检查点节点上的检查点image文件。

集群配置文件中可以同时指定多个检查点。

详细信息，请查看 [namenode][6]


# 备份节点
备份节点跟检查点节点一样提供相同的检查功能，将相关信息保存在内存中，更新命名空间的拷贝并与活跃Namenode状态保持同步。除了接受来自NameNode的文件系统编辑的日志流并将其持久保存到磁盘之外，备份节点还将这些编辑应用到其自己的内存中命名空间的副本中，从而创建命名空间的备份。

备份节点不需要像Secondary NameNode和Checkpoint node一样从活动NameNode下载fsimage和编辑文件，以便创建检查点，因为它已经保存命名空间的最新状态在内存中。备份节点进程更高效，因为它只需要将命名空间保存到本地fsimage文件中并重置编辑。


因为备份节点需要将命名空间保存在内存中，所以它需要跟Namenode一样的内存大小。

Namenode一次只支持一个备份节点。当备份节点在使用时，我们就无需检查点节点。同时支持多个备份节点将在未来实现。

备份节点的配置方式与检查点节点相同。我们可以使用“bin/hdfs namenode -backup”来启动备份节点。

“dfs.namenode.backup.address”和“dfs.namenode.backup.http-address”配置属性可以指定备份节点的位置以及其web接口地址。


备份节点提供了使NameNode运行时不需要进行持久化的选项，将保留命名空间状态的所有责任委派给备份节点。要执行此操作，请使用-importCheckpoint选项来启动NameNode，并为edits类型设置非持久性存储目录 dfs.namenode.edits.dir。

关于备份节点和检查点节点的产生原因的讨论，查看 [HADOOP-4539][7]

# 导入检查点
如果fsimage和edits文件的备份丢失，我们可以将最新的检查点导入到Namenode中。操作如下：
* 在配置项“dfs.name.dir”指定的位置建立一个空文件夹
* 在配置项“dfs.namenode.checkpoint.dir”指定检查点的目录位置
* 使用“-importCheckpoint”参数启动Namenode

NameNode会从fs.checkpoint.dir目录读取检查点， 并把它保存在“dfs.name.dir”目录下。如果dfs.name.dir目录下有合法的fsimage，NameNode会启动失败。NameNode会检查“fs.checkpoint.dir”目录下镜像文件的一致性，但是不会去改动它。

相信信息，请查看 [namenode][8]


# 平衡器
HDFS数据可能不会均匀地分布在各个Datanode上。一个常见的原因是一个新的Datanode加入到已经存在的集群中。当需要给新的块选择位置，Namenode在选择哪些Datanode来存放前会考虑多个因素：
* 根据就近原则，将数据块的一个副本放在正在写这个数据块的节点上
* 考虑将块数据放到不同机架上，以降低单机架故障风险
* 一个副本通常被放置在和写文件的节点同一机架的某个节点上，从而降低跨机架的带宽消耗
* 尽量均匀地将HDFS数据分布在集群的DataNode中

由于上述多种考虑需要取舍，数据可能并不会均匀分布在DataNode中。HDFS为管理员提供了一个工具，用于分析数据块分布和重新平衡DataNode上的数据分布。[HADOOP-1652][9] 的附件中的一个PDF是一个简要的rebalancer管理员指南。

详细信息，请查看 [balancer][10]





[1]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html
[2]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/ClusterSetup.html
[3]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html
[4]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#dfsadmin
[5]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#secondarynamenode
[6]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#namenode
[7]: https://issues.apache.org/jira/browse/HADOOP-4539
[8]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#namenode
[9]: https://issues.apache.org/jira/browse/HADOOP-1652
[10]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#balancer