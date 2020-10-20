## SQL限流

UDB读写分离中间件支持SQL限流功能。可以通过SQL限流来控制数据库请求访问量和SQL并发量，保障服务的可用性。

### 限流方案

读写分离的限流有两种：

#### 1、高水位限流

为读写分离中间件设置活跃链接的上限，当并发超过此值时，拒绝执行sql，保护MySQL，称之为高水位限流，这样就给数据库加上了一层限流的功能，使得数据库不被瞬间的高爆发请求打爆。

高水位限流命令：

a、uset active_sess_hwm =XXX 设置读写分离中间件限流高水位，取值范围0~100000,其中0默认不限流，该参数对应t_rwproxy_instance表中新增字段active_sess_hwm
 
![image](/images/rwnodes20102001.png) 
 
b、uset active_sess_ctl_mode="all[|select|insert|update|delete]" 限流的sql类型，多个取值组合：[all|select|insert|update|delete]，默认select，设置为all需谨慎
 
 ![image](/images/rwnodes20102002.png) 
 
c、ushow rw_proxy  可获取active_sess_hwm、active_sess_ctl_mode值

d、ushow sql_limit_stat 显示每个读写分离中间件当前限流信息
 
 ![image](/images/rwnodes20102003.png) 
 
其中：

mwId：读写分离id 

active_sess_hwm：限流高水位

active_sess_ctl_mode：限流类型

curr_active_sess：当前活跃sess数

#### 2、SQL模板限流

通过自定义SQL，为读写分离中间件设置限流SQL模板。在统计周期内(5s)，当模板SQL请求执行数超过设置的频率时，拒绝执行模板sql，保护MySQL。

SQL模板限流命令如下：

a、uinsert sql_limit(sql_stmt : freq_num) sql模板限流，sql_stmt 即sql模板，常量替换成?后的sql语句，freq_num限流频率单位(sq执行l数/s)，sql模板限流信息会记录到mysql.sql_limit_rules，中间件启动会尝试创建类似sql_route_rules表操作
 
 ![image](/images/rwnodes20102004.png) 
 
b、ushow sql_limit_stmts 查询所有限流模板 
 
 ![image](/images/rwnodes20102005.png) 
 
c、udelete sql_limit("rule_id" : id)  删除sql限流模板 ，这里的id是通过ushow sql_limit_stmts查询获取的rule_id值

![image](/images/rwnodes20102006.png) 

或通过命令udelete sql_limit("sql_template" : sql_stmt)  删除sql限流模板 
 
 ![image](/images/rwnodes20102007.png) 
 
d、ushow sql_limit_stmts_stat 显示显示每个读写分离中间件当前模板限流信息
 
 ![image](/images/rwnodes20102008.png) 
 
其中

mwId：读写分离id 

stmt：限流SQL模板 

limit_freq_num：限流频率(条/s)

curr_freq_num：当前sql频率(条/s)

e、ushow mw_nodes 查询所有读写分离中间件ip地址和端口
 
  ![image](/images/rwnodes20102009.png) 

### 拒绝限流场景: 

a、super 用户权限 

b、commit/rolback 

c、事务中 

d、ushow、uinsert、udelete、uset等中间件管理命令，但仍统计活跃链接数

### 限流补充说明

a、限流高水位是针对读写分离中间件的，udb读写分离默认有两个中间件，即读写分离的限流高水位=中间件的限流高水位*中间件的数目

b、设置限流是不区分用户的，即对user1设置的限流规则，对user2同样有效

c、读写分离 read_only模式也支持限流

d、满足忽略限流条件时，模板限流也不生效



