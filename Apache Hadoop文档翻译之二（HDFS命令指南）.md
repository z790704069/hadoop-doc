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

|COMMAND_OPTION|	Description|
|:-|:-|
|-help|	print help|

返回快照目录列表。当使用超级用户运行时，会返回所有快照目录，否则返回属于该用于的快照目录。

## jmxget
用法: hdfs jmxget [-localVM ConnectorURL | -port port | -server mbeanserver | -service service]

|COMMAND_OPTION	|Description|
|:-|:-|
|-help|	print help|
|-localVM ConnectorURL	|connect to the VM on the same machine|
|-port mbean server port|	specify mbean server port, if missing it will try to connect to MBean Server in the same VM|
|-server|	specify mbean server (localhost by default)|
|-service NameNode\|DataNode	|specify jmx service. NameNode by default.|

从服务中dump JMX信息

## oev
用法: hdfs oev [OPTIONS] -i INPUT_FILE -o OUTPUT_FILE

必需的命令行参数：

|COMMAND_OPTION	|Description|
|:-|:-|
|-i,--inputFile arg|	edits file to process, xml (case insensitive) extension means XML format, any other filename means binary format|
|-o,--outputFile arg|	Name of output file. If the specified file exists, it will be overwritten, format of the file is determined by -p option|

可选命令行参数：

|COMMAND_OPTION	|Description|
|:-|:-|
|-f,--fix-txids|	Renumber the transaction IDs in the input, so that there are no gaps or invalid transaction IDs.|
|-h,--help	|Display usage information and exit|
|-r,--recover|	When reading binary edit logs, use recovery mode. This will give you the chance to skip corrupt parts of the edit log.|
|-p,--processor arg|	Select which type of processor to apply against image file, currently supported processors are: binary (native binary format that Hadoop uses), xml (default, XML format), stats (prints statistics about edits file)|
|-v,--verbose|	More verbose output, prints the input and output filenames, for processors that write to a file, also output to screen. On large image files this will dramatically increase processing time (default is false).|

Hadoop离线编辑查看器。 有关详细信息，请参阅[Offline Edits Viewer Guide][7]

## oiv
用法: hdfs oiv [OPTIONS] -i INPUT_FILE

必需的命令行参数：

|COMMAND_OPTION|	Description|
|:-|:-|
|-i|--inputFile input file|	Specify the input fsimage file (or XML file, if ReverseXML processor is used) to process.|

可选命令行参数：


|COMMAND_OPTION	|Description|
|:-|:-|
|-o,--outputFile output file|	Specify the output filename, if the specified output processor generates one. If the specified file already exists, it is silently overwritten. (output to stdout by default) If the input file is an XML file, it also creates an <outputFile>.md5.|
|-p,--processor processor	|Specify the image processor to apply against the image file. Currently valid options are Web (default), XML, Delimited, FileDistribution and ReverseXML.|
|-addr address|	Specify the address(host:port) to listen. (localhost:5978 by default). This option is used with Web processor.|
|-maxSize size|	Specify the range [0, maxSize] of file sizes to be analyzed in bytes (128GB by default). This option is used with FileDistribution processor.|
|-step size	|Specify the granularity of the distribution in bytes (2MB by default). This option is used with FileDistribution processor.|
|-format|	Format the output result in a human-readable fashion rather than a number of bytes. (false by default). This option is used with FileDistribution processor.|
|-delimiter arg	|Delimiting string to use with Delimited processor.|
|-t,--temp temporary dir|	Use temporary dir to cache intermediate result to generate Delimited outputs. If not set, Delimited processor constructs the namespace in memory before outputting text.|
|-h,--help|	Display the tool usage and help information and exit.|

针对image文件的hadoop离线查看器（hadoop2.4及以上版本）。详见 [Offline Image Viewer Guide][8]

## oiv_legacy

用法: hdfs oiv_legacy [OPTIONS] -i INPUT_FILE -o OUTPUT_FILE

|COMMAND_OPTION|	Description|
|:-|:-|
|-i,--inputFile input file|	Specify the input fsimage file to process.|
|-o,--outputFile output file	|Specify the output filename, if the specified output processor generates one. If the specified file already exists, it is silently overwritten|

可选命令行参数：

|COMMAND_OPTION|	Description|
|:-|:-|
|-p\|--processor processor|	Specify the image processor to apply against the image file. Valid options are Ls (default), XML, Delimited, Indented, FileDistribution and NameDistribution.|
|-maxSize size|	Specify the range [0, maxSize] of file sizes to be analyzed in bytes (128GB by default). This option is used with FileDistribution processor.|
|-step size	|Specify the granularity of the distribution in bytes (2MB by default). This option is used with FileDistribution processor.|
|-format|	Format the output result in a human-readable fashion rather than a number of bytes. (false by default). This option is used with FileDistribution processor.|
|-skipBlocks|	Do not enumerate individual blocks within files. This may save processing time and outfile file space on namespaces with very large files. The Ls processor reads the blocks to correctly determine file sizes and ignores this option.|
|-printToScreen	|Pipe output of processor to console as well as specified file. On extremely large namespaces, this may increase processing time by an order of magnitude.|
|-delimiter arg	|When used in conjunction with the Delimited processor, replaces the default tab delimiter with the string specified by arg.|
|-h\|--help	|Display the tool usage and help information and exit.|

针对老版本的image文件的hadoop离线查看器。详见[HDFS Snapshot Documentation][9]

## version
用法: hdfs version
打印版本。

# 管理命令
对Hadoop集群管理员有用的诸多命令。

## balancer
用法：
```
hdfs balancer
          [-policy <policy>]
          [-threshold <threshold>]
          [-exclude [-f <hosts-file> | <comma-separated list of hosts>]]
          [-include [-f <hosts-file> | <comma-separated list of hosts>]]
          [-source [-f <hosts-file> | <comma-separated list of hosts>]]
          [-blockpools <comma-separated list of blockpool ids>]
          [-idleiterations <idleiterations>]
          [-runDuringUpgrade]
```

|COMMAND_OPTION	|Description|
|:-|:-|
|-policy <policy>|atanode (default): Cluster is balanced if each datanode is balanced.blockpool: Cluster is balanced if each block pool in each datanode is balanced.|
|-threshold <threshold>|Percentage of disk capacity. This overwrites the default threshold.|
|-exclude -f \<hosts-file\> \| \<comma-separated list of hosts\> | Excludes the specified datanodes from being balanced by the balancer.|
|-include -f \<hosts-file\> \| \<comma-separated list of hosts\>|Includes only the specified datanodes to be balanced by the balancer.|
|-source -f \<hosts-file\> \| \<comma-separated list of hosts\>|Pick only the specified datanodes as source nodes.|
|-blockpools \<comma-separated list of blockpool ids\>|The balancer will only run on blockpools included in this list.|
|-idleiterations \<iterations\>|Maximum number of idle iterations before exit. This overwrites the default idleiterations(5).|
|-runDuringUpgrade|	Whether to run the balancer during an ongoing HDFS upgrade. This is usually not desired since it will not affect used space on over-utilized machines.|
|-h|--help|Display the tool usage and help information and exit.|






[1]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/CommandsManual.html#Overview
[2]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#User_Commands
[3]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#Administration_Commands
[4]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/FileSystemShell.html
[5]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#fetchdt
[6]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#fsck
[7]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsEditsViewer.html
[8]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsImageViewer.html
[9]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Get_Snapshots_Difference_Report