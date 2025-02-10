## MySQL 从US3回复

### 操作场景

用户可以选择从自己上传到US3的备份文件创建MySQL实例。

### 支持文件类型

* 通过mysql_dump导出来的.sql结尾文件
* 通过gz压缩过的.sql文件
* 通过xtrabackup备份的.xbstream文件

### 操作步骤

**获取us3数据URL**
进入us3保存数据界面，点击生成URL。
![image](/images/get_us3_url.png)

**通过us3数据恢复**
进入创建实例界面，点击从us3获取数据，输入框中填写URL。
![image](/images/recovery_from_us3_create.png)

点击创建按钮正常创建实例即可。

