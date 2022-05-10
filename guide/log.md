

# MySQL日志管理

## 日志分类

MySQL实例的日志包括二进制日志（BinLog）、慢查询日志（SlowLog）和错误日志（ErrorLog）三种。

二进制日志（BinLog）是记录对数据发生或潜在发生更改的SQL语句，并以二进制的形式保存。

慢查询日志（SlowLog）是指MySQL实例记录所有执行超过long\\\_query\\\_time参数设定的时间阈值的SQL语句的日志。

错误日志（ErrorLog）是记录MySQL实例服务进程mysqld在启动/关闭或运行过程中遇到的错误信息。

## 日志下载

在详情页选择日志管理，在页面中选择日志包，即可打包慢查询日志（SlowLog）和错误日志（ErrorLog），并可下载。

![image](/images/管理日志0.png)

在详情页选择备份管理，在页面中选择Binlog，即可打包二进制日志（BinLog）、清理日志，并可下载和删除。

![image](/images/binlog.png)

![image](/images/打包binlog.png)

## 清理日志

清理日志操作仅适用于MySQL实例主库（Master）的二进制日志（BinLog）的清理。

在“详情”页选择“备份管理”，在左侧导航栏中选择“Binlog日志包”，点击“清理日志”。

![image](/images/cleanup-binlog.png)

在清理日志弹窗页选择清理的时间，为了保证数据库同步安全，目前仅支持清理到当前时间前一天的日志。

## 查询日志

MySQL支持控制台查询历史日志和实时日志，具体操作：在实例详情页面中，选择日志管理，选择日志类型和查询类型，点击查询日志：

![image](/images/查询日志.png)
