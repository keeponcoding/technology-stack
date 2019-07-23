* HDFS写流程
![45f422ee5ec7303bf3969a621b80bab4.png](evernotecid://2EB99A42-EE96-499C-93A4-4C877A924D9E/appyinxiangcom/19791940/ENResource/p28)
> 1.客户端通过对DistributedFileSystem对象调用create()来新建文件
> 2.DistributedFileSystem对namenode创建一个RPC调用，在文件系统的命名空间中新建一个文件，此时该文件中还没有相应的数据块
> 3.namenode执行各种不同的检查以确保这个文件不存在以及客户端有新建该文件的权限，如果这些检查均通过，namenode会创建新文件记录一条记录；否则，文件创建失败并向客户端抛出一个IOException异常。DistributedFileSystem向客户端返回一个FSDataOutputStream对象，由此客户端可以写入数据。就像读取事件一样，FSDataOutputStream封装一个DFSoutPutstream对象，该对象负责处理datanode和namenode之间的通信
> 4.在客户端写入数据时，DFSoutputStream将它分成一个个的数据包，并写入内部队列，称为“数据队列”(data queue)。DataStreamer处理数据队列，它的责任是挑选出适合存储数据副本的一组datanode，并据此来要求namenode分配新的数据块。这一组datanode构成一个**管线**(假设副本数为3，所以管线中有3个节点)。DataStreamer将数据包流式传输到管线中第1个datanode，该datanode存储数据包并将它发送到管线中的第2个datanode。同样，第2个datanode存储该数据包并且发送给管线中的第3个datanode
> 5.DFSOutputStream也维护着一个内部数据包队列来等待datanode的收到确认回执，称为“确认队列”(ack queue)，收到管道中所有datanode确认信息后，该数据包才会从确认队列删除
> 说明：如果任何datanode，在数据写入期间发生故障，则执行以下操作(对写入数据的客户端是透明的)。首先关闭管线，确认把队列中的所有数据包都添加回数据队列的最前端，以确保故障节点下游的datanode不会漏掉任何一个数据包。为存储在另一个正常datanode的当前数据块指定一个新的标识，并将该标识传送给namenode,以便故障datanode在恢复后可以删除存储的部分数据块。从管线中删除故障datanode，基于两个正常datanode构建一条新管线。余下的数据块写入管线中正常的datanode。namenode注意到块副本量不足时，会在另一个节点上创建一个新的副本。后续数据块继续正常接受处理。在一个块本写入期间可能会有多个datanode同时发生故障，但非常少见。只要写入了dfs.name.replication.min的副本数(默认为1)，写操作就会成功，并且这个块可以在集群中异步复制，直到到达其目标副本数(dfs.replication的默认值为3)
> 6.客户端完成数据的写入后，对数据流调用close()方法
> 7.该操作将剩余的所有数据包写入datanode管线，并在联系到namenode告知其文件写入完成之前，等待确认。

```
FsDataOutputStream.hflus(); //清理客户端缓冲区数据，被其他client立即可见
FsDataOutputStream.hsync(); //清理客户端缓冲区数据，被其他client不能立即可见
```

* 副本节点选择
> 第一个副本在client所处的节点上。如果客户端在集群外，随机选一个。
第二个副本和第一个副本位于相同机架，随机节点。
第三个副本位于不同机架，随机节点。
![0e4fc19f2bf164e0e2c1dbddea782c19.png](evernotecid://2EB99A42-EE96-499C-93A4-4C877A924D9E/appyinxiangcom/19791940/ENResource/p29)


* HDFS读取流程
![95c6aae05a59c21412bb2d8572152d9f.png](evernotecid://2EB99A42-EE96-499C-93A4-4C877A924D9E/appyinxiangcom/19791940/ENResource/p30)

> 1. client访问NameNode，查询元数据信息，获得这个文件的数据块位置列表，返回输入流对象。
> 2. 就近挑选一台datanode服务器，请求建立输入流 。
> 3. DataNode向输入流中中写数据，以packet为单位来校验。
> 4. 关闭输入流



