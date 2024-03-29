# MySQL高可用

MySQL 高可用底层基于Binlog实现数据同步， 客户业务不能关闭Binlog， 否则会导致高可用异常。

## 自助修复高可用

对于高可用实例，存在HAHealthState监控指标，在实例正常情况下，该指标的值为0。在检测到实例出现问题，可能无法保证集群正常容灾的情况下,该指标会变为非0，非0意味高可用异常。

在高可用异常发生时，并且没有自动恢复的情况下，可以尝试以下步骤进行实例高可用的修复

1. 检查当前业务是否正常,业务不正常请联系技术支持
2. 业务正常的情况下，检查高可用备库的磁盘使用率，对应的监控项指标为HAStandyDBDiskUsage，如果发现高可用备库磁盘使用率100%，尝试在Binlog管理界面清理一部分Binlog，紧急释放空间，释放空间完成后，高可用状态一般会自动修复，正常后，根据业务情况，升级磁盘配置。
3. 释放空间后，等待2分钟, 如果依然高可用异常，并且HAHealthState是3,4的情况下, 尝试重做备库。重做备库会从主库导出数据进行数据恢复。重做发起前，会先进行重做条件的预检查，对于没有异常的实例，可以直接点击确定，开始重做。对于实例非事务引擎大小多于1G的情况(影响同步主库的锁表时间)，实例存在长事务的情况(有长事务会导致重做拿不到锁一直在重做中)，以及实例复制关系不正确的情况(不合理的复制关系将被抹除)，需要手动勾选强制，开始重做。

![image](/images/RemakeStandby.png)
![image](/images/ForceRemake.png)

