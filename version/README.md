# Version 模块

`version` 模块管理 Harness 应用程序的应用程序版本信息。

## 概述

该模块提供版本跟踪和报告功能。它公开当前版本、构建信息和 Git 提交详细信息，可用于调试、监控和显示目的。

## 功能特性

- 应用程序版本跟踪
- 构建信息（构建时间、提交 SHA）
- Git 版本信息
- API 和 CLI 的版本报告
- 语义版本支持

## 版本信息

该模块通常公开：
- **Version**：语义版本（例如，"1.0.0"）
- **Commit**：Git 提交 SHA
- **BuildDate**：二进制文件构建时间
- **GoVersion**：使用的 Go 编译器版本

## 使用示例

```go
import "github.com/harness/gitness/version"

// 获取版本信息
v := version.Get()
fmt.Printf("Version: %s\n", v.Version)
fmt.Printf("Commit: %s\n", v.Commit)
fmt.Printf("Built: %s\n", v.BuildDate)

// 显示版本
version.Print()
```

## 构建时注入

版本信息通常在构建时使用链接器标志注入：

```bash
go build -ldflags "\
  -X github.com/harness/gitness/version.Version=${VERSION} \
  -X github.com/harness/gitness/version.Commit=${GIT_COMMIT} \
  -X github.com/harness/gitness/version.BuildDate=${BUILD_DATE}"
```

## CLI 使用

可以通过 CLI 显示版本信息：

```bash
./gitness version
# 输出：
# Version: 1.0.0
# Commit: abc123def456
# Built: 2024-01-15T10:30:00Z
```

## API 集成

版本信息通常通过 API 公开：
```
GET /api/v1/version
{
  "version": "1.0.0",
  "commit": "abc123def456",
  "buildDate": "2024-01-15T10:30:00Z"
}
```

## 使用场景

- 在 UI/CLI 中显示版本
- 包含在错误报告中
- 版本兼容性检查
- 监控和可观测性
- 发布跟踪
- 调试和支持

## 语义版本

遵循语义版本（semver）：
- MAJOR 版本用于不兼容的 API 更改
- MINOR 版本用于向后兼容的功能
- PATCH 版本用于向后兼容的错误修复

示例：v1.2.3
- 1 = 主版本
- 2 = 次版本
- 3 = 补丁版本

## 最佳实践

- 始终在构建时注入版本
- 包含提交 SHA 以便追溯
- 在启动时在日志中显示版本
- 在错误报告中包含版本
- 使用版本进行功能标志或兼容性检查

## 许可证

Apache License 2.0