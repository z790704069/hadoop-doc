Storm 是开源免费的分布实时计算系统（Apache Storm is a free and open source distributed realtime computation system）。这里提到了两个关键词：
* 分布式 
* 实时

1、分布式意味着 Storm 是部署在多台主机上，它解决并发性（多机资源同时作业）以及可用性（一台主机出现问题，计算任务移交到其他机器）问题
> 分布式自然而然让我们联想到了需要一个协调服务，这里提到的就是 zookeeper。zookeeper 用于协调 Nimbus、Supervisor。

![][5]

2、实时则区别于 Mapreduce 的批处理

至于如何从原理上理解 Storm 的特性，我会在后面的文章中具体介绍。<font color="red">本文则简单介绍如何安装以及启动 Storm，</font> 先将其完整的运作起来能够帮助我们理解以及建立信心。这里为什么说是简单介绍呢，因为安装并启动一个 Storm 运行环境确实很简单。

> 安装 Storm 之前请确保你已经安装好:
> * jdk
> * zookeeper


# 部署示例
 ![storm部署图][3]

* 三台主机 mini01 + mini02 + mini03
* zookeeper 部署在三台主机上
* mini01 上部署 nimbus 并启动 UI
* supervisor 部署在 mini02 和 mini03 上

# 安装
## zookeeper 集群安装
参见 [zookeeper 集群安装配置][1] ,当然你可以安装单节点的 zookeeper

## storm 安装 & 配置
### 下载解压
[访问官网][2]，下载你需要的版本，本文使用的是 1.0.6 版本（apache-storm-1.0.6.tar.gz）。
将 apache-storm-1.0.6.tar.gz 拷贝到三台主机上并解压。
```
cd /home/app
tar -zxvf apache-storm-1.0.6.tar.gz
```

### 增加 Storm 环境变量
修改/etc/profile，将 Storm 加入环境变量
```
vim /etc/profile
```
/etc/profile 文件如下
```
export STORM_HOME=/home/app/apache-storm-1.0.6
export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HBASE_HOME/bin:$STORM_HOME/bin:$PATH
```
生效/etc/profile
```
source /etc/profile
```

### 修改 Storm 配置文件
```
cd apache-storm-1.0.6/conf
vim storm.yaml
```
配置每台主机上的 storm.yaml 文件，配置内容如下
```
storm.zookeeper.servers:
        - "mini01"
        - "mini02"
        - "mini03"

storm.local.dir: "/home/app/apache-storm-1.0.6/data"

nimbus.seeds: ["mini01"]

supervisor.slots.ports:
        - 6700
ui: 8088
```
其中：
* storm.zookeeper.servers： 配置 zookeeper 的服务节点，因为这里使用 zookeeper 的默认端口(2181)，所以 zookeeper 的端口就不需要特别指定
* storm.local.dir： Nimbus 和 upervisor 需要一个本地目录存放少量状态（例如 Jar 包或者配置文件之类）。用户首先需要创建这个目录。
* nimbus.seeds：nimbus 节点地址，这里 mini01 主机作为主节点
* supervisor.slots.ports：配置supervisor，开启几个端口插槽，就开启几个对应的worker进程
* ui: 设置Storm Web UI 的 http 端口(可选，本文中我们只在 mini01 上配置该属性，因为我们访问的是 mini01:8088)

> 特别注意，配置项冒号后面需要接一个空格

这里只列出了几个能保证服务正常运行的配置项，Storm 提供了很多其他配置项，感兴趣的可以看看 [Storm 配置文件源码][4]

# 启动
mini01 上 启动 nimbus 以及 Storm UI
```
nohup storm nimbus &
nohup storm ui &
```
mini02 和 mini03 上启动 supervisor
```
nohup storm supervisor &
```
# 验证
## jps
mini01 上可以看到 zookeeper(QuorumPeerMain) 以及 nimnus 进程
```
[root@mini01 5257]# jps
4810 QuorumPeerMain
5257 core
5206 nimbus
5410 Jps
```
mini02 和 mini03 上可以看到 supervisor 进程
```
[root@mini02 bin]# jps
2823 QuorumPeerMain
3100 Supervisor
3191 Jps
```
## Storm Web UI
访问 mini01:8088
![Storm UI][6]


# Tips

在安装启动时可能会有一些报错（例如下面）：

* storm nimbus not a leader
* java.util.zip.ZipException: Not in GZIP format
* org.apache.storm.shade.org.apache.zookeeper.KeeperException$NoNodeException: KeeperErrorCode = NoNode 

请确保： 

* 防火墙没有阻止端口，包括 zookeeper 和 storm。
* zookeeper 正常启动。
* /etc/hosts 配置正确。


不能正常运行，**查看如下几个日志，对症下药**即可：

* zookeeper 报错请查看 ZK_HOME/bin/zookeeper.out; 
* nimbus 报错请查看 STORM_HOME/logs/nimbus.log; 
* supervisor 报错请查看 STORM_HOME/logs/supervisor.log



[1]: https://www.kooola.com/article/zookeeper
[2]: http://storm.apache.org/downloads.html
[3]: https://www.kooola.com/upload/2018/10/q6v7dhacfsjf0q32on9ko6im6j.png
[4]: https://github.com/apache/storm/blob/v1.0.6/conf/defaults.yaml
[5]: https://www.kooola.com/upload/2018/10/1c4ev2s5skgjqo229upeo47hsk.png
[6]: https://www.kooola.com/upload/2018/10/6akvmbaspggjsqv1d096u026vi.png