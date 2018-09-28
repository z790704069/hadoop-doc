# 概述
Offline Image Viewer是一个将hdfs fsimage内容dump成人们易读格式文件的工具，并且提供只读的WEB API以方便对Hadoop集群命名空间的离线分析和检查。这个工具可以相对较快的处理非常大的文件。他可以处理Hadoop2.4以及以上版本。如果你想格式化老版本的，请使用Hadoop2.3上的Offline Image Viewer或者使用[oiv_legacy Command][1]。

[1]: http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsImageViewer.html#oiv_legacy_Command