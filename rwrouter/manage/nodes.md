## 查询中间件节点信息


读写分离中间件节点有2个或2个以上，可以通过下面命令查询每个节点的具体信息。
```
ushow mw_nodes;
```


其中：

MwId 是读写分离中间件的id， 该MwId也是ushow sql_limit_stat 和 ushow sql_limit_stmts_stat 显示的id；

MwIp和Port： 中间件节点的ip和端口。可使用MySQL客户端访问，用户名和密码和直接访问读写分离入口ip一致。
