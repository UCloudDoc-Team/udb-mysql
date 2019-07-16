{{indexmenu_n>1}}

## SQL自定义路由

UDB读写分离中间件支持SQL自定义路由功能。 通过两种方式， 可以将一条SQL，指定路由到主节点或者某个从节点。

#### 方式1：SQL模板

可以通过以下中间件自定义SQL，为中间件配置SQL路由规则：

1.指定路由到某个udb：
```
uinsert sql_route ('SQL模板': 'UDB Id');
```
或者：
```
uinsert sql_route ('SQL模板' : 'master|slave|all');
```
特别说明： 'SQL模板' 和 'UDB Id' 之间的字符为 ':' 号 而不是 ',' 号。

举例：
```
uinsert sql_route ('select money from t_account where uid=? and name=?' : 'udbha-robert'); //将该SQL路由到udbha-robert节点
uinsert sql_route ('select money from t_account where uid=? and name=?' : 'master'); //将该SQL路由到主库
uinsert sql_route ('select money from t_account where uid=? and name=?' : 'slave'); //将该SQL路由到某个从库
```
其中：“select money from t_account where uid=? and name=?”为SQL模板，该模板将实际参数用“?”号代替， 用来概括结构相同，但参数不同的同一类SQL。

“udbha-robert”为要路由到的UDB节点id。 该自定义SQL下发到中间件节点后， 凡是和SQL模板结构相同的SQL， 都将被路由到“udbha-robert”这个UDB节点。

特别说明：

SQL模板的结构，和实际SQL语句的结构，必须完全一致。假如SQL模板为：select money from t_account where uid=? and name=?则业务发起SQL， 必须保证where查询条件中的uid在前， name在后。 否则中间件会认为结构和SQL模板不一样的SQL。

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

如果要删除某一条SQL路由规则，可采用以下自定义SQL：
```
udelete sql_route ('select money from t_account where uid=?');
```
查看中间件中全部路由规则，可以采用以下自定义SQL：
```
ushow all_sql_route;
```
