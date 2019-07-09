{{indexmenu_n>4}}

## 业务ip访问白名单

在UDB主从节点前，增加了读写分离中间件后， UDB看到的客户端Ip是中间件的Ip， 而不是业务真实ip。 因此，
MySQL根据用户名+访问ip来做权限管理的功能， 便不再可用。 为了解决这一问题，
UDB读写分离中间件提供业务ip访问白名单机制。该机制具备两个功能：

1、业务ip访问白名单： 在该白名单中的业务ip，才能登录到中间件，否则拒绝登录。

2、业务操作白名单：业务Ip登录后，发起的操作将被中间件进行鉴别。 如果该操作在操作白名单中，则中间件予以通过；否则将被拒绝。

业务ip访问白名单， 和业务操作白名单，均可通过4条自定义SQL进行配置。同时为了减少用户学习成本，
这4条自定义SQL的语法，和MySQL用户权限管理语句：\`create
user、grant、revoke、drop user\`高度相似。

举例：

假如用户需要创建一个名为“robert”的账号， 并只允许该账号在“10.10.1.%”网段，
和“10.10.2.%”网段的UHost，访问数据库。 而且， robert
在10.10.1.%网段发起访问时， 只具备select权限，但不具备其他权限（比如create
table、insert等）；robert 在10.10.2.%网段发起访问时， 除了不具备create
库表的权限，具备所有其他权限，而且能够授权给其他用户。

为了实现这个权限配置，可以采用以下做法：

1、 登录读写分离中间件（使用高权限用户，比如root）

2、创建mysql用户：

使用标准的create user、grant命令， 到主udb节点（可直连或者通过读写分离中间件）去创建用户。其中，
用户ip必须为%create user 'robert'@'%' identified by '123qwe';grant
all privileges on test.\* to 'robert'@'%'; 注： udb等云数据库， 均不可对整个实例(.)进行授权，
而只能以库或表为单位进行授权

创建成功后， 可以使用该用户名（robert）， 在任意uhost上， 登录读写分离中间件。

1、使用ucreate user命令， 创建ip访问白名单：

ucreate user 'robert'@'10.10.1.%';

该命令执行后， 允许10.10.1.%网段的robert账号登录中间件， 其他ip禁止。 但登录后， 权限只有show databases 和
show processlist，暂无其他权限。

2、使用ugrant 、urevoke等命令，配置ip操作权限白名单。比如：ugrant select to
'robert'@'10.10.1.%'; 注： 和标准grant命令不同的是， ugrant省略了 指定授权对象(on .） 这个语法，
ugrant的授权对象，直接继承自grant 执行该命令， 为 'robert'@'10.10.1.%' 用户开通 select 权限
如果要为10.10.2.%上的roert账号， 开通除create table之外的其他权限，可以这样做：ugrant all
privileges to 'robert'@'10.10.2.%' with grant option; urevoke create
from 'robert'@'10.10.2.%'; 执行该命令， 允许 'robert'@'10.10.2.%' 执行除create
table、database 之外的所有其他操作； 同时，还支持级联授权，允许'robert'@'10.10.2.%'
将权限授予其他用户（发起ucreate user 和ugrant命令）。

3、使用udrop命令， 删除访问ip访问控制白名单。如：

udrop user 'robert'@'10.10.1.%';

特别说明： 如果robert用户下的所有ip访问白名单都被删除， 则视为系统没有配置白名单， 此时可以用robert账号从任意uhost上登录。

提供权限配置查询命令：ushow users;
