# 机型版本

### MySQL产品机型

云数据库UDB MySQL提供NVMe机型和SSD云盘机型，不管是哪一种机型，UDB-MySQL的可靠性、持久性和读写性能都会满足产品SLA承诺。

NVMe机型的数据库实例，采用业内主流的计算存储分离架构：计算层使用高性能UCloud快杰云主机，存储层采用超高性能RDMA NVMe SSD云盘。RSSD云盘基于新一代分布式块存储架构，底层以NVMe SSD为存储介质，网络传输使用RDMA，为用户提供单盘最高达120万的随机读写能力和更低的单路时延能力。IOPS与容量对应关系：磁盘大小在1T以下时IOPS优化为固定值50000，1T以上按照固定的分配算法计算IOPS，计算公式为：1800 + 50 * size(GB)，最大IOPS为120万。磁盘步长为10GB，支持大容量存储，最大容量可达32T。

SSD云盘机型的数据库实例，一般部署于未提供NVMe机型的可用区。采用计算与存储分离架构，底层存储采用SSD云盘。

云数据库MySQL提供高可用双主热备架构，支持跨可用区构架高可用，并支持跨可用区构建slave从库，实现可用区级别容灾，确保服务高可用。

### MySQL容量规格

NVMe机型和SSD云盘机型支持按照CPU和内存1:2、1:4、1:8三种配比形式选择计算规格，硬盘最大支持32TB。用户可以根据对云数据库的硬件需求自由选择。

##### CPU内存比 1:2

| <p align="left">CPU规格(核)</p> | <p align="left">2</p> | <p align="left">4</p> | <p align="left">8</p> | <p align="left">16</p> | <p align="left">32</p> | <p align="left">64</p> |
| ------------ | -- | --- | --- | --- | --- | --- |
| 内存规格(G) | 4 | 8 | 16 | 32 | 64 | 128 |

##### CPU内存比 1:4

| <p align="left">CPU规格(核)</p> | <p align="left">2</p> | <p align="left">4</p> | <p align="left">8</p> | <p align="left">16</p> | <p align="left">32</p> | <p align="left">64</p> |
| ------------ | -- | --- | --- | --- | --- | --- |
| 内存规格(G) | 8 | 16 | 32 | 64 | 128 | 256 |

##### CPU内存比 1:8

| <p align="left">CPU规格(核)</p> | <p align="left">2</p> | <p align="left">4</p> | <p align="left">8</p> | <p align="left">16</p> | <p align="left">32</p> | <p align="left">64</p> |
| ------------ | -- | --- | --- | --- | --- | --- |
| 内存规格(G) | 16 | 32 | 64 | 128 | 256 | 512 |

### MySQL容量规格(旧版)

|<p align="left">内存规格(G)</p> |<p align="left">2</p> | <p align="left">4</p> | <p align="left">8</p> | <p align="left">12</p>  | <p align="left">16</p> | <p align="left">24</p> | <p align="left">32</p> | <p align="left">48及以上</p> |
| --------- | -- | --- | --- | --- | --- | --- | ---- | ----- |
| 推荐硬盘规格(G) | 50 | 200 | 300 | 400 | 500 | 800 | 1000 | 2000~32000 |

### MySQL产品版本

NVMe机型MySQL，支持MySQL5.6/5.7/8.0和Percona5.6/5.7协议。

SSD云盘机型MySQL，支持MySQL5.7/8.0和Percona5.7协议。
