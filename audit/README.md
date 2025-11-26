# Audit 模块

`audit` 模块提供审计日志功能，用于跟踪 Harness 系统中的重要操作和资源变更。

## 概述

该模块支持跟踪和记录用户操作、资源修改和安全相关事件。它支持审计跟踪，用于合规性和安全监控目的。

## 核心组件

- **操作类型**：定义操作，如 `Created`（创建）、`Updated`（更新）、`Deleted`（删除）、`Bypassed`（绕过）和 `ForcePush`（强制推送）
- **资源类型**：支持多种资源类型，包括：
  - 代码仓库
  - 分支规则
  - 标签和标签规则
  - 拉取请求
  - Webhooks
  - 镜像仓库制品
  - 等等

## 主要类型

### Event（事件）
表示审计事件，包含：
- 执行的操作
- 用户/主体信息
- 受影响的资源
- 时间戳
- 客户端 IP 和请求方法
- 更新前后的对象状态

### Resource（资源）
表示被审计的资源，包含：
- 资源类型
- 资源标识符
- 额外的元数据

## 使用示例

```go
// 创建审计事件
event := audit.Event{
    Action: audit.ActionCreated,
    User: principal,
    SpacePath: "/root/projects",
    Resource: audit.NewResource(
        audit.ResourceTypeRepository,
        "my-repo",
        audit.RepoName, "my-repo",
    ),
}
```

## 许可证

Apache License 2.0
