![][3]
> Apache Hadoop项目为高可用、可扩展、分布式计算开发开源软件。Apache Hadoop软件库是一个平台，它使用简单的编程模型让跨机器上大数据量的分布式计算变得简单。
它旨在从单个服务器扩展到数千台计算机，每台计算机都提供本地计算和存储。库本身被设计用来在软件层面检测和处理故障，而不是依赖硬件来提供高可用性，因此，在计算机集群之上提供高可用性服务，每个计算机都可能容易出现故障。

# 介绍
HDFS是一个被设计用来运行在商用机器上的分布式文件系统。它跟现有的分布式文件系统有很多相似之处，但是，区别也很大。HDFS容错率高，并且被设计为部署在廉价机器上。HDFS提供对应用程序数据的高吞吐量访问，适用于具有大型数据集的应用程序。HDFS放宽了一些POSIX要求，以实现对文件系统数据的流式访问。HDFS最开始被构建是用来作为Nutch搜索引擎项目的基础设施。HDFS是Apache hadoop核心项目的一部分。项目地址：[http://hadoop.apache.org/][1]

# 假设和目标
## 硬件故障
硬件故障很常见。一个HDFS示例应该是包含成百上千台服务器，每台服务器保存文件系统的部分数据。事实上，存在大量组件并且每个组件都有可能出现故障，这意外着有些组件可能一直无法正常工作。因此，检测故障并且自动从故障中快速恢复是HDFS架构的核心目标。
## 流数据访问
在HDFS上运行的程序需要对其数据集进行流式访问。它们不是运行在普通文件系统上的普通应用程序。HDFS被设计更多是作为批处理来使用，而不是用户的交互式使用。重点是数据访问的高吞吐量而不是数据访问的低延迟。POSIX强加了许多针对HDFS的应用程序不需要的硬性要求。
## 大数据集
运行在HDFS上的程序用户大量的数据集。HDFS中，一个普通文件可以达到千兆到太字节。因此，HDFS调整为支持大文件。它应该提供高聚合数据带宽并扩展到单个集群中的数百个节点。 它应该在单个实例中支持数千万个文件。

## 简单的一致性模型
HDFS程序需要一个一次写入多次读取的文件访问模型。一次创建、写入和关闭的文件，除了追加和截断之外，不需要更改。可以在文件尾部追加内容，但是不能在任意位置进行修改。这种假设简化了一致性问题，并且提高了数据访问的吞吐量。这种模型非常适合Mapreduce程序以及网络爬虫。
## 移动计算比移动数据代价更小
当计算所需要的数据距离计算越近时，计算的效率越高，当数据量越大时，这个效率提升更明显。越少的网络阻塞越能提高系统整体的吞吐量。这样，我们可以认为，将计算迁移到数据附近比将数据迁移到计算附近更高效。HHDFS为应用程序提供了接口，使其自身更靠近数据所在的位置。

## 跨异构硬件和软件平台的可移植性
HDFS被设计成易于从一个平台移植到另一个平台。这有助于HDFS的普及。

# Namenode和DataNodes
HDFS有一个主从架构。一个HDFS集群包含一个Namenode,Namenode管理文件系统的命名空间以及调整客户端对文件的访问。此外，还有一定数量的Datanode,集群中通常一个节点一个Datanode，用来管理其运行节点上的存储。HDFS公开文件系统命名空间，并允许用户数据存储在文件中。集群内部，一个文件被分成一个或多个blocks并且这些blocks存储在一些Datanode上。Namenode执行文件系统命名空间操作，例如打开、关闭、重命名文件和文件夹。Namenode还决定blocks到Datanode的映射。Datanode负责服务于系统客户端的读和写。DataNode还根据NameNode的指令执行块创建，删除和复制。
![][2]

Namenode和Datanode时设计用于运行在商用机器上的软件。这些机器通常运行linux操作系统。HDFS是JAVA语言编写的，所以任何支持JAVA的机器都能运行Namenode和Datanode软件。使用高度可移植的Java语言意味着HDFS可以部署在各种各样的机器上。普遍的部署方式是在一台专用机器上单独运行Namenode软件。集群中其他机器运行Datanode软件。架构支持在一台机器上运行多个Datanode,但是实际部署很少这样做。
一个集群中只存在一个Namenode极大简化了系统的架构。Namenode管理整个HDFS的元数据。系统被设计为任何用户数据都不流经Namenode。

# 文件系统命名空间
HDFS支持传统的文件分层组织。用户或程序可以创建目录以及保存文件到目录中。HDFS文件系统命名空间层次结构跟其他现有文件系统类似：比如创建文件和删除文件，将文件从一个目录移到另一个目录，或者重命名文件。HDFS支持[用户配额][4]和[访问权限][5]。HDFS不支持硬连接或软连接，但是不排除在未来的版本中实现。
Namenode维护文件系统的命名空间。文件系统命名空间或其属性的更改都由Namenode来记录。应用程序可以指定文件的副本数量，该文本由HDFS来维护。文件的副本数称为该文件的复制因子，该信息由NameNode存储。

# 数据复制
HDFS被设计用来可靠地在集群中大量机器上存储大文件。它以一系列块的形式保存每一个文件。文件块被复制多份以提高容错性。每个文件的块大小和复制因子是可配置的。
一个文件除了最后一个块以外，其他块都是相同大小。当设置块长度可变时，用户可以开启一个新的块而不需要在最后的块上进行追加以达到配置的固定大小。
应用程序可以指定文件的副本数量。复制因子在文件创建时指定并能在随后进行修改。HDFS中文件是一次性写入（除非追加和截断）并且任何时候只有一个写入者。
有关块的复制，NameNode全权负责。Namenode定期接收集群中Datanode的心跳和块信息报告。接收到心跳表明Datanode正常工作。块信息报告（Blockreport）包含DataNode上所有块的列表。
![][6]

## 副本选址
副本的选址对HDFS的可靠性和性能起到至关重要的作用。优化副本的选址使HDFS区别于其他分布式文件系统。这是一个需要大量调试和经验的特性。机架感知式的选址策略的目标是为了提供数据可靠性、可用性以及带宽利用率。目前的副本选址策略的实现是在这个方向上的第一次努力。实现这个策略的短期目标是在生产系统中进行验证，掌握更多行为并建立一个基础来测试和研究更加复杂的策略。

HDFS运行在一系列的集群主机上，这些主机通常分布在各个机架上。跨机架上的两个不同主机间信息交换需要经过交换机。在大多数情况下，同一机架中的计算机之间的网络带宽大于不同机架中的计算机之间的网络带宽。

Namenode通过[Hadoop Rack Awareness][7] 过程来决定每个Datanode隶属于哪个机架id。一个简单但是非最佳的策略是将副本放在不同机架上。这样，当一整个机架出现故障时能够防止数据丢失，并且读取数据能使用到不同机架上的带宽。但是，这种策略增加了写操作代价，因为需要传输块到不同机架上。

当副本数为3时，HDFS放置策略通常是将一个副本放在本机架的一个节点上，将另一个副本放在本机架的另一个节点，最后一个副本放在不同机架的不同节点上。该策略可以减少机架之间写入流量，从而提高写入性能。机架出现故障的概率要比机器出现故障的概率低，这个策略不会影响数据可靠性和可用性的保证。但这种策略会降低数据读取时的网络带宽，因为数据只放置在两个机架上而不是三个。这种策略下，文件的副本不是均匀的分布在各个机架上。三分一个的副本放置在一个节点上，三分之二的副本放置在一个机架上，而另外三分之一均匀分布在剩余的机架上。这个策略提高了写性能而不影响数据可靠性和读性能。

当前，这里介绍的默认副本放置策略是一项正在进行的工作。

## 副本选择
为了降低带宽消耗以及读取延时，HDFS尝试让需要读取的副本跟读取者更近。如果存在一个副本在客户端节点所在的同个机架上，那么这个副本是满足读取需求的首选。如果HDFS集群横跨多个数据中心，那么在本地数据中心的副本将是优先于其他远程副本。

## 安全模式
Namenode刚启动时会进入一个特殊阶段，我们称这个阶段为安全模式。当Namenode处于安全模式时，不会发生数据块的复制。Namenode接收来自Datanode的心跳以及块报告信息。块报告包含Datanode持有的一些列数据块。每个块都指定最小副本数。当一个数据块被NameNode检查确保它满足最小副本时数，它被认为是安全的。数据被Namenode检入，当检入达到一个百分比（该百分比可配置）时，Namenode退出安全模式。然后Namenode会确认那些副本数小于指定数量的数据块列表，并且复制这些块到其他Datanode上。

## 文件系统元数据的持久性
HDFS命名空间保存在Namenode上。NmaeNode使用一个称之为EditLog的事务日志持续地记录发生在文件系统元数据的每一个改变。例如，在HDFS中创建一个文件，Namenode将插入一条记录到EditLog中。同样，修改文件的复制因子也会导致一条新的记录插入EditLog中。Namenode使用本地操作系统文件来保存EditLog。整个文件系统命名空间（**包括块到文件的映射以及文件系统属性**）存储在一个称为FsImage的文件中。FsImage同样存储在Namenode的本地文件系统中。

Namenode将整个文件系统的命名空间以及块映射信息保存在内存中。当Namenode启动时，或者可配置阈值的检查点被触发时，Namenode便从磁盘中读取FsImage和EditLog，将EditLog中的事务应用到表示FsImage的内存中，然后将最新的FsImage内存刷新到磁盘上。然后可以截断旧的EditLog，因为新版的EditLog已经持久化到FsImage中。以上整个过程称为检查点(checkpoint)。检查点的目的是通过获取文件系统元数据的快照并将其保存到FsImage来确保HDFS具有文件系统元数据的一致视图。即使读取FsImage很高效，直接向FsImage增加编辑并不高效。因此针对每次编辑，我们会放在EditLog中而不是直接修改FsImage。检查点期间，EditLog的变更信息会应用到FsImage上。可以以秒为单位的给定时间间隔（dfs.namenode.checkpoint.period）触发检查点，或者在累积给定数量的文件系统事务（dfs.namenode.checkpoint.txns）之后触发检查点。当两者均被设置，则哪个先生效就直接触发检查点。

Datanode将HDFS数据保存在本地文件系统的文件中。Datanode对HDFS文件没有意识，它只是保存HDFS数据的每个块，并保存在本地文件系统中的各个文件中。Datanode不会在相同目录下创建所有文件,而是每个目录中有一个最佳文件数，并适当创建子目录。将所有文件放在同一个目录下不太明智，因为本地文件系统或许不能有效支持在一个目录下面存放海量文件。当一个Datanode启动时，它将扫描本地文件系统，生成所有HDFS数据块的列表（这些数据块对应每一个本地文件），并发送报告给Namenode。这个报告我们称之为块报告。

# 通讯协议
所有HDFS协议均位于TCP/IP之上。一个客户端建立一个连接到Namenode机器上的TCP端口（端口可配置）上。它使用客户端协议与NameNode通讯。Datanode使用Datanode协议与Namenode进行通讯。一个远程过程调用（RPC）封装了客户端协议以及Datanode协议。**按照设计，NameNode永远不会启动任何RPC。 相反，它只响应DataNodes或客户端发出的RPC请求。**

# 鲁棒性
HDFS的主要目标是即使在出现故障时也能可靠地存储数据。三种常见的故障有：Namanode故障、Datanode故障以及~~网络分裂?~~

## 数据磁盘故障，心跳以及重新复制
每个Datanode会定期地发送心跳信息给你Namenode。网络分裂会导致部分Datanode失去与Namenode的联系。Namenode通过收不到Datanode的心跳信息来收集这个失联信息。Namenode标记近期没有心跳信息的Datanode,并终止对这些Datanode的IO请求。在已经死亡的DataNode注册的任何数据在HDFS将不能再有效。Datanode故障会导致一些块的复制因子低于它们的指定值。NameNode时常地跟踪数据块是否需要被复制并在必要的时候启动复制。多个原因会导致重新复制的操作，例如：Datanode不可用，副本损坏，Datanode磁盘损坏，或者文件的复制因子增大。

将DatNode标记为死亡的超时时间适当地加长（默认超过10分钟）是为了避免DataNode状态改变引起的复制风暴。针对性能敏感的情况，用户可以通过配置来设置更短的时间间隔来标记DataNode为失效，尽量避免失效的节点还继续参与读写操作。

## 集群重新平衡
HDFS架构兼容数据再平衡方案。当一个Datanode上的剩余空间降到一定阈值时，方案应该自动将该Datanode上的数据迁移到其他Datanode上。对于一些特定文件的突发需求，方案应动态地创建额外的副本并重新平衡集群中的其他数据。这种方案暂时还未实现。

## 数据完整性
从DataNode获取的数据块可能已损坏。发生损坏的原因可能是存储设备的故障、网络故障或者缺陷软件。HDFS客户端软件对HDFS文件的内容进行校验和检查。当客户端创建一个HDFS文件，它会计算文件的每一个数据块的校验和，并将这些校验和储存在HDFS命名空间中一个单独的隐藏的文件当中。当客户端从DataNode获得数据时会对对其进行校验和，并且将之与储存在相关校验和文件中的校验和进行匹配。如果没有，客户端会选择从另一个拥有该数据块副本的DataNode上恢复数据。
## 元数据磁盘故障
FsImage和EditLog是HDFS的核心数据架构。这些文件的故障会导致HDFS实例无法工作。因为这个原因，HDFS可以被配置成支持多份FsImage和EditLog备份。任何对FsImage和EditLog的更新，都会导致其他所有FsImage和EditLog的同步更新。同步更新多份FsImge和EditLog降低NameNode能支持的每秒更新命名空间事务的频率。然而，频率的降低是可以被接受，尽管HDFS应用本质上是对数据敏感，但不是对元数据敏感。当一个NameNode重新启动，他会选择最新的FsImage和EditLog来使用。另一个增加容错性已达到高可用性的选项是使用多个Namenodes，这个选项的具体实现可以为：[文件系统的共享存储][8]、[分布式的编辑日志][9]（称为Journal）。后面会介绍使用方法。

## 快照
[快照][10]支持在特定的时间（瞬间）保存数据的副本。我们使用的快照特征可能是将损坏的HDFS实例回退到先前一个正常版本。

# Data Organization
## 数据块
HDFS被设计成支持非常大的文件。与HDFS兼容的应用程序是处理大型数据集的应用程序。这些程序一次写入数据多次读取，并且这些读操作满足流式的速度。HDFS支持一次写入多次读取的文件语义。HDFS中，一个典型的块大小是128兆。因此，一个HDFS文件会被切割成128M大小的块，如果可以的话，每一个大块都会分属于一个不同的DatNode。

## 复制流水线
当客户端按照3个副本数来写数据到HDFS文件中，Namenode使用副本目标选择算法来获取Datanode列表。这个列表包含块副本将落地的Datanode。然后客户端将数据写入第一个Datanode。第一个Datanode开始分部分接收数据，将每个部分写入其本地存储，并将该部分传输到列表中的第二个Datanode。第二个Datanode开始接收数据块的每个部分并写入本地存储然后传输到第三个Datanode。最后，第三个Datanode将数据写入本地存储。因此，一个Dataode能够从上一个节点接收数据据并在同一时间将数据转发给下一个节点。因此，数据在管道中从一个DataNode传输到下一个（复制流水线）。

# 可访问性
应用程序可以通过多种方式来访问HDFS。HDFS本身提供[FileSystem Java API][11]让应用程序来访问，这个Java Api的C语言封装版本以及REST API同样可以供用户使用。另外，一个HTTP浏览器也可以访问HDFS实例中的文件。使用[MFS gateway][12],HDFS能被安装作为本地文件系统的一部分。

## FS Shell
HDFS提供一个称为[FS Shell][13]的命令行接口，方便用户与HDFS中的数据进行交互。这写命令集合的语法跟其他我们熟悉的命令十分相似（例如bash,csh）。下面是一些动作/命令对的示例：

|动作|命令|
|:---:|:---:|
|创建目录|bin/hadoop dfs -mkdir /foodir|
|删除目录|bin/hadoop fs -rm -R /foodir|
|查看文件内容|bin/hadoop dfs -cat /foodir/myfile.txt|

## DFSAdmin
DFSAdmin命令集是用来管理HDFS集群。只有HDFS管理者才能使用这些命令。下面是一些动作/命令对的示例：

|动作|命令|
|:---:|:---:|
|Put the cluster in Safemode|bin/hdfs dfsadmin -safemode enter|
|Generate a list of DataNodes|bin/hdfs dfsadmin -report|
|Recommission or decommission DataNode(s)|bin/hdfs dfsadmin -refreshNode|

## 浏览器界面
A typical HDFS install configures a web server to expose the HDFS namespace through a configurable TCP port. This allows a user to navigate the HDFS namespace and view the contents of its files using a web browser.

通常安装HDFS时会配置一个web服务，通过一个配置好的TCP端口来暴露HDFS命名空间。它允许用户看到HDFS命名空间，并能使用web浏览器来查看文件内容。

# 空间回收
## 文件的删除和恢复
如果垃圾配置可用，使用HDFS SHELL删除的文件不会立刻从HDFS删除，而是被移动到垃圾目录（每个用户都有自己的垃圾目录：/user/<username>/.Trash），文件只要在垃圾目录中，可以随时进行恢复。

大部分刚删除的文件都被移动到垃圾目录中（/user/<username>/.Trash/Current），并且在一个可配置的间隔时间内，HDFS为当前垃圾目录中的文件创建检查点（under /user/<username>/.Trash/<date>），同时删除那些过期的检查点。垃圾检查点信息请点击[expunge command of FS shell][16]

# 参考
Hadoop [Java API][14]
HDFS源码: [http://hadoop.apache.org/version_control.html][15]




[1]: http://hadoop.apache.org/
[2]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/images/hdfsarchitecture.png
[3]: http://kooola.com/upload/2018/06/vpcgv27a4ghtgrkts238n63moj.jpg
[4]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsQuotaAdminGuide.html
[5]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html
[6]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/images/hdfsdatanodes.png
[7]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/RackAwareness.html
[8]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html
[9]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithQJM.html
[10]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html
[11]: http://hadoop.apache.org/docs/current/api/
[12]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsNfsGateway.html
[13]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html
[14]: http://hadoop.apache.org/docs/current/api/
[15]: http://hadoop.apache.org/version_control.html
[16]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html#expunge

