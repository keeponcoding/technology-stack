* 启动zk，查看zk运行状况，出现异常，报错信息：  
```org.apache.zookeeper.server.persistence.FileTxnSnapLog$SnapDirContentCheckException: Snapshot directory has log files. Check if dataLogDir and dataDir configuration is correct.```
 > * 解决方案：删除zoo.cfg  配置文件中配置的 data log 文件夹下面的 version 重新运行即可  
 > * 原因分析：配置文件zoo.cfg中 第一次启动时没有配置 dataLogDir，会有个默认值，之后配置了 dataLogDir 重新启动，但是没有删除其默认的版本信息，导致新创建的dataLogDir与默认的log存在 文件冲突
 
 * ...
 
 
 