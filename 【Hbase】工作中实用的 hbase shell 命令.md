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

## DDL & DML
这里将 DDL 和 DML 两类中经常用到的命令放在一起讲解
* list
* exists
* create
* describe
* put
* count 
* scan
* get
* delete
* deleteall

### list
列出数据库中所有表
### exists
判断表是否存在
用法: exists '表名'
![6][]
### create
创建表
用法: create '表名','列族1','列族2'
```
hbase(main):007:0> create 'kooola','cf1','cf2'
```
例如上面的语句就创建了拥有两个列族（cf1、cf2）的表，表名为kooola
### describe
查看表的结构
用法： describe '表名'
```
hbase(main):010:0> describe 'kooola'
Table kooola is ENABLED
kooola
COLUMN FAMILIES DESCRIPTION
{NAME => 'cf1', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLO
CKCACHE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0'}
{NAME => 'cf2', BLOOMFILTER => 'ROW', VERSIONS => '1', IN_MEMORY => 'false', KEEP_DELETED_CELLS => 'FALSE', DATA_BLOCK_ENCODING => 'NONE', TTL => 'FOREVER', COMPRESSION => 'NONE', MIN_VERSIONS => '0', BLO
CKCACHE => 'true', BLOCKSIZE => '65536', REPLICATION_SCOPE => '0'}
2 row(s) in 0.0360 seconds
```
显而易见，查看刚刚创建的 kooola 表，发现有两个列族：cf1 和 cf2

### put
向指定表插入数据
用法： put '表名','rowkey','列族:列名','值'。
> 列簇下的列不需要提前创建，插入值时通过'列族:列名'来指定就行,这意味着 hbase 对表结构的要求更加灵活
```
hbase(main):013:0> put 'kooola','xxxxxx1','cf1:name','march'
```
例如上面的命令，就是向 kooola 表中插入一行数据，其中 rowkey 为 xxxxxx1，cf1 列族的 name 列有一个值为 march

### count
查询表的记录数
用法：count '表名'
```
hbase(main):002:0> count 'kooola'
1 row(s) in 0.0320 seconds
```

### scan
查询表数据
用法： scan '表名'
当然你也可以增加一些过滤器来指定查询条件。
> 我们可以认识一些关键字，例如 COLUMN 和 COLUMNS 进行列族、列过滤；STARTROW 和 STOPROW 用以标识起始、终止rowkey；LIMIT 指定查询的个数；VERSIONS 指定版本。
```
hbase(main):001:0> scan 'kooola'
查询所有数据
```
```
scan 'kooola', {COLUMN=>'cf1'}
指定列族进行查询
```
```
scan 'kooola', {COLUMNS=> 'cf1:name'}
指定列进行查询
```

```
scan 'kooola', { STARTROW => 'xxxxxx1', LIMIT=>2, VERSIONS=>1}
rowkey 从 xxxxxx1 开始查询 2 个版本为 1 的记录
```
```
scan 'kooola', {COLUMNS => ['cf1', 'cf2'], LIMIT => 10, STARTROW => 'xxxxxx1'}
rowkey 从 xxxxxx1 开始查询 10 条记录，记录只显示列族为 cf1 和 cf2 的值
```
> 例外我们也可以使用 FILTER 对值进行过滤
```
scan 'kooola',FILTER=>"RowFilter(=,'binary:xxxxxx1')"
查询 rowkey 为 xxxxxx1 的记录
scan 'kooola',FILTER=>"RowFilter(>,'binary:xxxxxx1')"
查询 rowkey 大于 xxxxxx1 的记录
```
```
scan 'kooola',FILTER=>"RowFilter(=,'substring:xxx1')"
查询 rowkey 中包含 xxx1 的记录
```
```
scan 'kooola',FILTER=>"ValueFilter(=,'binary:march')"
查询拥有 march 值的记录
```
```
scan 'kooola',FILTER=>"ValueFilter(=,'substring:arch')"
查询值包含 arch 的记录
```
```
scan 'kooola',FILTER=>"PrefixFilter('xxxx')"
查询 rowkey 前缀为 xxxx 的记录
```

### get
获取行或单元的值
用法： 
get '表名','rowkey' 
get '表名','rowkey','列族'
get '表名','rowkey','列族:列'
```
get 'kooola','xxxxxx1'
```
```
get 'kooola','xxxxxx1','cf1:name'
```

### delete
使用 delete 命令，可以在一个表中删除特定单元格
用法： 
delete '表名', 'rowkey', '列族:列'

### deleteall
使用“deleteall”命令，可以删除一行中所有单元格
deleteall '表名', 'rowkey'


[1]: https://www.kooola.com/upload/2019/01/1cvmgaefpaighop6173os397mo.png
[2]: https://www.kooola.com/upload/2019/01/140ib2mbhsglcrt7l89mvhbe1u.png
[3]: https://www.kooola.com/upload/2019/01/74fmp4t82ugsbpu25hmdkjisf0.png
[4]: https://www.kooola.com/upload/2019/01/s9q856m5f4jvookb0pkf2fcv7i.png
[5]: https://www.kooola.com/upload/2019/01/6pc34t0ncshtboe3pr6236s0qt.png
[6]: https://www.kooola.com/upload/2019/01/05kkulahm8i69oca7o65et9nsc.jpg

