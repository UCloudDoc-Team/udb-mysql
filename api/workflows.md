# 常用操作流程

云数据库 MySQL 常用操作的端到端步骤。常量与枚举见 [常量与约束](/udb-mysql/api/constants)；接口参数见 [API 列表](/udb-mysql/api/api_reference)。

**约定**：

- 所有请求须带 `PublicKey`、`Signature`（见 [签名算法](https://docs.ucloud.cn/api/summary/signature)）
- 子账号须传 `ProjectId`（[GetProjectList](https://docs.ucloud.cn/api/summary/get_project_list)）
- 异步操作轮询间隔建议 30s，创建实例超时建议 10–15 分钟
- 状态轮询优先用 **DescribeUDBInstanceState**（轻量）；需完整配置时用 DescribeUDBInstance

---

## 1. 创建 MySQL 实例

### 1.1 探测必填参数

按顺序调用（均须 `Region` + `Zone`）：

1. **DescribeUDBType** — `InstanceMode=Normal` 或 `HA` → 取 `DBTypeId`
2. **ListUDBMachineType** — 取 `MachineType`、`StorageClass`、`SpecificationClass`（同条记录）
3. **DescribeUDBParamGroup** — 取 `GroupId` 作为 `ParamGroupId`；跨可用区 HA 时 `RegionFlag=true`
4. （可选）**DescribeUDBInstancePrice** — 与创建参数对齐询价
5. （可选）**DescribeVPC** + **DescribeSubnet** — 自定义网络时取 `VPCId`、`SubnetId`

### 1.2 创建

调用 **CreateUDBMySQLInstance**，关键参数：

```
Region, Zone, Name, AdminPassword, DBTypeId, Port, DiskSpace,
ParamGroupId, MachineType, StorageClass, SpecificationClass
```

高可用跨可用区额外传：`InstanceMode=HA`、`BackupZone`。

### 1.3 轮询

1. 记录响应 `DBId`
2. **DescribeUDBInstanceState** 轮询 `State` → `Running`
3. 用 **DescribeUDBInstance** 核对 `VirtualIP`、`Port` 等连接信息

---

## 2. 删除 MySQL 实例

> **关键**：Running 状态**不能**直接删除（RetCode `7357`）。

```
StopUDBInstance(DBId)
  → DescribeUDBInstanceState 轮询至 Shutoff
  → DeleteUDBInstance(DBId)
  → DescribeUDBInstanceState 轮询至 Delete（或 Describe 查不到）
```

对已 Shutoff 的实例重复 Stop 返回成功（幂等）。

---

## 3. 启停与重启

| 目标 | 步骤 |
|------|------|
| 停止 | StopUDBInstance → 轮询 `Shutoff` |
| 启动 | StartUDBInstance → 轮询 `Running` |
| 重启 | RestartUDBInstance → 轮询 `Running`（期间可能短暂非 Running） |

---

## 4. 修改配置（扩容）

### 4.1 询价（可选）

**DescribeUDBInstanceUpgradePrice** — 传入目标 `MemoryLimit`、`DiskSpace`、`MachineType` 等。

### 4.2 执行

**ResizeUDBInstance**：

- `DiskSpace`：新磁盘容量
- `MemoryLimit`：**必传**，即使只扩磁盘也须传当前值（从 DescribeUDBInstance 读取）
- 规格变更时传 `MachineType`

HA 实例须 `Running` 且健康检查通过；变更后等待数分钟再发起下一次 resize（避免 `7226`）。

轮询 **DescribeUDBInstanceState** → `Running`。

---

## 5. 修改名称 / 密码 / 备注

实例须为 `Running`：

| 操作 | 接口 | 关键参数 |
|------|------|----------|
| 改显示名 | ModifyUDBInstanceName | `Name`（6–63 位） |
| 改密码 | ModifyUDBInstancePassword | `Password`（8–36；字母/数字/特殊字符，至少两类） |
| 改备注 | ModifyUDBInstanceRemarkName | `Name`（备注内容） |

---

## 6. 手动备份

```
BackupUDBInstance(DBId, BackupName[, BackupMethod])
  → 记录 BackupId（若响应未返回，用 DescribeUDBBackup 按 BackupName 匹配）
  → DescribeUDBInstanceBackupState(BackupId) 轮询 State → 1（成功）
  → （可选）DescribeUDBInstanceBackupURL 获取下载地址
```

跨可用区 HA 删备份时 **DeleteUDBBackup** 须传 `BackupZone`。

---

## 7. 自动备份策略

```
DescribeUDBBackupStrategy(DBId)     # 查询当前策略
UpdateUDBInstanceBackupStrategy     # 修改 BackupDate / BackupMethod / BackupTime 等
```

注意：`BackupDate` 每周至少 2 天为 `1`；改 `BackupMethod` 时通常须同时设 `BackupDate`。

---

## 8. 查询实例列表

**DescribeUDBInstance**：

- 单实例：传 `DBId`（无需 ClassType/Offset/Limit）
- 列表：传 `ClassType=mysql`、`Offset`、`Limit`

---

## 9. 从备份恢复创建

在 **CreateUDBMySQLInstance** 中额外传 `BackupId`（来自 DescribeUDBBackup）。`DBTypeId`、`DiskSpace` 等须与备份源兼容。

---

## 10. 清理检查清单

操作完成后建议确认无遗留资源：

- [ ] 实例已 Delete（Describe 列表无该 DBId）
- [ ] 手动备份已 DeleteUDBBackup（若不再需要）
- [ ] 停止/删除流程完整执行（非 Running 状态下强删）
