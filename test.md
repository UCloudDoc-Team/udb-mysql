# 基准测试

基准测试（benchmark）是针对系统设计的一种压力测试，目标是为了掌握系统的行为。
sysbench作为一款优秀的MySQL基准测试工具为业界所认可。
本文应用sysbench在不同并发线程、测试持续时间15分钟的条件下，对MySQL九种不同配置机型，五种不同数据量场景，进行了测试，得出了QPS和TPS数据。

## 测试环境
压测机器配置：32C64G，200G系统盘

sysbench版本：sysbench 1.0.17 (using system LuaJIT 2.0.4)

## 操作步骤
```plain
准备数据：
sysbench /usr/share/sysbench/oltp_read_write.lua --tables= --table-size= --mysql-user=root --mysql-password= --mysql-host= --mysql-port= --threads= --mysql-db= prepare
 
压测命令
/usr/share/sysbench/oltp_read_write.lua --tables= --table_size= --mysql-user=root --mysql-password= --mysql-host= --mysql-port= --mysql-db= --time=900 --threads= --report-interval=1 run
```


## 测试结果

## NVMe 型

![image](/images/qps_tps.png)

| <font style="color:#000000;">规格</font> | <font style="color:#000000;">单表数据量</font> | <font style="color:#000000;">表数量</font> | <font style="color:#000000;">sysbench线程数</font> | <font style="color:#000000;">QPS</font> | <font style="color:#000000;">TPS</font> | <font style="color:#000000;">avg_lst(95)</font> |
| --- | --- | --- | --- | --- | --- | --- |
| <font style="color:#000000;">2核4G</font> | <font style="color:#000000;">5000000</font> | <font style="color:#000000;">5</font> | <font style="color:#000000;">16</font> | <font style="color:#000000;">15714.38</font> | <font style="color:#000000;">785.72</font> | <font style="color:#000000;">35.59</font> |
| <font style="color:#000000;">4核8G</font> | <font style="color:#000000;">10000000</font> | <font style="color:#000000;">10</font> | <font style="color:#000000;">32</font> | <font style="color:#000000;">29618.14</font> | <font style="color:#000000;">1480.91</font> | <font style="color:#000000;">37.56</font> |
| <font style="color:#000000;">4核16G</font> | <font style="color:#000000;">40000000</font> | <font style="color:#000000;">20</font> | <font style="color:#000000;">32</font> | <font style="color:#000000;">26818.57</font> | <font style="color:#000000;">1340.93</font> | <font style="color:#000000;">40.37</font> |
| <font style="color:#000000;">4核32G</font> | <font style="color:#000000;">60000000</font> | <font style="color:#000000;">30</font> | <font style="color:#000000;">32</font> | <font style="color:#000000;">23470.87</font> | <font style="color:#000000;">1173.54</font> | <font style="color:#000000;">45.79</font> |
| <font style="color:#000000;">8核16G</font> | <font style="color:#000000;">40000000</font> | <font style="color:#000000;">20</font> | <font style="color:#000000;">64</font> | <font style="color:#000000;">46738.46</font> | <font style="color:#000000;">2336.92</font> | <font style="color:#000000;">45.79</font> |
| <font style="color:#000000;">8核32G</font> | <font style="color:#000000;">60000000</font> | <font style="color:#000000;">30</font> | <font style="color:#000000;">64</font> | <font style="color:#000000;">42252.57</font> | <font style="color:#000000;">2112.63</font> | <font style="color:#000000;">51.02</font> |
| <font style="color:#000000;">8核64G</font> | <font style="color:#000000;">60000000</font> | <font style="color:#000000;">64</font> | <font style="color:#000000;">64</font> | <font style="color:#000000;">39422.73</font> | <font style="color:#000000;">1971.14</font> | <font style="color:#000000;">53.85</font> |
| <font style="color:#000000;">16核32G</font> | <font style="color:#000000;">60000000</font> | <font style="color:#000000;">30</font> | <font style="color:#000000;">64</font> | <font style="color:#000000;">58015.18</font> | <font style="color:#000000;">2900.76</font> | <font style="color:#000000;">36.24</font> |
| <font style="color:#000000;">16核64G</font> | <font style="color:#000000;">60000000</font> | <font style="color:#000000;">64</font> | <font style="color:#000000;">64</font> | <font style="color:#000000;">46652.26</font> | <font style="color:#000000;">2332.61</font> | <font style="color:#000000;">49.21</font> |

