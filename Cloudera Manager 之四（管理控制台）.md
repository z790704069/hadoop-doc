Cloudera 管理控制台是一个网站页面，你可以用它来配置、管理以及监控 CDH。

如果服务已经配置，控制台头部的导航栏将显示如下：
![][1]

导航栏显示以下信息：
* 集群（Clusters > cluster_name）
  * 服务（Services） - 展示个别服务，以及 Cloudera Management 服务，在这个页面你可以：
    * 查看服务实例的状态以及其他细节，也可以查看与服务相关的角色实例
    * 修改服务、角色实例的配置
    * 添加、删除服务或者角色
    * 启动、停止、重启服务或角色
    * 查看服务或角色上已经运行过的命令
    * 查看审计历史日志
    * 部署、下载客户端配置
    * 退役和重新启用角色实例
    * 进入或退出维护模式
    * 其他特定操作：HDFS HA 或 NameNode federation；运行 HDFS Balancer；创建 HBase、Hive、Sqoop目录 等
  * 主机（Hosts）- 展示集群中所有主机
  * 动态资源池（Dynamic Resource Pools） - 通过指定命名池的相对权重，管理对YARN和Impala服务的群集资源的动态分配。
  * 静态服务池（Static Service Pools）- 管理集群中 HBase、HDFS、Impala、MapReduce 以及 YARN 服务的静态资源分配。
  * Impala Queries - 展示当前集群上的 Impala 查询。
  * YARN Applications - 展示当前集群上的 YARN 程序。
* 主机（Hosts）- 展示 Cloudera Management 管理的主机，在这个页面你可以：
  * 查看每个主机的状态以及一系列具体指标
  * 主机监控的指标配置
  * 查看主机上的服务进程
  * 添加或删除主机
  * 创建、管理主机模板
  * 管理 parcels
  * 退役和重新启用主机
  * Make rack assignments
  * Run the host upgrade wizard
* 诊断（Diagnostics）- 日志、事件以及相关报警，子页面如下：
  * 事件（Events）
  * 日志（Logs）- 日志搜索，可以对服务、角色、主机进行日志搜索，支持日志级别选择
  * 服务日志（Server Log）- 展示 Cloudera Manager Server 的日志
* 审核（Audits）- 查询或过滤查询在集群上的操作事件，例如用户登录
* 图表（Charts）- 各种指标的图表形式展示
* 管理（Administration）
  * 设置（Setting）
  * 警报（Alerts）
  * 用户（Users）
  * 安全（Kerberos）
  * 许可证（License）
  * 语言（Language）
* Parcel Icon - 指向 Hosts > Parcels 页，显示一些服务的版本以及其他信息
* Running Commands Indicator - 展示正在运行的命令数
* 搜索（Search）
* 支持（Support）
* Logged-in User Menu

[1]: https://www.kooola.com/upload/2018/10/5hspu07t9egieq2i5t0qjpbpgg.jpg