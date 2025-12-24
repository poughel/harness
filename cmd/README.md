# CMD 模块

`cmd` 模块包含 Harness 应用程序的主入口点和命令定义。

## 概述

该模块提供命令行应用程序结构，包括主可执行文件入口点和命令定义。它使用 `gitness` 作为主要命令行工具的二进制文件名称。

## 结构

该模块组织为：
- **gitness**：主应用程序入口点和命令定义

## 主要职责

- 应用程序引导和初始化
- 命令行参数解析
- 命令路由和执行
- 配置加载
- 日志设置
- 优雅关闭处理

## 可用命令

Harness CLI 提供多个顶级命令：
- `server`：启动 Harness 服务器
- `login`：对 Harness 实例进行身份验证
- `user`：用户和令牌管理操作
- `repo`：代码仓库管理
- `space`：空间/项目管理
- 等等...

## 使用示例

```bash
# 启动服务器
./gitness server .local.env

# 生成 Swagger 文档
./gitness swagger > swagger.yaml

# 查看所有可用命令
./gitness --help

# 查看特定命令的帮助
./gitness server --help
```

## 配置

应用程序可以通过以下方式配置：
- 环境文件（例如 `.local.env`）
- 环境变量
- 命令行标志
- 配置文件

## 许可证

Apache License 2.0
