# 概要
所有的HDFS命令使用bin/hdfs脚本来调用。空参数运行该脚本将展示所有命令的介绍。

使用方法: hdfs [SHELL_OPTIONS] COMMAND [GENERIC_OPTIONS] [COMMAND_OPTIONS]

Hadoop有一个选项解析框架，它采用解析通用选项以及运行类。
|COMMAND_OPTIONS|Description|
|-|-|
|--config --loglevel|The common set of shell options. These are documented on the [Commands Manual][1] page.|
|GENERIC_OPTIONS|The common set of options supported by multiple commands. See the Hadoop [Commands Manual][1] for more information.|
|COMMAND   COMMAND_OPTIONS|Various commands with their options are described in the following sections. The commands have been grouped into [User Commands][2] and [Administration Commands][3].|

# 用户命令
对Hadoop集群用户有用的诸多命令。
## classpath
用法: hdfs classpath [--glob |--jar <path> |-h |--help]
|COMMAND_OPTION|Description|
|-|-|
|--glob|expand wildcards|
|--jar path|write classpath as manifest in jar named path|
|-h, --help|print help|

[1]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/CommandsManual.html#Overview
[2]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#User_Commands
[3]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html#Administration_Commands


