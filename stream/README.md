# Stream 模块

`stream` 模块为分布式消息处理提供流处理功能。

## 概述

该模块实现基于 Redis Streams 或内存队列的流式消息处理系统。它提供可靠的消息传递、消费者组和容错处理。

## 功能特性

- 基于流的消息传递
- 用于并行处理的消费者组
- 消息确认
- 带退避的自动重试
- 失败消息的死信队列
- 内存和 Redis 实现
- 并发消息处理
- 流监控和统计

## 架构

### Producer（生产者）
- 向流发布消息
- 确保消息传递
- 支持批量发布

### Consumer（消费者）
- 从流处理消息
- 可配置的并发
- 失败时自动重试
- 消息确认
- 空闲消息认领

## 核心组件

### ConsumerConfig（消费者配置）
流消费者的配置：
- Concurrency：工作 goroutine 的数量
- DefaultHandlerConfig：处理器的默认设置

### HandlerConfig（处理器配置）
单个流处理器的配置：
- IdleTimeout：消息可以被重新认领之前的时间
- MaxRetries：最大重试次数
- 批处理选项

## 使用示例

```go
// 创建生产者
producer := stream.NewProducer(config)

// 发布消息
err := producer.Publish(ctx, streamName, message)

// 创建消费者
consumer := stream.NewConsumer(config)

// 注册流处理器
err := consumer.Register(streamName, func(ctx context.Context, msg *Message) error {
    // 处理消息
    return processMessage(msg)
}, handlerOptions...)

// 开始消费
err := consumer.Start(ctx)

// 优雅关闭
consumer.Stop()
```

## 消息处理

消息处理具有：
- 成功时自动确认
- 失败时带指数退避的重试
- 达到最大重试次数后进入死信队列
- 一次性处理语义（配置时）

## 消费者组

多个消费者可以从同一流处理：
- 跨消费者的负载分配
- 自动故障转移
- 从失败消费者认领消息
- 并行处理

## 配置

```go
consumerConfig := stream.ConsumerConfig{
    Concurrency: 5,
    DefaultHandlerConfig: stream.HandlerConfig{
        IdleTimeout: 1 * time.Minute,
        MaxRetries: 3,
    },
}
```

## 使用场景

- 流水线执行事件
- Webhook 传递
- 后台作业处理
- 事件处理
- 异步任务执行
- 数据同步
- 通知传递

## 错误处理

- 失败的消息自动重试
- 可配置的重试限制
- 永久失败消息的死信队列
- 错误日志和监控

## 监控

跟踪消费者性能：
- 消息处理速率
- 错误率
- 待处理消息数
- 消费者滞后
- 处理器执行时间

## 最佳实践

- 设置适当的并发级别
- 根据操作类型配置重试限制
- 实现幂等消息处理器
- 监控消费者滞后
- 使用死信队列进行调试
- 优雅地关闭消费者

## 许可证

Apache License 2.0