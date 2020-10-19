## 限流方案


#### 1、活跃链接限流

为读写分离中间件设置活跃链接的上限，当并发超过此值是，拒绝执行sql，保护MySQL，我们将这个称之为高水位限流，这样就给数据库加上了一层限流的功能，使得数据库不被瞬间的高爆发请求打爆。

活跃session限流命令：

a、uset active_sess_hwm =XXX，设置读写分离中间件限流高水位，取值范围0~100000,其中0默认不限流，该参数对应t_rwproxy_instance表中新增字段active_sess_hwm

b、uset active_sess_ctl_mode="all[|select|insert|update|delete]", 限流的sql类型，多个取值组合：[all|select|insert|update|delete]，默认select，设置为all需谨慎。该参数对应t_rwproxy_instance表中新增字段active_sess_ctl_mode

c、ushow rw_proxy; 可获取active_sess_hwm、active_sess_ctl_mode值

d、ushow sql_limit_stat 显示每个读写分离中间件当前限流信息，包含读写分离id 限流高水位、限流类型，当前活跃sess数

e、ushow mw_nodes 查询所有读写分离中间件ip地址和端口


#### 2、sql模板限流

读写分离通过中间件自定义SQL，为中间件设置对sql模板的限流,在当前统计周期内(5s),模板sql超过设置的频率时，会拦截模板sql。

模板限流命令：

a、uinsert sql_limit(sql_stmt : freq_num) sql模板限流，sql_stmt 即sql模板，常量替换成?后的sql语句，freq_num限流频率单位(sql数目/s)，sql模板限流信息会记录到mysql.sql_limit_rules,中间件启动会尝试创建类似sql_route_rules表操作

b、ushow sql_limit_stmts查询所有限流模板

c、udelete sql_limit("rule_id" : id)/udelete sql_limit("sql_template" : sql_stmt) 删除sql限流模板

d、ushow sql_limit_stmts_stat 显示显示每个读写分离中间件当前模板限流信息，包含读写分离id 限流模板 限流频率，当前sql频率

可连续发送多条相同模板请求进行测试



#### 3、拒绝限流场景
a、super 用户权限

b、commit/rolback

c、事务中

d、ushow、uinsert、udelete、uset等中间件管理命令,但仍统计活跃链接数



#### 4、限流补充说明
a、限流高水位是针对读写分离中间件的，udb读写分离默认有两个中间件，即读写分离的限流高水位=中间件的限流高水位*中间件的数目

b、设置限流是不区分用户的，即对user1设置的限流规则 对user2同样有效

c、读写分离 read_only模式也支持限流

d、满足忽略限流条件时，模板限流也不生效

e、某个中间件异常、或中间件之间网络异常时，ushow sql_limit_stat[sql_limit_stmts_stat]查询限流信息，不报错，返回可用中间件的限流信息。
