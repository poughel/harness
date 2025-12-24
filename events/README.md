# Events 模块

`events` 模块为实时系统事件提供事件流和处理框架。

## 概述

该模块实现事件驱动架构，允许系统的不同部分发布和订阅事件。它支持内存和基于 Redis 的事件流，以实现可扩展性。

## 功能特性

- 事件发布和订阅
- 多后端支持（内存、Redis）
- 事件过滤和路由
- 流管理
- 事件保留和清理
- 类型安全的事件处理

## 事件模式

- **InMemory**：用于单实例部署或开发
- **Redis**：用于多实例的分布式部署

## 核心组件

### Event[T]（事件）
通用事件包装器，包含：
- 事件 ID
- 时间戳
- 类型化的有效负载

### Reader/Reporter（读取器/报告器）
- **Reader**：订阅和处理事件
- **Reporter**：向系统发布事件

## 配置

```go
config := events.Config{
    Mode: events.ModeRedis,
    Namespace: "harness",
    MaxStreamLength: 10000,
    ApproxMaxStreamLength: true,
}
```

## 使用示例

```go
// 创建报告器（发布者）
reporter := events.NewReporter(config)

// 发布事件
err := reporter.Report(ctx, event)

// 创建读取器（订阅者）
reader := events.NewReader(config)

// 订阅事件
err := reader.Subscribe(ctx, category, eventType, handler)
```

## 使用场景

- 实时通知
- 活动动态
- 审计事件分发
- Webhook 触发器
- 系统监控
- 集成点

## 许可证

Apache License 2.0
