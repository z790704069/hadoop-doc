# 概述
离线Edits查看器是一个解析Edits日志文件的工具。目前的程序用于不同格式之间的转换，包括xml(比二进制文件更易读且容易修改)。

这个工具可以解析formats -18及以后的版本。这个工具只操作文件，并不需要Hadoop集群处于运行状态。

输入格式支持：
1、二进制
2、xml格式

输出格式：
1、二进制
2、xml格式
3、stats:打印统计数据

# 用法
## XML Processor
将edits文件转换成xml格式，并输出文件
```
bash$ bin/hdfs oev -p xml -i edits -o edits.xml
```
因为xml是默认的处理格式，所以上面命令也可以简写成如下
```
bash$ bin/hdfs oev -i edits -o edits.xml
```

输出格式大致如下：
```
?xml version="1.0" encoding="UTF-8"?>
   <EDITS>
     <EDITS_VERSION>-64</EDITS_VERSION>
     <RECORD>
       <OPCODE>OP_START_LOG_SEGMENT</OPCODE>
       <DATA>
         <TXID>1</TXID>
       </DATA>
     </RECORD>
     <RECORD>
       <OPCODE>OP_UPDATE_MASTER_KEY</OPCODE>
       <DATA>
         <TXID>2</TXID>
         <DELEGATION_KEY>
           <KEY_ID>1</KEY_ID>
           <EXPIRY_DATE>1487921580728</EXPIRY_DATE>
           <KEY>2e127ca41c7de215</KEY>
         </DELEGATION_KEY>
       </DATA>
     </RECORD>
     <RECORD>
   ...remaining output omitted...
```

## Binary Processor
xml格式文件转二进制
```
bash$ bin/hdfs oev -p binary -i edits.xml -o edits
```

## Stats Processor
统计edits日志文件中各个操作数量
```
bash$ bin/hdfs oev -p stats -i edits -o edits.stats
```

输出格式大致如下：
```
VERSION                             : -64
   OP_ADD                         (  0): 8
   OP_RENAME_OLD                  (  1): 1
   OP_DELETE                      (  2): 1
   OP_MKDIR                       (  3): 1
   OP_SET_REPLICATION             (  4): 1
   OP_DATANODE_ADD                (  5): 0
   OP_DATANODE_REMOVE             (  6): 0
   OP_SET_PERMISSIONS             (  7): 1
   OP_SET_OWNER                   (  8): 1
   OP_CLOSE                       (  9): 9
   OP_SET_GENSTAMP_V1             ( 10): 0
   ...some output omitted...
   OP_APPEND                      ( 47): 1
   OP_SET_QUOTA_BY_STORAGETYPE    ( 48): 1
   OP_INVALID                     ( -1): 0
```

# 选项

|Flag	|Description|
|:---|:---|
[-i ; --inputFile] input file|	Specify the input edits log file to process. Xml (case insensitive) extension means XML format otherwise binary format is assumed. Required.
[-o ; --outputFile] output file	|Specify the output filename, if the specified output processor generates one. If the specified file already exists, it is silently overwritten. Required.
[-p ; --processor] processor|	Specify the image processor to apply against the image file. Currently valid options are binary, xml (default) and stats.
[-v ; --verbose]|	Print the input and output filenames and pipe output of processor to console as well as specified file. On extremely large files, this may increase processing time by an order of magnitude.
[-f ; --fix-txids]|	Renumber the transaction IDs in the input, so that there are no gaps or invalid transaction IDs.
[-r ; --recover]	|When reading binary edit logs, use recovery mode. This will give you the chance to skip corrupt parts of the edit log.
[-h ; --help]	|Display the tool usage and help information and exit.

# 案例研究：Hadoop集群恢复
当Hadoop集群出现问题，比如edits日志文件损坏但还是有部分是正常的。这个时候，我们可以使用该工具将二进制文件转换成xml格式，然后对xml格式进行修改，最终将xml文件转回二进制。如果edits文件缺少结束标识（以opCode -1结束），工具执行时会意识到这一点，通常格式化也将终止。

如果xml文件中没有结束标识，我们可以在最后一个正确的操作记录后面加上结束标识。任何在“opCode -1”的操作都会被忽略。

结束标识示例（带有 opCode -1）：
```
<RECORD>
    <OPCODE>-1</OPCODE>
    <DATA>
    </DATA>
  </RECORD>
```




