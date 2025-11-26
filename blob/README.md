# Blob 模块

`blob` 模块为二进制大对象存储操作提供抽象层，支持多种存储后端。

## 概述

该模块为跨不同存储提供商的二进制大对象（blobs）的存储和检索提供统一接口。它支持本地文件系统存储和云存储解决方案。

## 支持的存储后端

- **Filesystem**：本地文件系统存储
- **Google Cloud Storage (GCS)**：基于云的对象存储

## 核心接口

### Store（存储）
主接口提供：
- `Upload`：上传文件到 blob 存储
- `Download`：从 blob 存储下载文件
- `GetSignedURL`：生成有时限的签名 URL，用于安全访问文件

## 使用示例

```go
// 上传文件
err := store.Upload(ctx, fileReader, "path/to/file")

// 下载文件
reader, err := store.Download(ctx, "path/to/file")

// 获取签名 URL
url, err := store.GetSignedURL(ctx, "path/to/file", expireTime)
```

## 配置

该模块使用配置系统来指定：
- 存储提供商类型
- 提供商特定的设置（路径、凭证等）
- 上传/下载选项

## 许可证

Apache License 2.0
