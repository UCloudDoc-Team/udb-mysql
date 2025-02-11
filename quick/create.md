# 创建MySQL实例

1、在控制台选择"云数据库 MySQL UDB"\>"MySQL管理"，点击“创建数据库”；或者在空白区域根据提示点击创建MySQL实例。

![image](/images/quick/createMySQL2023.1.png)

2、创建页面中选择地域、可用区。

![image](/images/quick/createMySQLRegion2023.png)

3、选择数据库配置

3.1）按需选择实例类型（高可用版）、版本、数据库机型、内存、硬盘、高可用容灾方案。创建MySQL实例时会使用默认配置文件，若有特殊需要，用户可以上传自定义配置文件。

机型
![image](/images/quick/createMySQLConf2024.png)
机型(旧版)
![image](/images/quick/createMySQLConf2023.png)

- 用户在选择大版本后，可在小版本列表选择对应小版本。
- 对于8.0版本，用户按需要配置“表名大小写”。 此配置项在实例创建后不可更改
```
 on 表示 lower_case_table_names=0，即表名区分大小写
 off  表示 lower_case_table_names=1，即表名不区分大小写
```

3.2）按需设置网络、实例名称、端口号，管理员用户名和管理员密码。MySQL默认端口号为3306，管理员用户名默认为root。为了数据安全，管理员密码需要一定的复杂度。

![image](/images/create01.png)

3.3）数据库计费，在右侧面板选择计费方式与计费周期，并点击“立即购买”。

![image](/images/quick/createMySQLBuy2023.png)

4、最后确认各项选择以及金额，确认支付。

![image](/images/quick/createMySQLBuyConfim.png)

5、支付完成后，页面跳转回MySQL实例管理页，MySQL实例进行初始化，待初始化完成后即可使用；在MySQL实例管理页，列出了MySQL实例的全部信息，包括实例名称、属性、IP地址、实例类型、机型、版本、状态等。

![image](/images/quick/createMySQLList2023.png)

6、选择某一个MySQL实例点击“详情”会显示MySQL实例详细信息，同时可以查看监控数据和进行常用操作。

![image](/images/mysql-v4-008.png)
