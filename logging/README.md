# Logging 模块

`logging` 模块为 Harness 应用程序提供结构化日志配置和实用工具。

## 概述

该模块设置和配置整个应用程序使用的日志基础设施。它提供具有多种输出格式和日志级别的结构化日志。

## 功能特性

- 具有 JSON 和文本格式的结构化日志
- 可配置的日志级别（调试、信息、警告、错误）
- 带字段的上下文日志
- 请求范围的日志
- 日志输出配置（标准输出、文件）
- 性能优化的日志
- 与监控系统集成

## 日志级别

- **Debug**：用于调试的详细信息
- **Info**：一般信息消息
- **Warn**：潜在有害情况的警告消息
- **Error**：错误事件的错误消息
- **Fatal**：导致应用程序终止的严重错误

## 使用示例

```go
// 设置日志
logging.Setup(config)

// 基本日志
log.Info().Msg("Application started")
log.Error().Err(err).Msg("Failed to connect to database")

// 带字段的日志
log.Info().
    Str("user", username).
    Int("count", count).
    Msg("User action completed")

// 上下文感知日志
logger := log.Ctx(ctx)
logger.Info().Msg("Request processed")

// 结构化错误日志
log.Error().
    Err(err).
    Str("operation", "create_repo").
    Str("repo", repoName).
    Msg("Repository creation failed")
```

## 配置

```go
config := logging.Config{
    Level: "info",
    Format: "json",  // 或 "text"
    Pretty: false,   // 开发时美化打印
}
```

## 最佳实践

- 使用适当的日志级别
- 在日志消息中包含上下文
- 使用结构化字段而不是字符串格式化
- 不要记录敏感信息（密码、令牌）
- 使用上下文日志进行请求跟踪
- 保持日志消息简洁且可操作

## 集成

日志模块与以下集成：
- 用于请求日志的 HTTP 中间件
- 错误跟踪系统
- 监控和告警平台
- 日志聚合服务

## 许可证

Apache License 2.0