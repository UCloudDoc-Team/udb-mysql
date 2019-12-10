# FAQs



## 如何访问MySQL实例？

**phpMyAdmin操作**

选择需要访问的MySQL实例，点击列表右侧的“登录”，跳转至phpMyAdmin页面，然后输入管理员名称和密码，即可访问MySQL实例。

**命令行操作**

通过MySQL Client访问，登录云主机，在命令行中输入：

    mysql -h$IP -P$Port -u$User -p$Password

$IP指定MySQL实例的内网IP地址。

$Port指定MySQL实例的端口。

$User指定MySQL实例的管理员名称。

$Password指定MySQL实例的管理员密码。

MySQL实例仅支持通过云主机进行内网登陆。

## MySQL实例的安全性如何？

**访问安全性**

MySQL实例仅支持通过云主机进行内网登陆且按账户进行隔离，因此仅有同一账户的云主机能够对MySQL实例进行登录。

MySQL实例是强制鉴权的，只能通过认证的管理员账户才能访问。

**数据安全性**

所有的MySQL实例的数据文件所存放的硬盘都进行了RAID1保护。

MySQL实例每天都会进行数据备份，同时也提供了手工备份功能，以便用户能够在特定时间点主动对数据进行备份。

MySQL实例（Master）支持创建从库（搭建在与主库不同的物理机上），从库与主库自动进行数据同步，提供数据灾备的能力。

## 如何向MySQL实例中dump数据？

**phpMyAdmin操作**

登录phpMyAdmin，切换到“导入”页面，选择本地的数据文件进行导入。

**命令行操作**

将需要导入的数据文件上传至云主机，在云主机中执行如下命令：

    mysql -h$IP -P$Port -u$User -p$Password < data.sql

data.sql是指用户备份文件。

由于通过控制台导入数据有文件大小的限制，因此对于大文件，建议用户使用命令行导入数据。

注意：

Dump源数据库时，仅导出业务相关的库表，请过滤掉mysql/information\_schema/performance\_schema基础库。

若全库导入，会覆盖MySQL实例原有基础库，影响后续使用。

如果需要导入的数据中存在View或存储过程，需替换掉sql文件里的user@host为拥有正确权限的用户，如’root’@’%’，否则可能无法导入和正常使用。

## 如何从MySQL实例中dump数据？

以下操作不会锁库：

    mysqldump -h$IP -P$Port -u$User -p$Password --quick --routines --single-transaction --databases db1 db2 db3 > data.sql

single-transaction可以保证InnoDB表的一致性，但MyISAM表无法保证一致性。如果有MyISAM表，可在业务低谷时，进行锁库导出：

    mysqldump -h$IP -P$Port -u$User -p$Password --master-data=2 -l --databases db1 db2 db3 > data.sql

注意：dump导出时仅导出业务相关的库表。

## 如何创建新用户并授予权限？

**phpMyAdmin操作**

登录phpMyAdmin，点击添加用户，输入用户名、域和密码；如果域为’%’，在主机一栏选择“任意主机”;
如果指定某网段，选择“使用文本域”，然后输入对应的值，再勾选所需权限，完成后，点击“添加用户”即可。

**命令行操作**

命令行操作请使用grant命令。

例：增加与root同样权限的用户”<test@%>”，密码123456。

用户使用root账号登陆云数据库，执行show grants，得到root权限语句：

``` 
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,RELOAD,PROCESS,REFERENCES,INDEX,ALTER,SHOW DATABASES,CREATE TEMPORARY TABLES,LOCK TABLES,EXECUTE,REPLICATION SLAVE,REPLICATION CLIENT,CREATE VIEW,SHOW VIEW,CREATE ROUTINE,ALTER ROUTINE,CREATE USER,EVENT,TRIGGER ON *.* TO 'root'@'%' IDENTIFIED BY PASSWORD '*xxxxxx' WITH GRANT OPTION 
```

将该语句复制下来，然后替换语句中的用户名、域和密码串，执行修改后的授权语句：

    GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,RELOAD,PROCESS,REFERENCES,INDEX,ALTER,SHOW DATABASES,CREATE TEMPORARY TABLES,LOCK TABLES,EXECUTE,REPLICATION SLAVE,REPLICATION CLIENT,CREATE VIEW,SHOW VIEW,CREATE ROUTINE,ALTER ROUTINE,CREATE USER,EVENT,TRIGGER ON *.* TO 'test'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;

刷新权限表：

``` 
flush privileges; 
```

## 如何查看云数据库运行状态？

**控制台操作**

在MySQL实例管理页的列表中选择需查看的MySQL实例，右侧详情页中"性能监控"会展示CPU使用率、磁盘使用率、内存大小、内存使用率、连接数、QPS等监控数据。

**命令行操作**

查看云数据库运行状况：
```
    show status
```
查看InnoDB引擎运行状况：

``` 
show engine innodb status\G  
```

**phpMyAdmin操作**

登录phpMyAdmin，选择“状态”\>”查询统计”，查看数据库的请求频率以及分布情况。

## QPS是如何统计的？

每分钟都进行一次QPS采样计算，详情页性能监控所展示的曲线为5分钟粒度，若计算所得值小于1，则显示为0。

## 如何查看MySQL实例的SlowLog?

**命令行操作**

为了便于用户查看SlowLog，MySQL实例设置了log\_output=table，会将SlowLog写入mysql.slow\_log表中。如果需要查看SlowLog，可以执行：

    select * from mysql.slow_log where start_time >= 'xxxx-xx-xx';

## 如何修改MySQL实例的配置参数？

MySQL实例允许用户导入自定义配置文件对云数据库的相关参数进行修改。具体操作参考操作指南的配置文件章节。

## 如何安装和卸载插件？

MySQL实例支持安装插件和卸载插件的功能，目前仅开放handlersocket。

**命令行操作**

执行以下语句安装插件：
```
    install plugin handlersocket soname 'handlersocket.so';
```
查看当前已安装的插件列表：
```
    show plugins;
```
卸载插件：
```
    uninstall plugin handlersocket;
```
## 如何使用MySQL-Proxy使MySQL实例可以通过外网访问？

鉴于MySQL实例不能通过外网IP直接访问，可使用MySQL-Proxy将MySQL实例跳转至云主机（UHost）的端口进行访问。

在云主机（UHost）安装MySQL-Proxy：
```
    yum install mysql-proxy
```
安装结束后，可通过如下命令查看相关信息：

``` 
mysql-proxy -V 
```

查看MySQL-Proxy帮助选项：
```
    mysql-proxy -help -all
```
MySQL-Proxy默认端口为4040，通过访问4040端口就可以访问3306端口。

使用命令行开启MySQL-Proxy，步骤如下：
```
    touch /etc/mysql-proxy.cnf
    
    vim /etc/mysql-proxy.cnf
```
输入如下内容：
```
    [mysql-proxy]
    
    admin-username=root    #admin用户名
    
    admin-password=123fsck    #admin密码
    
    admin-lua-script=/usr/lib64/mysql-proxy/lua/admin.lua    #lua位置，参见上面的版本信息
    
    daemon=true        # mysql-proxy以守护进程方式运行
    
    keepalive=true        #保持连接启动进程会有2个， 一号进程用来监视二号进程
    
    proxy-backend-addresses=10.6.X.XX  #目标地址，udb内网地址，默认端口3306
    
    log-file=/var/log/mysql-proxy.log
    
    log-level=debug
```
配置文件保存后需要改变权限：
```
    chmod 0660 /etc/mysql-proxy.cnf
```
启动：
```
    mysql-proxy --defaults-file=/etc/mysql-proxy.cnf
```
使用kill命令可以将程序终止。

在外网环境测试：
```
    mysql -h$uhost_ip -P4040 -u$User -p$Password
```
$uhost\_ip为UHost的外网IP。

注意：

需要在UCloud管理控制台中，打开云主机（UHost）的4040端口。

## 如何查看MySQL实例的各项监控指标？

进入MySQL实例二级详情页，概览页面的“性能监控”中可以查询实例的各个监控项。

![image](/images/62.png)

## 是否可以查看云数据库运行状态？

可以。以下以部分语句作为举例：

查看当前连接状态：
```
    show full processlist
```
查看从库同步状态：

``` 
show slave status \G  
```

查看InnoDB状态：
```
    show engine innodb status\G
```
查看当前参数设置，如wait\_timeout：
```
    show global variables like '%wait_timeout%';
```
## 在phpMyAdmin上操作DROP数据库为什么被禁止？

由于phpMyAdmin的保护机制，DROP语句是被禁用的。如果需要在phpMyAdmin删除整个库，需要按如下步骤操作：

![image](/images/63.png)

选中相应的数据库

![image](/images/64.png)

点击“删除”后，会弹出确认提示，然后才会正常删除。

## 默认的配置是针对哪种存储引擎进行优化的？

针对InnoDB优化的，由于MyISAM的并发性能较差，建议使用InnoDB。

## 如何在云主机上搭建云数据库从库并进行主从同步呢？

可以在云主机搭建从库，与云数据库进行主从同步。

具体步骤如下：

1、云数据库导出数据：

``` 
mysqldump -hxxx -uxxx --quick --routines --master-data=2 --single-transaction --databases db1 db2 db3 > data.sql    
```

对于使用MyISAM存储引擎库表使用：
```
    mysqldump -hxxx -uxxx --master-data=2 -l --databases db1 db2 db3 > data.sql
```
2、导入数据：
```
    mysql -hxxx -uxxx -p < data.sql
```
3、设置同步。

第一步：从库配置文件修改（my.cnf）在\[mysqld\]下增加参数server-id=1（值与主库不一样即可）

执行以下命令，使修改生效。
```
    service mysqld reload
```
第二步：

登陆到主库中授权，执行：
```
    grant replication slave on *.* to username@”x.x.x.x” identified by “xxxx”;
    
    flush privileges;
```
第三步：

在data.sql中找到change master to，如：
```
    CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.000026',MASTER_LOG_POS=33268716;
```
将此语句复制后，登录从库，并补齐为如下命令 :
```
    CHANGE MASTER TO MASTER_LOG_FILE='mysql-bin.xxx',MASTER_LOG_POS=xxx,MASTER_HOST='X.X.X.X',
    
    MASTER_PORT=XXX,MASTER_USER='XXX',MASTER_PASSWORD='xxx';
```
PORT默认3306时可以不要指定，然后执行slave start。

查看主从是否建立成功，可以使用：
```
    show slave status\G;
```
## 在云主机上使用wget下载云数据库的Log时报错，如何解决？

在云主机上下载云数据库的Log备份时需要在url前后加上双引号。

例：下载地址为：<http://udbbackup.ufile.ucloud.cn/udb-3u022a/>
```
    wget -O ppp.tgz "http://udbbackup.ufile.ucloud.cn/udb-3u022a/"
```
\-O 设置本地名称

## 如何正确设置字符集？

UCloud建议用户将所有的字符集都设置为UTF-8，因为utf8兼容绝大部分字符，而统一的字符集设置是避免出现乱码最有效的方式，并且后台的mysqldump等备份工作我们也是统一使用的utf8字符集，

并没有检测服务器端的字符集设置，其他的字符集设置可能会导致备份乱码。如果您的项目中有使用到emoji表情，那么建议您将所有的字符集设置成utf8mb4(5.6版本以后支持)。

另外，UDB不支持设置字符集的校验规则，采用的是对应字符集的默认校验规则，除非您自行在MySQL终端设置(我们非常不建议您这么做)。

出现乱码的原因大概有两个，一是存入的字符集(character\_set\_client)和取出的字符集(character\_set\_results)不一致，那么必然会导致乱码；二是虽然存入和取出的字符集一致，但期间有字符集转换，

但是这个转换过程并非无损的，那么也有可能导致乱码。所以我们建议您的程序在登陆UDB时，以utf8为例，先通过这个命令set names
utf8来保证character\_set\_client，character\_set\_connection和character\_set\_results均为utf8，

并且在配置文件中设置character\_set\_server，character\_set\_database，character\_set\_system均为utf8，在表定义时就可以不指定字符集而采用默认的utf8了，这样就可以完全杜绝乱码问题了。

## 如何查询MySQL实例的客户端和服务器端版本

登陆MySQL，执行命令s，首行显示客户端版本，Server version显示服务器端版本。
```
    mysql> s
    
    ./mysql Ver 14.14 Distrib 5.6.20-ucloudrel1, for Linux (x86_64) using EditLine wrapper
    
    Connection id: 40491
    
    Current database:
    
    Current user: <ucloudbackup@115.239.196.104>
    
    SSL: Not in use
    
    Current pager: stdout
    
    Using outfile: ''
    
    Using delimiter: ;
    
    Server version: 5.5.24-ucloudrel1-log Source distribution
    
    Protocol version: 10
    
    Connection: 114.119.41.82 via TCP/IP
    
    Server characterset: utf8
    
    Db characterset: utf8
    
    Client characterset: utf8
    
    Conn. characterset: utf8
    
    TCP port: 3370
    
    Uptime: 3 days 8 hours 42 min 18 sec
```
## MySQL数据库主从同步延迟是如何产生的？

数据库的主从不一致的情况如下:

网络延迟:由于mysql主从复制是基于binlog的一种异步复制，通过网络传送binlog文件，网络延迟是主从不同步最多的诱因，特别是跨机房的数据同步出现这种几率非常的大，所以做读写分离，从业务层进行前期设计。

主从两台机器的负载不一致:由于mysql主从复制是主上面启动1个io线程，而从上面启动1个sql线程和1个io线程，当中任何一台机器的负载很高可能会导致其中的任何一个线程出现资源不足，出现主从不一致的情况。

max_allowed_packet设置不一致：主上面设置的max_allowed_packet比从大，当大的sql语句能在主上面执行完毕，从上面设置过小，无法执行，导致的主从不一致。

key自增键：开始的键值跟自增步长设置不一致引起的主从不一致。

mysql异常宕机：如果未设置sync\_binlog=1(默认为0:执行的语句向二进制日志一次不同步到硬盘,性能最好,宕机丢数据多;1:每写一次二进制日志都要与硬盘同步,性能最差,宕机丢数据少)或者innodb\_flush\_log\_at\_\trx\_commit=1(默认为1:每一次事务提交都需要把日志刷新到硬盘,性能差,丢数据少;2:写入缓存,日志每隔一秒刷新到硬盘,性能好,丢数据多)很有可能出现binlog或者relaylog文件出现损坏,导致主从不一致;

mysql本身的bug引起的主从不同步;

版本不一致,特别是高版本是主,低版本为从的情况下,主上面支持的功能,从上面不支持该功能。

## 在PMA中执行操作后无法显示？

用户在PMA执行操作后，PMA需要打开库表获取必要信息才能在PMA中显示，但如果用户的视图不合理，会导致读取超时。

如用户自定义视图需要查询多张表或者多张表进行join，此时SQL语句可能需要执行30秒左右，查询超时，导致PMA无法显示，此时建议用户使用MySQL客户端。

## 用户如何通过API下载binlog？

如果用户需要通过API下载binlog,步骤如下：

1.通过DescribeUDBInstanceBinlog 列出指定DB的binlog包列表

2.通过BackupUDBInstanceBinlog 选定需要备份的binlog文件后开启备份

3.通过DescribeUDBLogPackage 列出指定DB的备份成功的binlog文件包列表

4.通过DescribeUDBBinlogBackupURL 获取指定binlog备份文件包的下载URL

## 内存溢出

内存溢出，Out Of Memory，简称OOM，是指程序在申请内存时，没有足够的内存空间供其使用，出现OOM。

OOM原因：当内存使用超过UDB申请内存时，会按OS的一个规则评分，分数高的进行会Kill掉。

现象：通过控制台监控视图可以看到内存使用陡然下降，异常重启的监控项会出现数据。

可采取措施：确认内存使用参数配置合理，确认是否慢查询过多，确认这些内存是否能满足当前业务。

## 默认的最大连接数是多少？

综合考虑udb服务的性能和稳定性,默认的最大连接数为2000,如果用户有特殊需求,可以上调至5000（5000为mysql云数据库服务提供最大上限）。

## 普通实例如何升级到高可用实例？

控制台上对普通版实例自主操作升级到高可用。

目前支持升级的类型：mysql-5.5、mysql-5.6、mysql-5.7、percona-5.5、percona-5.6、percona-5.7

目前支持升级的状态：运行中，且该库为单实例且没有从库；

用户的账户需求：用户的账户需具有一定余额或者信用额度，用于创建高可用订单，原普通db的订单会在升级过程完成时删除

升级时间选择：最好是选择用户业务小甚至没有业务的时间点做升级

升级对业务的影响：如果用户普通版数据库没有myisam存储引擎，可以在控制台上自主完成升级到高可用，且影响时间只有一分钟之内（ip漂移）；
如果用户的库使用了myisam存储引擎，建议更改存储引擎后再控制台升级为高可用，控制台暂不支持。

也可以直接使用API升级到高可用：

升级前检测（具体参照官方udb的api文档）：
调用CheckUDBInstanceToHAAllowance接口，确认db是否可以升级，如果不可以，对照上述关于普通升级高可用的限制，如果都满足的情况下，接口仍然返回失败，则联系技术支持。

升级流程（API）：

步骤一、调用PromoteUDBInstanceToHA,该api给原db建双主，如果接口返回失败，则联系技术支持

步骤二、调用轮询DescribeUDBInstanceState,获取该db的状态，当状态为WaitForSwitch时，则执行下面的api；如果长时间处于Upgradingha或者处于UpgradehaFail状态则联系技术支持

步骤三、待原db状态为WaitForSwitch时，调用SwitchUDBInstanceToHA,该api将原普通db的ip切换到高可用上，同时重新生成高可用订单并删除原普通db订单;如果接口返回失败，则联系技术支持；接口返回后刷新控制台，如果原db的实例类型变为高可用版，ip及实例名称都未变且状态为运行中，则升级成功，否则联系技术支持

## 高可用UDB的sync_binlog参数可否修改？

高可用UDB的sync\_binlog参数默认为1，表示每个事务完成后，调用fsync使得binlog落盘，这样可以保证数据不丢失，建议不要修改！

## DB系统总内存、内存使用率、内存大小、DB系统总缓存这几个监控项的含义？

DB系统指的是DB服务进程（mysqld、mongod等）以及对该DB实例进行日常维护的进程（比如备份）的集合

DB系统总内存：DB系统内的所有进程的内存开销以及这些进程的IO所消耗的缓存之和

DB系统总缓存：DB系统内的所有进程的IO所消耗的缓存之和

内存大小：DB服务进程（mysqld、mongod等）所占用的内存大小

内存使用率：内存大小占用户所购买内存的大小的百分比

## 高可用UDB存储引擎如何选择？

建议高可用UDB使用innodb引擎，如果使用非事务引擎myisam表，可能会造成高可用主从同步异常，从而导致高可用容灾失效。

## UDB实例删除之后备份会被删除么？

UDB实例删除之后，备份（包括自动备份和手动备份）会保留7天，7天之后自动回收。

## UDB备份机制

1、每天固定时间段自动备份（通常是0-6点，具体时间段见实例的备份页面），最多保留最近3次手动备份；

2、备份采用逻辑备份，使用mysqldump进行备份，每个备份文件会自动压缩打包；

3、备份文件至少保留2个副本；

4、备份策略可自定义，例如是否使用从从库备份、定义备份时间段、定义是否每天备份等等，具体见实例的备份页面。

##  云数据库监控项中取值为0,1的意义是？

数值0：表示对应监控项正常无需关注，数值1：表示对应监控项出现异常需要关注。

## 高可用UDB在线升降级逻辑及影响？

高可用UDB支持在线升降级，具体逻辑及影响如下：

1）只升级内存，正常在线升级内存容量，高可用业务无任何影响，但和内存大小有关的某些配置参数（如数据库内部的缓存大小）需重启生效，建议您升级完内存之后在业务低峰时重启实例。

2）高可用UDB升降级硬盘或只降级内存：先升降级高可用的备库，当备库升降级完成之后，追回主库的binlog；备库追平binlog后开始主备切换，此时有影响：服务只读不可写（20S左右的闪断）；切换完成之后，再升降级原来的主库。

##  数据库实例CPU核数？

UDB数据库提供内存、硬盘等配置供用户选择，单个数据库实例默认核数（C）与内存（G）比为1:4，核数向上取整。


##  重建MySQL从库存在哪些风险？

如需UDB团队DBA后台重建MySQL从库，请用户注意以下事项：

1）以UDB主库的数据为准，若从库产生了从库的数据，会被覆盖；

2）在重建从库期间，从库不可用；

3）在重建从库期间，主库IO会对应限速跑高，一般不会有太大影响。

##  UDB MySQL高可用备库修改日志落盘策略，需注意哪些事项？

如需UDB团队DBA后台修改UDB MySQL高可用备库的日志落盘策略，请用户注意以下事项：

1）备库如果发生宕机拉起，有一定几率造成主备数据不一致；

2）如果发生容灾后，备库提升为新的主库，再次故障时，有一定几率造成主备数据不一致。
