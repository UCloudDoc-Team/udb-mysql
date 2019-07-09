{{indexmenu_n>2}}

# 登录访问

完成创建云数据库MySQL实例后，可通过phpMyAdmin登录MySQL云数据库，也可以通过IP及端口连接到云数据库。

## phpMyAdmin登录

在控制台UDB-MySQL管理页面，选择需要登录的MySQL数据库实例，点击操作项中的登录：

![image](/images/mysql-v4-009.png)

跳转至phpMyAdmin登录页面：

![image](/images/mysql-v4-010.png)

输入创建实例时设置的用户名及密码，即可连接登录。

![image](/images/mysql-v4-011.png)

## IP端口连接

登录云主机UHost，通过MySQL Client访问，在命令行中输入：

    mysql -h$IP -P$Port -u$User -p$Password

$IP指定MySQL实例的内网IP地址。

$Port指定MySQL实例的端口。

$User指定MySQL实例的管理员名称。

$Password指定MySQL实例的管理员密码。

MySQL实例仅支持通过云主机进行内网登陆。
