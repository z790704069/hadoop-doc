# 架构（Architecture）

如下图所示，Cloudera Manager 的核心是 Cloudera Manager Server（一下简称Server）。Server 托管管理控制台 web 服务和应用程序逻辑，并负责软件的安装、配置、服务的启动与关闭以及管理集群。

![][1]

Server 和其他一些组件共同工作：
* **Agent** - 安装在每台主机上。Agent 负责进程的启动和停止，解压配置，触发安装以及监视主机。
* **Management Service** - 由一组角色组成的服务，这些角色执行各种监视，警报和报告功能。
* **Database** - 存储配置以及监控信息。通常情况下，多个逻辑数据库运行在一个或多个数据库服务器上。例如，Cloudera Manager Server 和 monitoring roles 使用不同的逻辑数据库。
* **Cloudera Repository** - Cloudera Manager分发软件的存储库。
* **Clients** - 与Server交互的接口
  * Admin Console - 管理员 web 界面版
  * API - 用于开发者创建 Cloudera Manager 程序

## 心跳（Heartbeating）




[1]: https://www.cloudera.com/documentation/enterprise/5-7-x/images/xcm_arch.png.pagespeed.ic.8Cx5_5Dr3T.webp