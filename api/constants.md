# 常量与约束

本文档汇总云数据库 MySQL API 中**高频枚举、格式约束与字段语义**，便于在调用接口时快速查阅。各接口的完整字段表见 [API 列表](/udb-mysql/api/api_reference) 中的官方链接。

---

## 公共参数

| 参数 | 约束 |
|------|------|
| `Region` | 地域 ID，如 `cn-bj2`。UDB 并非支持所有地域，创建前建议用 DescribeUDBType 验证 |
| `Zone` | 可用区 ID，如 `cn-bj2-02`。须与目标 `Region` 匹配 |
| `ProjectId` | 子账号**必填**；主账号省略则用默认项目。通过 [GetProjectList](https://docs.ucloud.cn/api/summary/get_project_list) 获取 |
| `BackupZone` | 跨可用区 HA 的备库可用区；见 [地域和可用区列表](https://docs.ucloud.cn/api/summary/regionlist) |
| `VPCId` + `SubnetId` | 成对使用；通过 [DescribeVPC](https://docs.ucloud.cn/api/vpc-api/describe_vpc) / [DescribeSubnet](https://docs.ucloud.cn/api/vpc-api/describe_subnet) 查询；子网可用区须与 `Zone` 一致 |

---

## 密码与命名

### AdminPassword / Password（创建与改密）

| 规则 | 值 |
|------|-----|
| 长度 | 8–36 个字符 |
| 允许字符 | 字母、数字及特殊字符：`@#$%^*-+=_,?!&()~.|` |
| 复杂度 | 须同时包含以下至少两项：大写字母、小写字母、数字、特殊符号 |
| 适用接口 | CreateUDBMySQLInstance.`AdminPassword`、ModifyUDBInstancePassword.`Password` |
| 常见错误码 | `7201` 密码不合法 |

### Name（实例名称）

| 规则 | 值 |
|------|-----|
| 长度 | 6–63 位 |
| 适用接口 | CreateUDBMySQLInstance、ModifyUDBInstanceName |
| 与备注区分 | `ModifyUDBInstanceRemarkName` 修改的是备注（Remark），非显示名 |

---

## ChargeType（计费方式）

| 值 | 含义 |
|----|------|
| `Month` | 按月付费 | 
| `Year` | 按年付费 |
| `Dynamic` | 按时付费|

---

## SpecificationClass 与 StorageClass（规格与存储）

创建 MySQL 实例时两者**必须配对**传入，可从 [ListUDBMachineType](https://docs.ucloud.cn/api/udb-api/list_udb_machine_type) 同条规格记录读取。

| SpecificationClass | 机型 | 常见 StorageClass | 说明 |
|--------------------|------|-------------------|------|
| `O` | NVMe 型 | `CLOUD_RSSD` | 高性能，RSSD 云盘 |
| `O2` | O2 型 | `CLOUD_RSSD` | 新一代处理器|
| `OM` | 共享型 | `CLOUD_SSD_ESSENTIAL` | 经济型，SSD Essential 云盘 |
| `N` | 通用型（部分询价/旧接口） | `CLOUD_SSD` | 旧版通用型场景 |

> 产品侧选型说明见 [产品选型](/udb-mysql/product/specification_class)。

### MachineType 命名

规格 ID 取自 ListUDBMachineType 返回的 `ID`，常见模式：`o.mysql2m.medium`（2C4G）、`o.mysql2m.xlarge`（4C8G）等。`MemoryLimit` 档位如 2000/4000/6000/8000（单位 MB）须与所选 MachineType 一致。

### DiskSpace

- 范围：约 20 GB – 32 TB（视存储类型与地域而定）
- 步长：通常 10 GB

---

## InstanceMode（实例模式）

| 值 | 含义 | MySQL 支持 |
|----|------|------------|
| `Normal` | 普通版（单点） | 是 |
| `HA` | 高可用版 | 是；跨可用区时须填 `BackupZone` |

DescribeUDBType 调用时须**显式传** `InstanceMode=Normal` 或 `HA`（大小写敏感，勿传 `normal`/`ha`）。

---

## DBTypeId（数据库版本）

通过 [DescribeUDBType](https://docs.ucloud.cn/api/udb-api/describe_udb_type) 按地域/可用区查询，常见值：

- `mysql-8.4`、`mysql-8.0`
- `mysql-5.7`、`percona-5.7`

`DBSubVersion`（小版本）可不传，使用平台默认小版本。

### CaseSensitivityParam（表名大小写，仅 mysql-8.0 及以上等）

| 值 | 含义 | lower_case_table_names |
|----|------|------------------------|
| `0` | 区分大小写（**默认**） | 0 |
| `1` | 不区分大小写 | 1 |

**仅创建时生效，创建后不可修改。**

---

## SemisyncFlag（半同步）

| 值 | 含义 |
|----|------|
| `0` | 默认值，开启半同步 |
| `1` | 开启半同步 |
| `2` | 关闭半同步 |

---

## BackupMethod（备份方式）

### 自动备份策略（DescribeUDBBackupStrategy / UpdateUDBInstanceBackupStrategy）

| 值 | 含义 | 适用机型 |
|----|------|----------|
| `logic` | 逻辑备份（默认） | 通用 |
| `snapshot` | 快照备份（物理） | SSD 版 MySQL |
| `xtrabackup` | 物理备份 | NVMe 版 MySQL |
| `ark_snapshot` | 方舟快照备份 | 特定场景 |
| `nobackup` | 不备份 | — |

修改 `BackupMethod` 时通常须同时设置 `BackupDate`。

### 手动备份（BackupUDBInstance）

| 值 | 含义 |
|----|------|
| （默认） | 逻辑备份 |
| `snapshot` | SSD 实例可用 |
| `xtrabackup` | NVMe MySQL 可用 |

---

## BackupDate（每周备份日）

7 位字符串，**最右位为周日**，向左依次为周六…周一。`1` 表示当天备份，`0` 表示关闭。

- 示例：`1100000` = 周五 + 周六开启
- **每周至少 2 天**须为 `1`，否则备份不会执行

---

## BackupType（备份类型，列表过滤）

| 值 | 含义 |
|----|------|
| `0` | 自动备份 |
| `1` | 手动备份 |

---

## 备份状态

不同接口表示方式不同，**勿混用解析规则**：

### DescribeUDBBackup 列表（`State` 字符串）

| State | 含义 |
|-------|------|
| `Backuping` | 备份中 |
| `Success` | 成功 |
| `Failed` | 失败 |
| `Expired` | 已过期 |

### DescribeUDBInstanceBackupState（`State` 字符串）

| State | 含义 |
|-------|------|
| `Backuping` | 备份中 |
| `Success` | 成功 |
| `Failed` | 失败 |
| `Expired` | 过期 |

---

## 实例状态（State）

### 状态流转

```
Init → Starting → Running → Stopping → Shutoff → Deleting → Delete
                    ↓
              Restarting → Running
                    ↓
              Resizing → Running
                    ↓
              Backuping → Running
                    ↓
                  Fail
```

### 各操作允许的实例状态

| 操作 | 允许状态 | 备注 |
|------|----------|------|
| 修改名称 / 密码 / 备注 | `Running` | — |
| 重启 | `Running`、`Shutoff` | — |
| 扩容（Resize） | `Running` | 仅扩容磁盘时 `MemoryLimit` 也**必传**（取当前值） |
| 手动备份 | `Running` | — |
| 启动 | `Shutoff` | 已 Running 时重复调用返回 `7357` |
| 停止 | `Running` 等 | 停止后为 `Shutoff` |
| **删除** | **`Shutoff`、`Fail`** | Running 直接删返回 **`7357`**，须先 Stop |

> 状态名是 **`Shutoff`**，不是 `Stopped`。内网 IP 字段名是 **`VirtualIP`**，不是 `PrivateIP`。

---

## 常见错误码

| RetCode | 含义 | 处理建议 |
|---------|------|----------|
| `0` | 成功 | — |
| `230` | 参数校验错误 | 检查参数名与取值 |
| `292` | Project 不存在 | 检查 `ProjectId` |
| `7045` | 创建计费失败 / 配额超限 | 减小规格或申请配额 |
| `7201` | 密码不合法 | 见上文密码规则 |
| `7226` | HA 健康检查未通过 | Resize 后等待数分钟再重试 |
| `7357` | 实例状态不允许此操作 | 删除前先 Stop；启动时实例已 Running |

完整列表：[UDB 错误码](https://docs.ucloud.cn/api/udb-api/error_code)

---

## 字段别名与注意事项

| 概念 | 说明 |
|------|------|
| `DBId` | 实例唯一 ID，创建成功返回；后续接口均使用此值 |
| `BackupId` | 备份 ID；BackupUDBInstance 响应返回，也可用 DescribeUDBBackup 按 `BackupName` 匹配 |
| `ParamGroupId` | 参数组 ID；跨可用区 HA 查询 DescribeUDBParamGroup 时须 `RegionFlag=true` |
