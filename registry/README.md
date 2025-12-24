# Registry 模块

`registry` 模块提供容器镜像仓库功能，用于存储和分发 Docker 镜像和 OCI 制品。

## 概述

该模块实现了一个完整的容器镜像仓库，兼容 Docker Registry HTTP API V2 和 OCI Distribution Specification。它允许用户推送、拉取和管理容器镜像和制品。

## 功能特性

- Docker Registry V2 API 支持
- OCI Distribution Specification 合规性
- 镜像层存储和管理
- 清单管理（Docker 和 OCI 格式）
- 标签管理
- 垃圾回收
- 上游代理支持，用于缓存远程镜像仓库
- Webhook 通知
- 访问控制和身份验证
- 镜像扫描集成
- 镜像仓库 API 文档

## 结构

该模块组织为多个子包：

- **app**：主应用程序逻辑和 API 处理器
- **config**：镜像仓库配置
- **docs**：Swagger 文档
- **gc**：未使用制品的垃圾回收
- **request**：请求处理实用工具
- **services**：核心镜像仓库服务
- **tests**：合规性和集成测试
- **types**：镜像仓库特定的类型
- **utils**：辅助实用工具
- **validation**：输入验证

## 核心组件

### 镜像仓库服务
- 镜像存储和检索
- 清单验证和存储
- Blob 存储和去重
- 标签管理
- 代码仓库管理

### 上游代理
- 缓存远程镜像仓库镜像
- 减少带宽和延迟
- 支持 Docker Hub、GCR 等

### 垃圾回收
- 删除未引用的 blob
- 回收存储空间
- 可配置的保留策略

## API 端点

镜像仓库实现标准端点：
- `/v2/`：API 版本检查
- `/v2/<name>/manifests/<reference>`：清单操作
- `/v2/<name>/blobs/<digest>`：Blob 操作
- `/v2/<name>/tags/list`：列出标签
- 等等...

## 使用示例

### 推送镜像
```bash
docker tag myimage:latest registry.example.com/myrepo/myimage:latest
docker push registry.example.com/myrepo/myimage:latest
```

### 拉取镜像
```bash
docker pull registry.example.com/myrepo/myimage:latest
```

### 镜像仓库配置
```go
config := registry.Config{
    Storage: storageConfig,
    Auth: authConfig,
    Proxy: proxyConfig,
}
```

## 存储

通过 blob 存储抽象支持多个存储后端：
- 本地文件系统
- 云存储（GCS、S3、Azure Blob）

## 合规性

该模块包括 OCI Distribution Spec 合规性测试：
```bash
make conformance-test
```

## 许可证

Apache License 2.0