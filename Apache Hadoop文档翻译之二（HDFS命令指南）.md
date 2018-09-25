# 概要
所有的HDFS命令使用bin/hdfs脚本来调用。空参数运行该脚本将展示所有命令的介绍。

使用方法: hdfs [SHELL_OPTIONS] COMMAND [GENERIC_OPTIONS] [COMMAND_OPTIONS]

Hadoop有一个选项解析框架，它采用解析通用选项以及运行类。

|COMMAND_OPTIONS|Description|
|:---|:---|
|--config --loglevel|The common set of shell options. These are documented on the [Commands Manual][1] page.|
|GENERIC_OPTIONS|The common set of options supported by multiple commands. See the Hadoop [Commands Manual][1] for more information.|
|COMMAND   COMMAND_OPTIONS|Various commands with their options are described in the following sections. The commands have been grouped into [User Commands][2] and [Administration Commands][3].|

# 用户命令
对Hadoop集群用户有用的诸多命令。
## classpath
用法: hdfs classpath [--glob |--jar <path> |-h |--help]

|COMMAND_OPTION|Description|
|:---|:---|
|--glob|expand wildcards|
|--jar path|write classpath as manifest in jar named path|
|-h, --help|print help|

打印获取Hadoop jar以及依赖库所需的类路径。如果不带参数调用，则打印由命令脚本设置的类路径，该脚本可能在类路径条目中包含通配符。其他选项在通配符扩展后打印类路径，或将类路径写入jar文件的清单中。后者在无法使用通配符且扩展类路径超过支持的最大命令行长度的环境中非常有用。

## dfs
用法: hdfs dfs [COMMAND [COMMAND_OPTIONS]]
在hadoop支持的文件系统上运行一个文件系统命令。COMMAND_OPTIONS变量可以在[文件系统shell指南][4]中找到。

## fetchdt
用法: hdfs fetchdt <opts> <token_file_path>

|COMMAND_OPTION|Description|
|:-|:-|
|--webservice NN_Url|Url to contact NN on (starts with http or https)|
|--renewer name|Name of the delegation token renewer|
|--cancel|Cancel the delegation token|
|--renew|Renew the delegation token. Delegation token must have been fetched using the –renewer name option.|
|--print|Print the delegation token|
|token_file_path|File path to store the token into.|

从NameNode获取委托令牌。详细内容请参见 [fetchdt][5]

## fsck
用法：
```
   hdfs fsck <path>
          [-list-corruptfileblocks |
          [-move | -delete | -openforwrite]
          [-files [-blocks [-locations | -racks | -replicaDetails | -upgradedomains]]]
          [-includeSnapshots]
          [-storagepolicies] [-maintenance] [-blockId <blk_Id>]
```

|COMMAND_OPTION|	Description|
|:-|:-|
|path	|Start checking from this path.|
|-delete|	Delete corrupted files.|
|-files|	Print out files being checked.|
|-files -blocks|	Print out the block report|
|-files -blocks -locations	|Print out locations for every block.|
|-files -blocks -racks	|Print out network topology for data-node locations.|
|-files -blocks -replicaDetails	|Print out each replica details.|
|-files -blocks -upgradedomains	|Print out upgrade domains for every block.|
|-includeSnapshots|	Include snapshot data if the given path indicates a snapshottable directory or there are snapshottable directories under it.|
|-list-corruptfileblocks|	Print out list of missing blocks and files they belong to.|
|-move|	Move corrupted files to /lost+found.|
|-openforwrite|	Print out files opened for write.|
|-storagepolicies	|Print out storage policy summary for the blocks.|
|-maintenance|	Print out maintenance state node details.|
|-blockId	|Print out information about the block.|

运行HDFS文件系统检查实用程序。详细内容请参见 [fsck][6]

## getconf
用法：
```
hdfs getconf -namenodes
hdfs getconf -secondaryNameNodes
hdfs getconf -backupNodes
hdfs getconf -includeFile
hdfs getconf -excludeFile
hdfs getconf -nnRpcAddresses
hdfs getconf -confKey [key]
```
|COMMAND_OPTION|	Description|
|:-|:-|
|-namenodes	|gets list of namenodes in the cluster.|
|-secondaryNameNodes|	gets list of secondary namenodes in the cluster.|
|-backupNodes	|gets list of backup nodes in the cluster.|
|-includeFile|	gets the include file path that defines the datanodes that can join the cluster.|
|-excludeFile	|gets the exclude file path that defines the datanodes that need to decommissioned.|
|-nnRpcAddresses|	gets the namenode rpc addresses|
|-confKey [key]	|gets a specific key from the configuration|

从配置目录中获取配置信息，进行后处理。

## groups
用法: hdfs groups [username ...]
返回指定的一个或多个用户的组信息。

## lsSnapshottableDir
用法: hdfs lsSnapshottableDir [-help]





[1]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/CommandsManual.html#Overview
[2]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#User_Commands
[3]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#Administration_Commands
[4]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html
[5]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#fetchdt
[6]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#fsck

