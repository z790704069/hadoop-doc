> Cloudera Manager 是一个端到端用于管理CDH集群的程序。Cloudera Manager提供了CDH群集很多细节的可视化和控制，因此它为企业化部署提供了一个标准。它使得企业能够高效、合理地管理集群。使用Cloudera Manager，用户可以轻松部署和集中操作完整的CDH堆栈和其他托管服务。这个程序可以自动地安装相关服务，将部署时间大大缩短。它为您提供运行主机和服务的集群范围的实时视图; 提供单个中央控制台，以在整个群集中实施配置更改; 并集成了全套的报告和诊断工具，可帮助您优化性能和利用率。本文主要介绍Cloudera Manager的一些基本概念、结构以及功能。

# 术语（Terminology）
为了更好地使用 Cloudera Manager，我们需要先了解它的一些术语。术语的定义以及相互之间的关系如下：

![][1]

这些概念中，cluster和service不需要过多解释。其他一些概念，我们将在后面的章节中展开介绍，例如：role group、gateway、host template以及parcel等。

**service**、**role** 的类型以及实例这两个概念容易造成混淆。对于这两个概念，Cloudera Manager 以及本文经常会不进行明确的区分。例如 Cloudera Manager 管理员页面的 **Home > Status** 和 **Clusters > ClusterName** 都会列出集群的服务列表。这个很像编程语言里面 "string" 既可以表示类型（java.lang.String），也可以表示字符串实例（"in here"）。当需要区分类型和实例时，附加单词“type”以表示类型，同样，附加单词“instance”以明确表示实例。

# 部署（deployment）
Cloudera Manager 的配置以及所有集群的管理

# 动态资源池（dynamic resource pool）
在 Cloudera Manager 中，负责池中应用服务（例如yarn、Impala等）的资源调度（使用一些可配置的资源以及策略）。

# 集群（cluster）
包含HDFS、Mapreduce以及其他服务的一系列主机以及机架。可以单台机器上部署伪集群CDH，用于演示或学习。

在 Cloudera Manager 中,一个逻辑实体包含一系列主机（host）,这些主机上运行着CDH,已经服务(service)和角色(role)的实例。一个主机只隶属于一个集群。Cloudera Manager 可以同时管理**多个** CDH 集群。但是，每个群集只能与单个Cloudera Manager Server或 [Cloudera Manager HA pair][2] 关联。

# 主机（host）
在 Cloudera Manager 中，运行角色实例的物理机或虚拟机。一个主机只隶属于一个集群。

# 机架（rack）
在 Cloudera Manager 中，一个物理实体，包含通常由同一交换机提供服务的一组物理主机。

# 服务（service）
Cloudera Manager 中的一类托管功能，可以在群集中运行，可以是分布式或者单节点。这里的服务通常是指服务的类型。例如：MapReduce，HDFS，YARN，Spark，Accumulo等等。传统环境下，多个服务运行在一个主机上，在分布式系统下，一个服务运行在多个主机上。

# 服务实例（service instance）
在 Cloudera Manager 中，在群集上运行的服务实例。 例如：“HDFS-1”和“yarn”。服务实例跨越许多角色实例。

# 角色（role）
在 Cloudera Manager 中，服务中的一类功能。例如，HDFS服务有以下多个角色：NameNode，SecondaryNameNode，DataNode已经Balancer。角色在这里指的是角色类型。详情请查看[user role][3]。

# 角色实例（role instance）
在 Cloudera Manager 中，运行在主机上上一个角色实例。它类似于Unix进程。例如："NameNode-h1" ， "DataNode-h1"。

# 角色组（role group）
一系列角色实例的组合。

# 主机模板（host template）
一系列角色组可以认为一个模板。当这个模板应用到某个主机上，每个角色组中的角色会被创建并分配到这个主机上。

# 网关（gateway）
In Cloudera Manager, role that designates a host that should receive a client configuration for a service when the host does not have any role instances for that service running on it.

# parcel
二进制格式，包含已编译的代码和元信息，例如包描述，版本和依赖关系。


# static service pool
在 Cloudera Manager 中，跨服务对群集总的资源（CPU，内存和I / O权重）进行静态分区。

# 集群示例（Cluster Example）
四个主机组成的集群：
![][4]

该集群中，tcdn501-1 是"master"主机，所以他有21个角色实例，而其他主机只有7个。

用户还可以查看每个主机所拥有的服务角色，下图是cdn501-1的服务角色图：
![][5]





[1]: https://www.cloudera.com/documentation/enterprise/5-7-x/images/xcm_model.jpg.pagespeed.ic.WwXPor5pex.webp
[2]: https://www.cloudera.com/documentation/enterprise/5-7-x/topics/admin_cm_ha_overview.html#concept_bhl_cvc_pr
[3]: https://www.cloudera.com/documentation/enterprise/5-7-x/topics/glossaries.html#glossary__glos_userrole
[4]: https://www.cloudera.com/documentation/enterprise/5-7-x/images/xhosts.png.pagespeed.ic.bfDwout6Aw.webp
[5]: https://www.cloudera.com/documentation/enterprise/5-7-x/images/xhost.png.pagespeed.ic.BpUXHGToDC.webp