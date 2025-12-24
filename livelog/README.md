# Livelog 模块

`livelog` 模块为流水线执行提供实时日志流功能。

## 概述

该模块实现了实时日志流系统，允许实时查看构建和流水线执行日志。它支持多个并发订阅者，并提供高效的日志分发。

## 功能特性

- 实时日志流
- 每个日志流支持多个并发订阅者
- 逐行日志传递
- 时间戳跟踪
- 内存日志缓冲
- 流生命周期管理
- 订阅者管理

## 核心组件

### LogStream 接口
主接口提供：
- `Create`：为步骤初始化日志流
- `Delete`：清理日志流
- `Write`：向流追加日志行
- `Tail`：订阅日志更新
- `Info`：获取流统计信息

### Line（行）
表示单行日志，包含：
- 行号
- 消息内容
- 时间戳

## 使用示例

```go
// 创建日志流
err := logStream.Create(ctx, stepID)

// 写入日志行
line := &livelog.Line{
    Number: 1,
    Message: "Starting build...",
    Timestamp: time.Now().Unix(),
}
err = logStream.Write(ctx, stepID, line)

// 订阅日志
logChan, errChan := logStream.Tail(ctx, stepID)
for {
    select {
    case line := <-logChan:
        fmt.Println(line.Message)
    case err := <-errChan:
        if err != nil {
            log.Fatal(err)
        }
        return
    }
}

// 清理
err = logStream.Delete(ctx, stepID)
```

## 实现

该模块使用内存发布-订阅系统实现高效的日志分发：
- 发布者将日志行写入中央流
- 订阅者实时接收日志行
- 每个流维护自己的订阅者列表
- 删除流时自动清理

## 使用场景

- 流水线执行日志
- 构建输出流
- 测试执行日志
- 实时调试
- Web UI 中的日志查看
- CLI 日志跟踪

## 性能

- 通过行缓冲实现高效的内存使用
- 低延迟日志传递
- 处理高吞吐量日志流
- 自动订阅者清理

## 许可证

Apache License 2.0
