# Lock 模块

`lock` 模块提供分布式锁机制，用于协调跨多个实例的操作。

## 概述

该模块实现分布式锁，以防止竞争条件并确保多实例部署中的互斥。它支持内存锁（用于单实例）和基于 Redis 的分布式锁。

## 功能特性

- 使用 Redis 的分布式锁
- 用于开发的内存锁
- 互斥锁风格的锁接口
- 可配置的锁超时
- 自动锁过期
- 锁重试机制
- 死锁预防

## 锁实现

- **Memory**：用于单实例部署的内存锁
- **Redis**：使用 Redis（Redsync）的分布式锁

## 核心接口

### MutexManager（互斥锁管理器）
提供锁管理：
- `NewMutex`：为键创建新的互斥锁
- 锁获取和释放
- 可配置的重试和超时选项

## 使用示例

```go
// 创建锁管理器
manager := lock.NewRedisManager(redisClient)

// 为资源创建互斥锁
mutex := manager.NewMutex(
    "resource-key",
    lock.WithExpiry(30 * time.Second),
    lock.WithRetries(3),
)

// 获取锁
err := mutex.Lock()
if err != nil {
    // 处理锁获取失败
    return err
}
defer mutex.Unlock()

// 执行受保护的操作
performCriticalOperation()
```

## 错误类型

该模块定义特定的错误类型：
- `ErrorKindLockHeld`：锁已被另一个进程持有
- `ErrorKindLockNotHeld`：尝试解锁未持有的锁
- `ErrorKindCannotLock`：获取锁时超时
- `ErrorKindMaxRetriesExceeded`：达到最大重试次数后失败

## 配置

```go
config := lock.Config{
    Mode: lock.ModeRedis,
    Namespace: "harness",
    // Redis 特定设置
}
```

## 使用场景

- 作业调度器协调
- 代码仓库操作
- 缓存更新
- 资源配置
- 数据库迁移
- 分布式系统中的单例操作

## 最佳实践

- 始终使用 defer 确保锁被释放
- 设置适当的过期时间以防止死锁
- 为不同资源使用唯一键
- 优雅地处理锁获取失败
- 保持临界区简短

## 许可证

Apache License 2.0