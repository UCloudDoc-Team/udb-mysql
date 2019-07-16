{{indexmenu_n>2}}

## 查看上一条SQL路由目的地

要查看上一条SQL被路由到了哪个节点，可以采用以下命令：
```
mysql> ushow last_route;
+-------------+------------+
| LastSqlCmd  | Route      |
+-------------+------------+
| show tables | udb-123qwe |
+-------------+------------+
1 row in set (0.00 sec)
```
其中，udb-123qwe 为 show tables 这条SQL被路由到的节点。
