# Types 模块

`types` 模块包含整个 Harness 应用程序中使用的通用类型定义和数据结构。

## 概述

该模块定义在不同模块之间共享的核心领域类型、枚举和数据结构。它提供类型定义的中心位置，以确保一致性并避免循环依赖。

## 主要类型类别

### 核心实体
- **Repository**：Git 代码仓库元数据
- **Space**：项目/命名空间容器
- **Principal**：用户和服务帐户表示
- **Pipeline**：CI/CD 流水线定义
- **PullRequest**：拉取请求数据结构
- **Webhook**：Webhook 配置

### 执行类型
- **Execution**：流水线执行实例
- **Stage**：流水线阶段定义
- **Step**：阶段中的单个步骤
- **Log**：构建和执行日志

### 镜像仓库类型
- **Image**：容器镜像元数据
- **Artifact**：镜像仓库制品信息
- **Tag**：镜像标签
- **Manifest**：镜像清单

### 身份验证类型
- **Token**：API 令牌和凭据
- **Session**：用户会话
- **PublicKey**：SSH 公钥

### 枚举
位于 `enum` 子包中：
- 代码仓库状态
- 拉取请求状态
- 流水线触发器
- Webhook 事件
- 用户角色和权限
- 等等...

## 结构

该模块包括：
- **check**：验证实用工具和检查器
- **enum**：枚举定义
- 根目录中的主类型定义

## 常见类型

### Repository（代码仓库）
```go
type Repository struct {
    ID          int64
    Version     int64
    Identifier  string
    Path        string
    ParentID    int64
    Description string
    IsPublic    bool
    CreatedBy   int64
    Created     int64
    Updated     int64
    // ... 其他字段
}
```

### Principal（主体）
```go
type Principal struct {
    ID          int64
    UID         string
    Email       string
    DisplayName string
    Admin       bool
    // ... 其他字段
}
```

### PullRequest（拉取请求）
```go
type PullRequest struct {
    ID              int64
    Version         int64
    Number          int64
    State           PullReqState
    Title           string
    Description     string
    SourceRepoID    int64
    SourceBranch    string
    TargetRepoID    int64
    TargetBranch    string
    Author          Principal
    // ... 其他字段
}
```

## 使用示例

```go
import "github.com/harness/gitness/types"

// 创建代码仓库
repo := &types.Repository{
    Identifier: "my-repo",
    Path: "/space/my-repo",
    IsPublic: true,
}

// 使用枚举
if pr.State == types.PullReqStateOpen {
    // 处理打开的拉取请求
}

// 使用验证
err := types.ValidateIdentifier(identifier)
```

## 验证

许多类型包括验证方法：
- 标识符格式验证
- 字段长度检查
- 必填字段验证
- 业务规则验证

## JSON 序列化

类型设计用于 JSON 序列化：
- 适当的 JSON 标签
- 可选字段的 omitempty
- 需要时的自定义编组

## 最佳实践

- 使用定义的类型而不是基元
- 使用类型方法验证输入
- 对固定值集使用枚举
- 一致地嵌入通用字段（时间戳、ID）
- 保持类型专注和内聚

## 许可证

Apache License 2.0