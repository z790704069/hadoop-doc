> 操作系统: CentOs6.4 虚拟机（4核 3G） 
>
> 主机： cdh01(server & agent) + cdh02( agent )
>
> CDH版本：cdh5.7.1
>
> jdk8


---


# 准备工作
## 关闭防火墙
在两台主机上都运行一下命令
```
service iptables stop
```

## 设置主机名
```
vim /etc/sysconfig/network
```
将HOSTNAME 分别改成cdh01和cdh02：
```
NETWORKING=yes
HOSTNAME=cdh01
```
重启生效
service network restart

## 修改/etc/hosts
```
vim /etc/hosts
```

加上如下两行（两台主机同样操作）：
```
192.168.132.140 cdh01
192.168.132.141 cdh02
```

## 设置ssh免密登陆
两台主机上均一下运行，出现提示一直回车即可
```
ssh-keygen
```
在cdh01主机上运行:
```
ssh-copy-id cdh02
```
在cdh02主机上运行:
```
ssh-copy-id cdh01
```

## 开启时间同步(每台)
```
service ntpd start
```

## jdk安装
```
[root@localhost opt]# rpm -ivh jdk-8u45-linux-x64.rpm
[root@localhost opt]# java -version
java version "1.8.0_45"
Java(TM) SE Runtime Environment (build 1.8.0_45-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.45-b02, mixed mode)
```

# CM安装
## Server端安装
### 创建目录 
```
mkdir /opt/cloudera-manager
```
### 解压CM到/opt/cloudera-manager目录
```
tar -zxvf cloudera-manager-el6-cm5.7.1_x86_64.tar.gz -C /opt/cloudera-manager
```

### 创建cloudera-scm用户
```
useradd --system --home=/opt/cloudera-manager/cm-5.7.1/run/cloudera-scm-server/ --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm
```

### 创建本地元数据存放目录
```
mkdir /var/cloudera-scm-server
```
### 修改文件夹所属用户
```
chown cloudera-scm:cloudera-scm /var/cloudera-scm-server/
chown cloudera-scm:cloudera-scm /opt/cloudera-manager/
```

### 修改配置文件，设置CM server host
```
vim /opt/cloudera-manager/cm-5.7.1/etc/cloudera-scm-agent/config.ini
```
```
# Hostname of the CM server.
server_host=cdh01
```

### 创建parcel-repo仓库目录
```
mkdir -p /opt/cloudera/parcel-repo
```
将
CDH-5.7.1-1.cdh5.7.1.p0.11-el6.parcel,
CDH-5.7.1-1.cdh5.7.1.p0.11-el6.parcel.sha,
manifest.json
三个文件拷到/opt/cloudera/parcel-repo下

### 创建parcels目录并改变所属用户
```
mkdir -p /opt/cloudera/parcels
chown cloudera-scm:cloudera-scm /opt/cloudera/parcels
```

### mysql安装及配置
安装并启动mysql
```
yum -y install mysql mysql-server mysql-devel
service mysql start
```

登陆、设置root密码、开放远程登陆权限
```
mysql -u root
mysql> use mysql;
mysql> update user set password='你的密码' where user='root';
mysql> grant all privileges on *.* to 'root'@'%' identified by '你的密码' with grant option;
```

### mysql driver包
mysql-connector-java-5.1.35.jar放到/opt/cloudera-manager/cm-5.7.1/share/cmf/lib目录下

### 初始化mysql
```
/opt/cloudera-manager/cm-5.7.1/share/cmf/schema/scm_prepare_database.sh mysql -hcdh02 -uroot -p你的密码 --scm-host cdh02 scmdbn root 你的密码
```

### 启动Server、Agent
```
/opt/cloudera-manager/cm-5.7.1/etc/init.d/cloudera-scm-server start
/opt/cloudera-manager/cm-5.7.1/etc/init.d/cloudera-scm-agent start
```

### 验证
访问 http://ip:7180

## Agent端安装
### 创建目录 
```
mkdir /opt/cloudera-manager
```
### 解压CM到/opt/cloudera-manager目录
```
tar -zxvf cloudera-manager-el6-cm5.7.1_x86_64.tar.gz -C /opt/cloudera-manager
```

### 创建cloudera-scm用户
```
useradd --system --home=/opt/cloudera-manager/cm-5.7.1/run/cloudera-scm-server/ --no-create-home --shell=/bin/false --comment "Cloudera SCM User" cloudera-scm
```
### 修改配置文件，设置CM server host
```
vim /opt/cloudera-manager/cm-5.7.1/etc/cloudera-scm-agent/config.ini
```
```
# Hostname of the CM server.
server_host=cdh01
```

### 创建parcels目录并改变所属用户
```
mkdir -p /opt/cloudera/parcels
chown cloudera-scm:cloudera-scm /opt/cloudera/parcels
```
### Agent启动
```
/opt/cloudera-manager/cm-5.7.1/etc/init.d/cloudera-scm-agent start
```


