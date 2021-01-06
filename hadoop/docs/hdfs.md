# HDFS 相关

##功能
hdfs是hadooop分布式大数据存储系统，用于存储hadoop中的数据

##主要角色
###_namenode:_
`大数据集群中有两个namenode，一主一备，主NameNode对外提供服务，备NameNode同步主NameNode元数据，以待切换，所有DataNode同时向两个NameNode汇报数据块信息`<br>       
1.接收读写请求，所有的读写操作都要经过namenode<br>
2.保存分布式系统的元数据信息，包括文件归属和权限，文件由几个block块<br>
3.block块的位置<br>
4.fsimage：是内存命名空间元数据在外存的镜像文件<br>
  editlog：则是各种元数据操作的 write-ahead-log 文件，在体现到内存数据变化前首先会将操作记入 editlog 中，以防止数据丢失。<br>
###_SecondaryNameNode:_
hadoop1.x中的，不做过多阐述
###_datanode:_
datanode负责存储数据的block块，接收namenode发来的读写请求，进行读写数据<br>
[DN故障处理](./hadoop/pictures/dn_error.jpg)
###_[block](./hadoop/pictures/duplication_strategy.jpg):_<br>
hdfs上存储数据的最小单元，每份数据文件可以被切分成一个或多个不同的block块，每个不同的block会有副本
###_journalnode:_
负责同步active namenode和standby namenode之间的editlog，便于hdfs保持高可用

##读写流程
[图解hdfs读数据](./hadoop/pictures/read.jpg)<br>
[图解hdfs写数据1](./hadoop/pictures/write1.jpg)<br>
[图解hdfs写数据2](./hadoop/pictures/write2.jpg)<br>
[图解hdfs写数据3](./hadoop/pictures/write3.jpg)<br>

[hdfs读写故障](./hadoop/pictures/rw_error.jpg)
