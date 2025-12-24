# Job 模块

`job` 模块提供后台作业调度和执行框架。

## 概述

该模块实现了一个健壮的作业调度器，处理定期和一次性的后台任务。它支持分布式执行、作业持久化和自动重试机制。

## 功能特性

- 基于 Cron 的作业调度
- 一次性和定期作业
- 分布式作业执行
- 作业持久化和恢复
- 带退避的自动重试
- 作业锁定以防止重复
- 作业生命周期管理
- 作业历史和保留

## 核心组件

### Scheduler（调度器）
- 管理作业注册和执行
- 处理作业触发器和定时
- 协调跨实例的作业分发
- 实现作业锁定机制

### Executor（执行器）
- 执行作业处理器
- 管理作业并发
- 处理作业超时
- 实现重试逻辑

### Store（存储）
- 持久化作业定义和状态
- 跟踪作业执行历史
- 管理作业保留策略

## 使用示例

```go
// 定义作业
job := job.Definition{
    UID: "my-job",
    Type: "cleanup",
    Cron: "0 0 * * *", // 每天午夜
    MaxRetries: 3,
}

// 注册作业处理器
scheduler.Register(job, func(ctx context.Context) error {
    // 作业实现
    return performCleanup(ctx)
})

// 启动调度器
err := scheduler.Start(ctx)

// 手动触发作业
err := scheduler.Trigger(ctx, jobUID)
```

## 作业类型

该模块支持各种内置作业类型：
- 代码仓库清理和维护
- 日志保留和归档
- 指标聚合
- 系统健康检查
- 数据同步
- 自定义应用程序作业

## 配置

```go
config := job.Config{
    InstanceID: "instance-1",
    MaxRunning: 10,
    RetentionTime: 30 * 24 * time.Hour,
}
```

## 分布

在多实例部署中：
- 作业使用分布式锁进行分发
- 一次只有一个实例执行作业
- 失败的作业可以被其他实例接管
- 状态通过持久存储共享

## 许可证

Apache License 2.0
