# Errors 模块

`errors` 模块为 Harness 应用程序提供标准化的错误处理和错误类型。

## 概述

该模块定义应用程序中使用的通用错误类型、错误创建实用工具和错误处理模式。它提供带有额外上下文的结构化错误，以便更好地调试和改善用户体验。

## 功能特性

- 标准化的错误类型和代码
- 错误包装和上下文
- HTTP 状态码映射
- 用户友好的错误消息
- 错误分类（未找到、验证、授权等）

## 常见错误类型

- `NotFoundError`：资源未找到错误
- `ValidationError`：输入验证失败
- `UnauthorizedError`：身份验证失败
- `ForbiddenError`：授权失败
- `ConflictError`：资源冲突
- `InternalError`：内部服务器错误

## 使用示例

```go
// 创建未找到错误
err := errors.NotFound("repository not found")

// 创建验证错误
err := errors.InvalidArgument("invalid repository name")

// 使用上下文包装错误
err := errors.Wrap(originalErr, "failed to create repository")

// 检查错误类型
if errors.IsNotFound(err) {
    // 处理未找到的情况
}
```

## 错误信息

错误可以包括：
- 错误代码/类型
- 面向用户的消息
- 内部错误详情
- 堆栈跟踪（在开发中）
- HTTP 状态码映射

## 最佳实践

- 对不同的失败场景使用特定的错误类型
- 在包装错误时包含上下文
- 向用户提供可操作的错误消息
- 记录详细的错误信息以便调试

## 许可证

Apache License 2.0
