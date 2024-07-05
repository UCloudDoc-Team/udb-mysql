

# MySQL日志管理

## 日志分类

MySQL实例的日志包括二进制日志（BinLog）、慢查询日志（SlowLog）和错误日志（ErrorLog）三种。

二进制日志（BinLog）是记录对数据发生或潜在发生更改的SQL语句，并以二进制的形式保存。

慢查询日志（SlowLog）是指MySQL实例记录所有执行超过long\\\_query\\\_time参数设定的时间阈值的SQL语句的日志。

错误日志（ErrorLog）是记录MySQL实例服务进程mysqld在启动/关闭或运行过程中遇到的错误信息。

## 查询日志

MySQL支持控制台查询历史日志和实时日志，具体操作：在实例详情页面中，选择日志管理，选择日志类型和查询类型，点击查询日志：

![image](/images/查询日志.png)

## 日志下载

在详情页选择日志管理，在页面中选择日志包，即可打包慢查询日志（SlowLog）和错误日志（ErrorLog），并可下载。
高可用实例支持下载主备双节点日志。
> 控制台看到的慢查询日志是原生日志， 下载的慢查询日志是经过pt-query-digest处理后的日志。

![image](/images/管理日志0.png)

在详情页选择备份管理，在页面中选择Binlog，即可打包二进制日志（BinLog）、清理日志，并可下载和删除。

![image](/images/binlog.png)

![image](/images/打包binlog.png)

## 清理Binlog日志

在“详情”页选择“备份管理”，再选择“Binlog日志设置”，点击“清理本地日志”。

![image](/images/guide/binlog_cleanup_entry.png)

![image](/images/BinlogTime.png)

清理日志会先进行可清理时间的检查，返回可清理的开始时间，结束时间以及安全时间。开始时间为实例可清理第一个Binlog的时间，对于高可用实例，会综合显示两个节点最早的时间。结束时间为可进行Binlog清理的最晚时间，会综合考虑实例保留两个Binlog的极限清理时间，以及高可用未同步Binlog的时间，从库未同步Binlog的时间。安全时间为Binlog可安全清理的时间，不影响DB的回档功能，目前只有使用Binlog转储功能，该时间才有意义。


