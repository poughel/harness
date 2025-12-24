# Client 模块

`client` 模块提供用于与 Harness API 交互的 Go 客户端库。

## 概述

该模块包含编程客户端实现，允许 Go 应用程序与 Harness 服务器通信。它提供类型安全的方法来访问所有 Harness API 端点。

## 功能特性

- 完整的 Harness 操作 API 覆盖
- 类型安全的请求和响应处理
- 身份验证支持（令牌、会话）
- 错误处理和重试
- 请求/响应拦截器
- 上下文感知操作

## 使用示例

```go
// 创建新客户端
client := client.New(
    "https://harness.example.com",
    client.WithToken("your-api-token"),
)

// 调用 API
repo, err := client.GetRepository(ctx, spaceRef, repoRef)
if err != nil {
    log.Fatal(err)
}

// 创建资源
pr, err := client.CreatePullRequest(ctx, repoRef, pullRequest)
```

## 核心组件

- 带身份验证的 HTTP 客户端
- 资源特定的客户端方法（代码仓库、流水线、用户等）
- 请求构建器和验证器
- 响应解析器和错误映射器
- 分页支持

## 配置

客户端可以配置：
- 基础 URL
- 身份验证凭据
- 超时设置
- 自定义 HTTP 客户端
- 日志和调试选项

## 许可证

Apache License 2.0
