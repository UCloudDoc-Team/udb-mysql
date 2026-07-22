# API 列表

云数据库 MySQL 提供以下 API 。各接口的**请求/响应字段定义**以官方 API 文档为准；本文档提供索引与产品侧补充说明。

> **使用建议**：先读 [常量与约束](/udb-mysql/api/constants) 和 [常用操作流程](/udb-mysql/api/workflows)，再按下方链接查阅具体接口参数。

**快速开始**：[公共参数与调用约定](https://docs.ucloud.cn/api/summary/public)  
**API 访问地址**：[公网 / VPC 内网入口](https://docs.ucloud.cn/api/summary/gateway)（公网 `http(s)://api.ucloud.cn`）  
**签名算法**：[签名算法](https://docs.ucloud.cn/api/summary/signature)  
**完整 UDB API 目录**：[云数据库 UDB API](https://docs.ucloud.cn/api/udb-api/README)

---

## 实例生命周期

[创建 MySQL 数据库 - CreateUDBMySQLInstance](https://docs.ucloud.cn/api/udb-api/create_udb_my_sql_instance)

[启动云数据库 - StartUDBInstance](https://docs.ucloud.cn/api/udb-api/start_udb_instance)

[重启云数据库 - RestartUDBInstance](https://docs.ucloud.cn/api/udb-api/restart_udb_instance)

[关闭云数据库 - StopUDBInstance](https://docs.ucloud.cn/api/udb-api/stop_udb_instance)

[删除云数据库 - DeleteUDBInstance](https://docs.ucloud.cn/api/udb-api/delete_udb_instance)

## 查询与配置

[获取云数据库信息 - DescribeUDBInstance](https://docs.ucloud.cn/api/udb-api/describe_udb_instance)

[获取云数据库状态 - DescribeUDBInstanceState](https://docs.ucloud.cn/api/udb-api/describe_udb_instance_state)

[修改 UDB 实例的配置 - ResizeUDBInstance](https://docs.ucloud.cn/api/udb-api/resize_udb_instance)

[修改云数据库名称 - ModifyUDBInstanceName](https://docs.ucloud.cn/api/udb-api/modify_udb_instance_name)

[修改 DB 实例的管理员密码 - ModifyUDBInstancePassword](https://docs.ucloud.cn/api/udb-api/modify_udb_instance_password)

[修改云数据库备注 - ModifyUDBInstanceRemarkName](https://docs.ucloud.cn/api/udb-api/modify_udb_instance_remark_name)

## 询价

[获取云数据库价格 - DescribeUDBInstancePrice](https://docs.ucloud.cn/api/udb-api/describe_udb_instance_price)

[获取 UDB 实例升降级价格信息 - DescribeUDBInstanceUpgradePrice](https://docs.ucloud.cn/api/udb-api/describe_udb_instance_upgrade_price)

## 备份

[备份云数据库 - BackupUDBInstance](https://docs.ucloud.cn/api/udb-api/backup_udb_instance)

[获取实例备份状态 - DescribeUDBInstanceBackupState](https://docs.ucloud.cn/api/udb-api/describe_udb_instance_backup_state)

[获取备份列表 - DescribeUDBBackup](https://docs.ucloud.cn/api/udb-api/describe_udb_backup)

[删除备份 - DeleteUDBBackup](https://docs.ucloud.cn/api/udb-api/delete_udb_backup)

[获取 UDB 备份下载地址 - DescribeUDBInstanceBackupURL](https://docs.ucloud.cn/api/udb-api/describe_udb_instance_backup_url)

[修改 UDB 自动备份策略 - UpdateUDBInstanceBackupStrategy](https://docs.ucloud.cn/api/udb-api/update_udb_instance_backup_strategy)

[获取实例备份策略 - DescribeUDBBackupStrategy](https://docs.ucloud.cn/api/udb-api/describe_udb_backup_strategy)

## 规格与参数查询

创建实例前通常按顺序调用以下接口，获取可用的数据库类型、机型规格与参数组：

[获取云数据库支持类型 - DescribeUDBType](https://docs.ucloud.cn/api/udb-api/describe_udb_type)

[获取 UDB 云数据库计算规格列表 - ListUDBMachineType](https://docs.ucloud.cn/api/udb-api/list_udb_machine_type)

[获取参数信息 - DescribeUDBParamGroup](https://docs.ucloud.cn/api/udb-api/describe_udb_param_group)

## 常用配套接口

调用 UDB API 时，常需配合以下接口获取项目、地域与网络等前置参数：

| 用途 | 接口 | 说明 |
|------|------|------|
| 项目 ID | [GetProjectList](https://docs.ucloud.cn/api/summary/get_project_list) | 子账号**必须**填写 `ProjectId`；主账号不填则使用默认项目 |
| 地域 / 可用区 | [地域和可用区列表](https://docs.ucloud.cn/api/summary/regionlist) | `Region`、`Zone`、`BackupZone` 取值来源 |
| VPC | [DescribeVPC](https://docs.ucloud.cn/api/vpc-api/describe_vpc) | 创建时可选；与 `SubnetId` 成对传入 |
| 子网 | [DescribeSubnet](https://docs.ucloud.cn/api/vpc-api/describe_subnet) | 须与 `VPCId` 同属一个 VPC，且子网可用区与 `Zone` 对应 |

## 产品侧补充文档

- [常量与约束](/udb-mysql/api/constants)：`ChargeType`、`SpecificationClass`、`BackupMethod`、密码规则、状态机等
- [常用操作流程](/udb-mysql/api/workflows)：创建、删除、备份、扩容等端到端步骤
- [产品选型](/udb-mysql/product/specification_class)：机型与存储类型说明
- [机型版本](/udb-mysql/product/version)：容量规格（CPU/内存配比）及各机型支持的数据库版本
- [UDB 错误码](https://docs.ucloud.cn/api/udb-api/error_code)：常见 `RetCode` 含义
