{{indexmenu_n>1}}

## SQL自定义路由

UDB读写分离中间件支持SQL自定义路由功能。 通过两种方式， 可以将一条SQL，指定路由到主节点或者某个从节点。

#### 方式1：SQL模板

可以通过以下中间件自定义SQL，为中间件配置SQL路由规则：

1.插入路由规则：

命令格式：
```
uinsert sql_route("sql_stmt" :  "route_dest")
```
参数说明：

sql_stmt: 即sql模板，常量替换成?后的sql语句； 比如： select * from t where id=?  / call proc1等。 其中，proc1为存储过程的名字。

route\_dest：路由目的地，取值有all、master、slave以及udb\_id，其中

all：路由到全部节点，路由比例由控制台上的 读模式 来控制

master: 只路由到主节点

slave: 只路由到从节点。 路由比例，由控制台上的 读模式 来控制（去除主节点的比例

udb_id：:路由到指定udb上

特别说明： 

1) sql\_stmt 与route\_dest" 之间的字符为 ':' 号 而不是 ',' 号。

2) SQL模板的结构，和实际SQL语句的结构，必须完全一致。假如SQL模板为：select money from t_account where uid=? and name=?则业务发起SQL， 必须保证where查询条件中的uid在前， name在后。 否则中间件会认为结构和SQL模板不一样的SQL

样例：
```
uinsert sql_route("select * from t where id=?" : "master");  
```
作用：  指定sql语句路由到 master节点  
```
uinsert sql_route("call proc1" : "all"); 
```
作用：  指定将某种类型的call存储过程语句， 路由到 all/master/slave 节点  
```
uinsert sql_route("call proc1" : "udbha-123qwe"); 
```
作用： 指定路由到udb节点

2. 查询路由规则：

命令格式：
```
ushow all_sql_route
```
作用： 查看目前已配置的路由规则

参数： 无

返回：

rule_id:  路由规则id,

sql_stmt: sql模板

sql_md5: sql模板MD5加密产生的字符串

route_dest: 路由目的地

3. 删除路由规则：

命令格式：
```
udelete sql_route("rule_id": "id");
```
参数说明：

id:  路由规则id，即ushow all_sql_route命令返回的rule_id值

样例：
```
udelete sql_route("rule_id":"1");
```

#### 方式2：SQL Hints

对于Select语句， 可以在SQL前面的注释中，增加forcemater, forceslave命令， 来指定将该Select SQL路由主节点， 或者某个从节点。举例：
```
/*force_master*/ select money from t_account where uid="tony";
```
该语句将被路由到主节点
```
/*force_slave*/ select money from t_account where uid="tony";
```
该语句将被路由到某个节点

注意：注释必须为： /\* \*/， #和–类型的SQL不具备该功能。
