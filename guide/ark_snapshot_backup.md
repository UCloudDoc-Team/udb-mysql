## MySQL方舟快照备份

### 操作场景

UDB MySQL除了逻辑备份和物理备份以外，还支持方舟快照备份。方舟快照备份对源库影响小、速度快，对于某些实例较大、备份时间过长的情况，可以考虑开通快照备份。

### 功能限制

* 仅支持快杰实例。
* 开通方舟快照备份服务后，不支持修改备份方式。
* 产生的快照备份不支持下载。
* 手动备份方式只支持快照备份，如有其他备份方式需要，请联系技术支持。
* 快照备份会根据备份文件的大小进行收费。


### 操作步骤

**开启方舟快照备份服务**
进入实例的备份管理界面，点击立即开通。
![image](/images/open_ark_snapshot_service.png)


**获取快照自动备份策略**
进入实例的备份管理界面，获取自动备份策略。
![image](/images/ark_snapshot_backup_strategy.png)


**获取快照备份列表**
进入实例的备份管理界面，获取自动备份以及手动备份的备份列表。
![image](/images/ark_snapshot_backup_list.png)


**获取快照备份容量**
进入实例的备份管理界面，已经开通快照备份的会显示备份使用量。
![image](/images/ark_snapshot_backup_used.png)


**手动备份**
进入实例的备份管理界面，选择手动备份，进行手动快照备份。
![image](/images/ark_snapshot_backup_manual.png)


更详细计费项如官网
https://docs.ucloud.cn/udb-mysql/price
