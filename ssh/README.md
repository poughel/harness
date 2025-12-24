# SSH 模块

`ssh` 模块为通过 SSH 进行 Git 操作提供 SSH 服务器功能。

## 概述

该模块实现 SSH 服务器，允许用户使用 SSH 协议与 Git 代码仓库交互。它处理 SSH 身份验证、Git 命令执行和安全通信。

## 功能特性

- 用于 Git 操作的 SSH 服务器
- 公钥身份验证
- Git 命令处理（git-upload-pack、git-receive-pack）
- 代码仓库访问控制
- 用户身份验证集成
- 安全命令执行
- 会话管理

## 支持的操作

- 通过 SSH 的 `git clone`
- 通过 SSH 的 `git push`
- 通过 SSH 的 `git pull`
- 通过 SSH 的 `git fetch`

## 使用示例

### 服务器配置
```go
config := ssh.Config{
    Address: ":3022",
    HostKeys: hostKeys,
    // 身份验证和代码仓库处理器
}

server := ssh.NewServer(config)
err := server.Start()
```

### 客户端使用
```bash
# 通过 SSH 克隆代码仓库
git clone ssh://git@harness.example.com:3022/project/repo.git

# 推送更改
git push origin main

# 配置 SSH 密钥
ssh-keygen -t ed25519 -C "user@example.com"
# 将公钥添加到 Harness 用户配置文件
```

## 身份验证

支持 SSH 公钥身份验证：
- 用户将 SSH 公钥添加到其配置文件
- 服务器在连接期间验证密钥
- 授权密钥与用户帐户关联
- 无密码身份验证（更安全）

## 访问控制

- 代码仓库级访问控制
- 与 Harness 权限系统集成
- 读/写访问验证
- SSH 操作的审计日志

## 安全性

- 主机密钥验证
- 仅公钥身份验证
- 加密通信
- 命令白名单（仅 Git 命令）
- 速率限制
- 连接超时

## 配置

```go
config := ssh.Config{
    Address: ":3022",
    MaxConnections: 100,
    Timeout: 30 * time.Second,
    // 服务器身份的主机密钥
    HostKeys: []ssh.Signer{hostKey},
}
```

## 集成

适用于：
- 用于代码仓库操作的 Git 模块
- 用于用户验证的身份验证系统
- 用于日志记录的审计模块
- 用于用户 SSH 密钥管理的存储模块

## 故障排除

常见问题：
- SSH 密钥未授权：将密钥添加到用户配置文件
- 权限被拒绝：检查代码仓库访问权限
- 连接超时：验证网络和防火墙设置

## 许可证

Apache License 2.0