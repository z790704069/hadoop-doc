本文将介绍一些常用的 Hbase Shell 命令。作为开发以及运维人员这些常用命令是需要了解并经常使用的，当然可以不必将他们死记硬背下来。如果在某些场景下想到需要使用某个命令，但是又不知道具体的使用方法时，可以扫一眼这篇文章（熟练使用 mysql 命令的用户可能会觉得 hbase shell 命令的设计有点费解--!）。

Hbase Shell 为 Hbase 提供了一套“简单方便”的命令行工具。使用它可以很好地与 Hbase 进行交互，例如查看 Hbase 集群状态、对 Hbase 数据进行增删改查操作等等。启动 Hbase 之后，我们可以通过 "hbase shell" 进行 Hbase Shell 命令行。
![][1]

Hbase Shell 中运行 help 命令可以看到有哪些命令可以使用。这些命令按照功能范围被分成了很多组（Command Groups），每组都包含了若干个命令。本文我选取几个日常工作中常用的命令来进行介绍(对其他命令感兴趣的朋友，可以逐个百度或者 google 进行了解)，这些命令主要分布在 general（通用）、ddl（data manipulation language：数据操作语言）、dml(data definition language：数据定义语言) 这些分组中。
![][2]
## General
* status
* version
* table_help
* whoami

### status
![][3]
status 命令显示 hbase 集群的状态，包括 master、region server 的数量和活跃情况，还包括集群的负载情况。

并且，我们还可以在 status 后面加上 'simple'、'summary' 或者 'detauled' 字段来获取更加详细的信息。不加任何字段的 status 等同于 status 'summary'。

![][4]

### version
![][5]
version 命令很好理解，就是查看 hbase 版本了。

### table_help
一些关于表操作命令的帮助介绍（Help for table-reference commands）。

### whoami 
查询当前的 hbase 用户

## DDL
* list
* exists
* create
* describe
* enable
* disable
* drop
* alter

### list
### exists
### create
### describe
### enable
### disable
### drop
### alter
## DML

[1]: https://www.kooola.com/upload/2019/01/1cvmgaefpaighop6173os397mo.png
[2]: https://www.kooola.com/upload/2019/01/140ib2mbhsglcrt7l89mvhbe1u.png
[3]: https://www.kooola.com/upload/2019/01/74fmp4t82ugsbpu25hmdkjisf0.png
[4]: https://www.kooola.com/upload/2019/01/s9q856m5f4jvookb0pkf2fcv7i.png
[5]: https://www.kooola.com/upload/2019/01/6pc34t0ncshtboe3pr6236s0qt.png
