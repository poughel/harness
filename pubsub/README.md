# Pubsub 模块

`pubsub` 模块为进程间通信提供发布-订阅消息传递。

## 概述

该模块实现发布-订阅消息系统，允许应用程序的不同部分通过主题异步通信。它支持内存和基于 Redis 的消息传递以实现可扩展性。

## 功能特性

- 基于主题的消息传递
- 多订阅者支持
- 内存和 Redis 后端
- 消息传递保证
- 消费者组
- 消息持久化（Redis 模式）
- 优雅关闭处理

## 消息传递模式

- **InMemory**：用于单实例部署或测试
- **Redis**：用于多实例的分布式部署

## 核心接口

### Publisher（发布者）
- `Publish`：向主题发送消息

### PubSub（发布-订阅）
结合发布者和：
- `Subscribe`：为主题注册处理器

### Consumer（消费者）
- `Subscribe`：订阅主题
- `Unsubscribe`：取消订阅主题
- `Close`：清理消费者资源

## 使用示例

```go
// 创建发布-订阅实例
ps := pubsub.New(config)

// 发布消息
err := ps.Publish(ctx, "user.created", payload)

// 订阅主题
consumer := ps.Subscribe(ctx, "user.created", func(payload []byte) error {
    // 处理消息
    return processUserCreated(payload)
})

// 订阅多个主题
err := consumer.Subscribe(ctx, "user.updated", "user.deleted")

// 清理
consumer.Close()
```

## 配置

```go
config := pubsub.Config{
    Mode: pubsub.ModeRedis,
    Namespace: "harness",
    // Redis 特定设置
}
```

## 消息模式

- **扇出**：一个发布者，多个订阅者
- **工作队列**：多个消费者从同一主题处理
- **主题路由**：基于主题模式路由消息

## 使用场景

- 事件通知
- 异步任务分发
- 缓存失效
- 实时更新
- 微服务通信
- Webhook 传递
- 活动动态

## 可靠性

- Redis 模式下的消息持久化
- 自动重新连接
- 消息处理器中的错误处理
- 优雅降级

## 最佳实践

- 使用有意义的主题名称
- 保持消息处理器幂等
- 处理消息处理中的错误
- 为部署类型使用适当的模式
- 关闭时清理消费者

## 许可证

Apache License 2.0