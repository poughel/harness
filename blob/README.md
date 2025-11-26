# Blob 模块

`blob` 模块为二进制大对象存储操作提供抽象层，支持多种存储后端。

## 概述

该模块为跨不同存储提供商的二进制大对象（blobs）的存储和检索提供统一接口。它支持本地文件系统存储和云存储解决方案（Google Cloud Storage）。该模块设计为可扩展的，可以轻松添加新的存储后端。

## 文件说明

### 核心文件

#### 1. **interface.go** - 存储接口定义
定义 blob 存储的核心接口和错误类型。

**Store 接口**：
```go
type Store interface {
    Upload(ctx context.Context, file io.Reader, filePath string) error
    GetSignedURL(ctx context.Context, filePath string, expire time.Time, opts ...SignURLOption) (string, error)
    Download(ctx context.Context, filePath string) (io.ReadCloser, error)
}
```

**主要方法**：
- `Upload` - 上传文件到 blob 存储
  - 参数：上下文、文件读取器、文件路径
  - 返回：错误（如果有）

- `GetSignedURL` - 生成签名 URL 用于安全访问
  - 参数：上下文、文件路径、过期时间、签名选项
  - 返回：签名 URL 字符串和错误
  - 用于生成时间限制的安全访问链接

- `Download` - 从 blob 存储下载文件
  - 参数：上下文、文件路径
  - 返回：可读可关闭的流和错误

**错误定义**：
- `ErrNotFound` - 资源未找到
- `ErrNotSupported` - 操作不被支持

#### 2. **config.go** - 配置定义
定义 blob 存储的配置结构。

**Provider 类型**：
```go
const (
    ProviderGCS        Provider = "gcs"
    ProviderFileSystem Provider = "filesystem"
)
```

**Config 结构**：
```go
type Config struct {
    Provider              Provider      // 存储提供商类型
    Bucket                string        // 存储桶名称或基础路径
    KeyPath               string        // 服务账户密钥文件路径（GCS）
    TargetPrincipal       string        // 目标服务账户（用于模拟）
    ImpersonationLifetime time.Duration // 模拟令牌生命周期
}
```

**配置字段说明**：
- `Provider` - 指定使用的存储后端
- `Bucket` - GCS 的存储桶名称或文件系统的基础路径
- `KeyPath` - GCS 服务账户密钥文件路径（用于非 GCP 环境）
- `TargetPrincipal` - 用于 Workload Identity 模拟的目标服务账户
- `ImpersonationLifetime` - 模拟令牌的有效期

#### 3. **filesystem.go** - 文件系统存储实现
实现基于本地文件系统的 blob 存储。

**FileSystemStore 结构**：
```go
type FileSystemStore struct {
    basePath string  // 基础存储路径
}
```

**主要功能**：

- **Upload（上传）**
  - 在基础路径下创建文件
  - 自动创建必要的父目录
  - 使用 `io.Copy` 复制文件内容
  - 失败时自动清理部分写入的文件
  - 记录警告日志以便调试

- **Download（下载）**
  - 打开指定路径的文件
  - 返回文件读取器
  - 文件不存在时返回 `ErrNotFound`
  - 调用者负责关闭返回的读取器

- **GetSignedURL（获取签名 URL）**
  - 返回 `ErrNotSupported`
  - 文件系统存储不支持签名 URL

**特点**：
- 简单直接的实现
- 适用于开发和单机部署
- 不支持签名 URL（由于安全性考虑）
- 自动处理目录创建
- 包含错误处理和清理逻辑

#### 4. **gcs.go** - Google Cloud Storage 实现
实现基于 Google Cloud Storage 的 blob 存储。

**GCSStore 结构**：
```go
type GCSStore struct {
    cachedClient        *storage.Client  // 缓存的 GCS 客户端
    config              Config           // 配置
    tokenExpirationTime time.Time        // 令牌过期时间
}
```

**三种认证模式**：

1. **服务账户密钥文件**（开发和非 GCP 环境）
   - 使用 `KeyPath` 配置
   - 适用于本地开发
   - 需要下载服务账户密钥文件

2. **直接 Workload Identity**（GKE 环境）
   - `KeyPath` 和 `TargetPrincipal` 都为空
   - 使用默认凭据
   - 无需令牌刷新，由 GCP 管理
   - 最简单和推荐的方式

3. **Workload Identity 模拟**（GKE 环境）
   - 使用 `TargetPrincipal` 配置
   - 模拟另一个服务账户
   - 需要定期刷新令牌
   - 用于跨账户访问

**主要功能**：

- **Upload（上传）**
  - 获取最新的 GCS 客户端（可能刷新令牌）
  - 创建对象写入器
  - 使用 `io.Copy` 写入数据
  - 失败时尝试删除部分上传的文件
  - 自动关闭写入器

- **Download（下载）**
  - 获取最新的 GCS 客户端
  - 创建对象读取器
  - 对象不存在时返回 `ErrNotFound`
  - 返回流供调用者使用

- **GetSignedURL（获取签名 URL）**
  - 支持多种签名选项
  - 生成时间限制的访问 URL
  - 可配置 HTTP 方法、内容类型、头部等
  - 用于安全的文件共享

**令牌管理**：

- `createNewImpersonatedClient` - 创建模拟客户端
  - 使用 Workload Identity 模拟
  - 配置目标服务账户和作用域
  - 生成新令牌

- `getClient` - 获取当前客户端
  - 检查令牌是否过期
  - 必要时刷新令牌
  - 直接 Workload Identity 无需刷新

- `checkAndRefreshToken` - 检查和刷新令牌
  - 在令牌过期前刷新
  - 更新缓存的客户端
  - 更新过期时间

**特点**：
- 支持多种 GCP 认证方式
- 自动令牌刷新
- 完整的签名 URL 支持
- 适用于生产环境
- 支持 GKE Workload Identity

#### 5. **options.go** - 签名 URL 选项
定义生成签名 URL 的配置选项。

**SignURLConfig 结构**：
```go
type SignURLConfig struct {
    Method          string      // HTTP 方法
    ContentType     string      // 内容类型
    Headers         []string    // HTTP 头部
    QueryParameters url.Values  // 查询参数
    Insecure        bool        // 是否使用 HTTP（而非 HTTPS）
}
```

**SignURLOption 接口**：
- 函数式选项模式
- 允许灵活配置签名 URL 参数

**可用选项函数**：

- `SignWithMethod(method)` - 指定 HTTP 方法
  - 默认：GET
  - 可选：POST、PUT 等

- `SignWithContentType(contentType)` - 指定内容类型
  - 例如：`application/json`、`image/png`

- `SignWithHeaders(headers)` - 指定 HTTP 头部
  - 用于签名验证的自定义头部

- `SignWithQueryParameters(params)` - 指定查询参数
  - 添加到签名 URL 的参数

- `SignWithInsecure(insecure)` - 允许 HTTP（不安全）
  - 默认：false（使用 HTTPS）
  - 仅用于开发环境

**使用示例**：
```go
url, err := store.GetSignedURL(ctx, "file.pdf", expireTime,
    SignWithMethod(http.MethodPut),
    SignWithContentType("application/pdf"),
)
```

#### 6. **wire.go** - 依赖注入
使用 Google Wire 进行依赖注入配置。

**ProvideStore 函数**：
```go
func ProvideStore(ctx, config) (Store, error)
```

**功能**：
- 根据配置创建相应的存储实例
- 支持的提供商：
  - `ProviderFileSystem` - 创建 `FileSystemStore`
  - `ProviderGCS` - 创建 `GCSStore`
- 无效提供商返回错误

**Wire 集合**：
- `WireSet` - 导出的 Wire 提供者集合
- 用于自动依赖注入

## 支持的存储后端

### 1. Filesystem（文件系统）
- **用途**：本地开发、单机部署
- **配置**：设置 `Bucket` 为基础路径
- **优点**：简单、无需外部依赖
- **缺点**：不支持签名 URL、不适合分布式部署

### 2. Google Cloud Storage（GCS）
- **用途**：生产环境、云部署
- **配置**：设置存储桶名称和认证方式
- **优点**：可扩展、支持签名 URL、高可用
- **缺点**：需要 GCP 账户和配置

## 使用示例

### 基本使用

```go
// 方式 1：使用 ProvideStore（推荐，支持 Wire 依赖注入）
store, err := blob.ProvideStore(ctx, blob.Config{
    Provider: blob.ProviderFileSystem,
    Bucket: "/var/data/blobs",
})

// 方式 2：直接创建文件系统存储
fsStore, err := blob.NewFileSystemStore(blob.Config{
    Provider: blob.ProviderFileSystem,
    Bucket: "/var/data/blobs",
})

// 方式 3：直接创建 GCS 存储（使用服务账户密钥）
gcsStore, err := blob.NewGCSStore(ctx, blob.Config{
    Provider: blob.ProviderGCS,
    Bucket: "my-bucket",
    KeyPath: "/path/to/service-account-key.json",
})

// 方式 4：直接创建 GCS 存储（使用 Workload Identity）
gcsStore, err := blob.NewGCSStore(ctx, blob.Config{
    Provider: blob.ProviderGCS,
    Bucket: "my-bucket",
    // KeyPath 和 TargetPrincipal 为空，使用默认凭据
})
```

### 上传文件

```go
file, err := os.Open("document.pdf")
if err != nil {
    return err
}
defer file.Close()

err = store.Upload(ctx, file, "documents/report.pdf")
if err != nil {
    return fmt.Errorf("failed to upload: %w", err)
}
```

### 下载文件

```go
reader, err := store.Download(ctx, "documents/report.pdf")
if err != nil {
    if errors.Is(err, blob.ErrNotFound) {
        return fmt.Errorf("file not found")
    }
    return err
}
defer reader.Close()

// 读取内容
data, err := io.ReadAll(reader)
```

### 生成签名 URL

```go
// 生成用于下载的签名 URL（默认 GET）
expireTime := time.Now().Add(1 * time.Hour)
url, err := store.GetSignedURL(ctx, "documents/report.pdf", expireTime)

// 生成用于上传的签名 URL（PUT 方法）
url, err := store.GetSignedURL(ctx, "uploads/new-file.pdf", expireTime,
    blob.SignWithMethod(http.MethodPut),
    blob.SignWithContentType("application/pdf"),
)
```

### 使用 Wire 依赖注入

```go
// 在 wire.go 中
var Set = wire.NewSet(
    blob.WireSet,
    // ... 其他依赖
)

// Wire 会根据配置自动创建正确的存储实例
```

## GCS 认证配置

### 方式 1：服务账户密钥文件（开发环境）

```go
config := blob.Config{
    Provider: blob.ProviderGCS,
    Bucket: "my-bucket",
    KeyPath: "/path/to/service-account-key.json",
}
```

**适用场景**：
- 本地开发
- 非 GCP 环境
- CI/CD 管道

### 方式 2：直接 Workload Identity（推荐）

```go
config := blob.Config{
    Provider: blob.ProviderGCS,
    Bucket: "my-bucket",
    // KeyPath 和 TargetPrincipal 都留空
}
```

**适用场景**：
- GKE 集群内运行
- Pod 绑定到服务账户
- 最安全和简单的方式

**GKE 配置**：
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-app-sa
  annotations:
    iam.gke.io/gcp-service-account: my-app@project.iam.gserviceaccount.com
---
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      serviceAccountName: my-app-sa
```

### 方式 3：Workload Identity 模拟

```go
config := blob.Config{
    Provider: blob.ProviderGCS,
    Bucket: "my-bucket",
    TargetPrincipal: "target-sa@project.iam.gserviceaccount.com",
    ImpersonationLifetime: 1 * time.Hour,
}
```

**适用场景**：
- 需要模拟不同的服务账户
- 跨项目访问
- 细粒度的权限控制

## 最佳实践

### 1. 选择合适的存储后端
- **开发**：使用文件系统存储
- **生产**：使用 GCS 或其他云存储

### 2. 错误处理
```go
reader, err := store.Download(ctx, path)
if err != nil {
    if errors.Is(err, blob.ErrNotFound) {
        // 处理文件不存在
        return nil
    }
    // 处理其他错误
    return fmt.Errorf("download failed: %w", err)
}
defer reader.Close()
```

### 3. 资源清理
- 始终关闭 `Download` 返回的读取器
- 使用 `defer` 确保资源释放

### 4. 签名 URL
- 设置合理的过期时间（不要太长）
- 使用 HTTPS（除非开发环境）
- 根据用途选择正确的 HTTP 方法

### 5. GCS 认证
- 生产环境优先使用 Workload Identity
- 避免将服务账户密钥文件提交到代码库
- 定期轮换密钥

### 6. 路径管理
- 使用一致的路径命名约定
- 考虑使用路径前缀组织文件
- 避免路径冲突

## 性能考虑

### 文件系统存储
- 快速的本地访问
- 受限于单机磁盘性能
- 不适合大规模并发

### GCS 存储
- 高并发处理
- 全球分布式访问
- 自动扩展
- 令牌缓存减少认证开销

## 安全考虑

### 1. 访问控制
- 使用 IAM 角色限制 GCS 访问
- 签名 URL 有时间限制
- 避免公开存储桶

### 2. 数据传输
- GCS 使用 HTTPS 加密传输
- 签名 URL 默认使用 HTTPS

### 3. 认证
- Workload Identity 比服务账户密钥更安全
- 定期刷新令牌
- 最小权限原则

## 测试

每个实现都有对应的测试文件：
- `interface_test.go` - 接口测试
- `filesystem_test.go` - 文件系统存储测试
- `gcs_test.go` - GCS 存储测试
- `options_test.go` - 选项配置测试
- `config_test.go` - 配置测试

## 扩展性

添加新的存储后端只需：
1. 实现 `Store` 接口
2. 在 `config.go` 中添加新的 Provider 常量
3. 在 `wire.go` 的 `ProvideStore` 中添加分支
4. 编写相应的测试

## 许可证

Apache License 2.0
