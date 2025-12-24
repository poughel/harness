# Git 模块

`git` 模块提供全面的 Git 操作和代码仓库管理功能。

## 概述

该模块是一个核心组件，封装了 Git 功能并提供用于处理 Git 代码仓库的高级抽象。它处理所有与 Git 相关的操作，包括提交、分支、合并、差异等。

## 主要子包

- **api**：用于 Git 操作的 HTTP API 集成
- **check**：Git 代码仓库验证和检查
- **command**：底层 Git 命令执行
- **diff**：差异解析和操作
- **enum**：Git 相关类型的枚举
- **hash**：Git 对象哈希实用工具
- **hook**：Git 钩子管理和执行
- **maintenance**：代码仓库维护操作
- **merge**：合并策略和冲突解决
- **parser**：Git 输出解析实用工具
- **sha**：SHA 处理和验证
- **sharedrepo**：共享代码仓库管理
- **storage**：代码仓库存储抽象
- **tempdir**：临时目录管理
- **types**：Git 特定的类型定义

## 主要操作

- **代码仓库管理**：克隆、初始化、删除
- **分支操作**：创建、删除、列出、保护
- **提交操作**：创建、修改、还原、挑选
- **差异操作**：生成差异、解析补丁
- **合并操作**：合并分支、解决冲突
- **标签操作**：创建、删除、列出标签
- **追溯**：使用提交信息注释文件
- **树操作**：浏览代码仓库树
- **引用操作**：管理 Git 引用

## 使用示例

```go
// 初始化 Git 代码仓库
repo, err := git.InitRepository(ctx, path)

// 创建分支
err = git.CreateBranch(ctx, repoPath, branchName, targetSHA)

// 提交更改
sha, err := git.Commit(ctx, repoPath, params)

// 获取提交信息
commit, err := git.GetCommit(ctx, repoPath, sha)

// 生成差异
diff, err := git.Diff(ctx, repoPath, baseSHA, headSHA)
```

## 功能特性

- 完整的 Git 协议支持
- 针对大型代码仓库的优化操作
- 预接收和后接收钩子支持
- 密钥扫描集成
- 代码仓库优化和维护
- GPG 签名验证
- 子模块支持

## 性能优化

- 共享代码仓库模式以提高空间效率
- 尽可能进行增量操作
- 缓存频繁访问的数据
- 批量操作支持

## 许可证

Apache License 2.0
