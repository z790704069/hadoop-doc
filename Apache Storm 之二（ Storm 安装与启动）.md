> 安装 Storm 之前请确保你已经安装好:
> * jdk
> * zookeeper


# 部署示例
 ![IMAGE](quiver-image-url/C78AC8E576C2BEF80DE4619907E6F12B.jpg =620x291)

* 三台主机 mini01 + mini02 + mini03
* zookeeper 部署在三台主机上
* mini01 上部署 nimbus 并启动 UI
* supervisor 部署在 mini02 和 mini03 上

# 安装
## zookeeper 集群安装
参见 [zookeeper 集群安装配置][1]

## storm 安装
### 下载解压
[访问官网][2]，下载你需要的版本，本文下载的是 1.0.6 版本。
将 apache-storm-1.0.6.tar.gz 拷贝到三台主机上并解压

### 配置
# 启动
# 验证

[1]: https://www.kooola.com/article/zookeeper
[2]: http://storm.apache.org/downloads.html