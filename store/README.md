# Store 模块

`store` 模块为 Harness 应用程序提供数据持久化和数据库操作。

## 概述

该模块包含所有与数据库相关的代码，包括数据访问对象（DAO）、数据库迁移和数据模型。它提供数据库上的抽象层，用于存储和检索应用程序数据。

## 功能特性

- 数据库连接管理
- 所有实体的数据访问对象（DAO）
- 数据库迁移
- 事务管理
- 查询构建器
- 连接池
- 数据库错误处理

## 结构

该模块组织为：
- **database**：核心数据库实现和 DAO

## 支持的实体

存储管理以下持久化：
- 用户和主体
- 代码仓库
- 空间（项目）
- 拉取请求
- 流水线和执行
- Webhooks
- 密钥
- 令牌
- SSH 密钥
- 设置和配置
- 审计日志
- 镜像仓库制品
- 等等...

## 数据库支持

主数据库：PostgreSQL

## 使用示例

```go
// 获取 DAO 实例
repoStore := store.NewRepositoryStore(db)

// 创建代码仓库
repo := &types.Repository{
    Identifier: "my-repo",
    Path: "/space/my-repo",
    // ... 其他字段
}
err := repoStore.Create(ctx, repo)

// 查找代码仓库
repo, err := repoStore.Find(ctx, repoID)

// 更新代码仓库
repo.Description = "Updated description"
err = repoStore.Update(ctx, repo)

// 列出代码仓库
repos, err := repoStore.List(ctx, parentID, filter)

// 删除代码仓库
err = repoStore.Delete(ctx, repoID)
```

## 事务管理

```go
// 在事务中执行
err := db.InTransaction(ctx, func(tx *sql.Tx) error {
    // 多个数据库操作
    err := repoStore.Create(ctx, repo)
    if err != nil {
        return err // 回滚
    }
    
    err = auditStore.Log(ctx, event)
    if err != nil {
        return err // 回滚
    }
    
    return nil // 提交
})
```

## 迁移

使用迁移工具管理数据库模式迁移：
```bash
# 运行迁移
./gitness migrate up

# 回滚迁移
./gitness migrate down
```

## 错误处理

常见存储错误：
- `ErrNotFound`：未找到资源
- `ErrDuplicate`：重复键违规
- `ErrVersionConflict`：乐观锁冲突
- `ErrForeignKeyViolation`：外键约束违规

## 最佳实践

- 对相关操作使用事务
- 适当处理 `ErrNotFound`
- 对大结果集使用过滤器和分页
- 索引频繁查询的字段
- 使用预编译语句（自动处理）
- 关闭结果集和读取器

## 性能

- 连接池以提高效率
- 索引查询以实现快速查找
- 尽可能进行批量操作
- 查询优化
- 正确使用数据库约束

## 许可证

Apache License 2.0