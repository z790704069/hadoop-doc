> Cloudera Manager 是一个端到端用于管理CDH集群的程序。Cloudera Manager提供了CDH群集很多细节的可视化和控制，因此它为企业化部署提供了一个标准。它使得企业能够高效、合理地管理集群。使用Cloudera Manager，用户可以轻松部署和集中操作完整的CDH堆栈和其他托管服务。这个程序可以自动地安装相关服务，将部署时间大大缩短。它为您提供运行主机和服务的集群范围的实时视图; 提供单个中央控制台，以在整个群集中实施配置更改; 并集成了全套的报告和诊断工具，可帮助您优化性能和利用率。本文主要介绍Cloudera Manager的一些基本概念、结构以及功能。

# Terminology
为了更好地使用 Cloudera Manager，我们需要先了解它的一些术语。术语的定义以及相互之间的关系如下：

![][1]

这些概念中，cluster和service不需要过多解释。其他一些概念，我们将在后面的章节中展开介绍，例如：role group、gateway、host template以及parcel等。

**service**、**role** 的类型以及实例这两个概念容易造成混淆。对于这两个概念，Cloudera Manager 以及本文经常会不进行明确的区分。例如 Cloudera Manager 管理员页面的 **Home > Status** 和 **Clusters > ClusterName** 都会列出集群的服务列表。这个很像编程语言里面 "string" 既可以表示类型（java.lang.String），也可以表示字符串实例（"in here"）。当需要区分类型和实例时，附加单词“type”以表示类型，同样，附加单词“instance”以明确表示实例。

# deployment
Cloudera Manager 的配置以及所有集群的管理

# dynamic resource pool
在Cloudera Manager中，负责池中应用服务（例如yarn、Impala等）的资源调度（使用一些可配置的资源以及策略）。

# cluster









[1]: https://www.cloudera.com/documentation/enterprise/5-7-x/images/xcm_model.jpg.pagespeed.ic.WwXPor5pex.webp