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
Heartbeats是Cloudera Manager中的主要通信机制。默认情况下，Agent 每15秒发送一次心跳给 Server。当然，这个心跳频率可以进行调整。

通过这个心跳机制，Agent 向 Server 汇报自己的活动。反过来，Server 会响应 Agent 的活动。Agent 和 Server 最终都会进行一些对帐。例如，如果你启动一个服务， Agent 尝试启动相应的进程，如果这个进程启动失败，Server会标记这个失败的启动命令。

# 状态管理（State Management）
Server 维护集群的状态。这个状态可以划分为两类：**model** 和 **runtime**， 二者都保存在 Server 的数据库中。

![][2]

Cloudera Manager 为 CDH 建模以及管理服务：角色、配置以及内部依赖。模型告诉我们应该在什么地方运行什么以及使用什么样的配置。例如，模型状态捕获了一个事实，即一个集群包含17个主机，每个主机应该运行一个DataNode。你可以使用管理控制台的配置页或者 API 来操作模型，例如 "Add Service"。

Runtime 状态这个概念可以理解为：进程在哪里运行，什么命令正在运行等。在管理控制台页面中选择“启动”时，服务器将收集相关服务和角色的所有配置，对其进行验证，生成配置文件并将其存储在数据库中。

当你更新配置（例如更改 Hue Server 的 web 端口），你就更新了 Model 状态。然后， 当 Hue 正在运行时改变端口，它仍然会使用老的端口。当这种情况发生时，角色会被标记为拥有一个“过时的配置”。为了达到同步，你必须重启角色（触发配置文件重新生成以及进程重启）。







[1]: https://www.cloudera.com/documentation/enterprise/5-7-x/images/xcm_arch.png.pagespeed.ic.8Cx5_5Dr3T.webp
[2]: https://www.cloudera.com/documentation/enterprise/5-7-x/images/xstate.png.pagespeed.ic.VrT7FVIqnM.webp