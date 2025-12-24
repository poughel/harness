# CLI 模块

`cli` 模块为 Harness 应用提供命令行界面功能。

## 概述

该模块包含命令行工具的实现，允许用户从终端与 Harness 系统交互。它提供用于管理代码仓库、流水线、用户和其他资源的各种操作。

## 结构

该模块组织为多个子包：

- **operations**：包含 CLI 命令和操作的实现
- **provide**：CLI 组件的依赖注入提供者
- **session**：会话管理和身份验证
- **textui**：基于文本的用户界面组件和格式化器

## 主要功能

- 用户身份验证和会话管理
- 代码仓库操作
- 流水线管理
- 用户和令牌管理
- 交互式和非交互式模式
- 格式化输出（表格、JSON 等）

## 使用示例

CLI 通常作为主 `gitness` 二进制文件的一部分构建：

```bash
# 登录到 Harness
./gitness login

# 创建个人访问令牌
./gitness user pat "my-pat-uid" 2592000

# 列出代码仓库
./gitness repo list

# 查看帮助
./gitness --help
```

## 组件

- 命令解析和路由
- 用于 API 通信的 HTTP 客户端
- 输出格式化和美化打印
- 进度指示器和状态消息
- 错误处理和用户友好的消息

## 许可证

Apache License 2.0
