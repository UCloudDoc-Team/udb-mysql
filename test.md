# 基准测试

{{indexmenu_n>50}}

基准测试（benchmark）是针对系统设计的一种压力测试，目标是为了掌握系统的行为。

sysbench作为一款优秀的MySQL基准测试工具为业界所认可。

本文应用sysbench在指定并发线程50、测试持续时间30分钟的条件下，对MySQL六种不同配置机型，四种不同数据量场景，进行了测试，得出了QPS和TPS数据。

## MySQL不同机型QPS数据

![image](/images/udb-mysql/qps.png)

## MySQL不同机型TPS数据

![image](/images/tps.png)

注意：

1、图中某些机型因硬盘容量限制不能满足某些测试场景，因而测试结果为空。如小型的600M\*30GB机型，就无法满足数据量为“60表\*600W条/表”数据量的场景。

2、测试详情：最大请求数：500,000,000，测试时长：1800s，测试脚本：test/db/oltp.lua。
