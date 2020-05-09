# 产品架构

UCloud云数据库MySQL高可用版架构：

![image](/images/ha-mysql2020.png)

容灾原理：

1>高可用UDB采用双主架构，通过Semi-Sync实现数据同步

2>UDB可用性管理模块实时监控底层节点可用性，一旦监测到Master DB不可用，则触发自动进行容灾、漂移VIP至Standby DB，保证用户UDB数据库服务的稳定可靠；

3>对于用户好处：单一vip接入、应用层无缝切换，整个过程中用户不需要任何人工干预和配置修改。
