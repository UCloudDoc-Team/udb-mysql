{{indexmenu_n>6}}

# 性能优势

读写分离中间件的核心价值，在于添加从节点后，数据库读性能有显著提高，从节点的读请求处理能力，能够得到充分发挥。 如果做不到这一点，中间件的
MySQL兼容度再好， 管理功能再强大，也无法满足业务的本质需求。

UDB读写分离中间件，通过精心的结构设计，推敲和打磨每一行跟性能相关的代码，
实现了让读性能随从节点数量线性增长：增加相应数量的从节点，数据库的读性能也能够随之线性增长。
从这个意义上讲，使用UDB读写分离中间件， 能够将客户所购买的从节点的每一点性能都压榨出来，杜绝浪费。而这一点，是业内绝大多数中间件无法做到的。

## 读请求QPS随节点数线性增长

![image](/images/udbrw_detail.png)

从上图可以看到， 采用Sysbench测试程序，在测试线程数\>=128个（保证测试压力足够）的情况下， 读QPS随着从节点数增加而线性增加：
只有1个主节点时QPS最高能到5万，1主1从QPS能够到10万， 1主2从时QPS峰值为15万。

为了能够更加直观地说明：让读性能随从节点数量线性扩展 这一效果， 我们从上图中， 分别挑选1主0从， 1主1从，
1主2从三种配置下的最高读QPS，得到以下性能曲线：

![image](/images/udbrw_line.png)

从图中可以看出， 读QPS随着节点数的增加，呈现几乎完美的线性增长。

## 和ProxySQL的对比测试

ProxysQL是业内一款著名的数据库中间件，主要功能有读写分离、数据库管理、Cache等，
为国内外大量DBA所喜爱。甚至在某种程度上，ProxySQL是开源读写分离中间件的第一选择。

产品发布之前，为了搞清楚UDB读写分离中间件在读性能上离业内标杆还有多大差距， 我们做了两个产品的读性能对比测试。出乎意料的是，
我们发现在读性能上，UDB读写分离中间件几乎完胜ProxySQL：

![image](/images/udbrw_comp.png)

从测试结果看， 对于配置完全一样的两个后端数据库节点，UDB读写分离中间件读性能峰值能够到10w QPS， 而ProxySQL最高仅为7.5w
QPS，
两者之间有25%的差距（但是ProxySQL消耗的CPU比UDB读写分离中间件低，由于瓶颈在后端数据库节点IO上，因此两个中间件都没有跑完测试机的CPU）。

考虑到ProxySQL采用C++开发，
而UDB读写分离中间件采用Go语言开发，这个结果让我们感到惊讶。我们仔细检查了测试方法并进行多次测试，得到的结果是一样的。

从这次测试我们得到的结论是：一般而言，C++由于对底层更具掌控力，在性能上相较于Go会有更多的优势，但这也并不是绝对的。性能往往更多地取决于模块设计是否科学，代码是否经过精心优化，以及是否能够充分利用多核CPU强劲的计算能力。

具体的测试方法如下：

| 类别       | 名称                                           |
| -------- | -------------------------------------------- |
| 测试程序     | Sysbench 1.1.0                               |
| 测试机器     | 测试机器                                         |
| 读写分离中间件  | 双活两节点，每个节点配置：4GB内存 / CPU不限                   |
| ULB      | 复用标准的ULB产品，无特殊配置                             |
| UDB      | 主从节配置均为：6GB内存 / 200GB SSD / CPU不限 / MySQL5.6 |
| 数据量      | 5张表，每个表5000w                                 |
| ProxySQL | 单节点，每个节点配置：8GB内存 / CPU不限                     |

建表语句：
```
CREATE TABLE `sbtest1` (`id` int(10) unsigned NOT NULL,`k` int(10) unsigned NOT NULL DEFAULT '0',`c` char(120) NOT NULL DEFAULT '',`pad` char(60) NOT NULL DEFAULT '',PRIMARY KEY (`id`),KEY `k_1` (`k`)) ENGINE=InnoDB DEFAULT CHARSET=utf8 MAX_ROWS=1000000
```
测试命令：
```
./src/sysbench --db-driver=mysql \
--mysql-table-engine=innodb \
--mysql-host=10.9.99.169  --mysql-port=3306 --mysql-user=root --mysql-password="liuly624@cloud" --mysql-db=sbtest \
--oltp-tables-count=5 --oltp-table-size=50000000 --report-interval=2 --max-requests=0 --time=300 --threads=128 \
--rand-init=on --rand-type=special --rand-spec-pct=5 --percentile=99 --oltp_auto_inc=off \
--test=/data/sysbench/tests/include/oltp_legacy/select.lua run
```
具体的测试步骤是：

1. 分别采用64、128、192、256线程，直连UDB主节点进行压测，记录QPS；

2. 分别采用64、128、192、256线程，连接读写分离中间件，后端配置1主0从共1个UDB节点进行压测， 记录QPS；

3. 分别采用64、128、192、256线程，连接读写分离中间件，后端配置1主1从共2个UDB节点进行压测， 记录QPS（注：
主从节点的配置完全一致，下同）；

4. 分别采用64、128、192、256线程，连接读写分离中间件，后端配置1主2从共3个UDB节点进行压测， 记录QPS。

5. 分别采用64、128、192、256线程，连接ProxySQL中间件， 后端配置1主2从共3个节点，记录QPS。
我们在测试时发现，由于ProxySQL是以SQL语句为粒度进行转发，而且转发目的是以group
为单位，主从节点不在同一个group。因此虽然后端配置了3个节点，实际利用的是2个从节点。因此，在上面性能测试结果最后一张图中，这次测试只体现ProxySQL后端接2个从节点的性能。
