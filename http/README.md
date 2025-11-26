# HTTP 模块

`http` 模块为 Harness Web 服务器提供 HTTP 实用工具和中间件。

## 概述

该模块包含在整个 Harness Web 应用程序中使用的通用 HTTP 实用工具、中间件组件和辅助函数。它提供标准化的请求/响应处理、错误处理和常见的 HTTP 模式。

## 功能特性

- HTTP 中间件组件
- 请求和响应实用工具
- 错误处理和格式化
- HTTP 客户端辅助函数
- 内容协商
- 请求验证

## 常见中间件

- 身份验证中间件
- 授权检查
- 请求日志记录
- 错误恢复
- CORS 处理
- 速率限制
- 请求 ID 注入

## HTTP 实用工具

- 响应写入器和格式化器
- JSON 编码/解码辅助函数
- 查询参数解析
- 头部操作
- 内容类型检测
- 状态码辅助函数

## 使用示例

```go
// 写入 JSON 响应
http.WriteJSON(w, data, http.StatusOK)

// 写入错误响应
http.WriteError(w, err, http.StatusBadRequest)

// 解析请求体
var input RequestType
err := http.DecodeJSON(r.Body, &input)

// 应用中间件
handler = http.Chain(handler, authMiddleware, loggingMiddleware)
```

## 错误处理

该模块提供标准化的错误响应，包括：
- 一致的错误格式
- HTTP 状态码映射
- 错误消息净化
- 错误日志集成

## 最佳实践

- 使用提供的实用工具以保持一致性
- 以正确的顺序应用适当的中间件
- 使用适当的状态码处理错误
- 记录请求以便调试和监控

## 许可证

Apache License 2.0
