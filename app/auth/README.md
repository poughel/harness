# App/Auth 模块

`app/auth` 模块是 Harness 应用程序的身份验证（Authentication）和授权（Authorization）系统，负责验证用户身份和检查访问权限。

## 概述

该模块提供完整的认证和授权功能，包括 JWT 令牌验证、会话管理、基于成员的权限检查和公共访问控制。它是系统安全的核心组件，确保只有经过身份验证和授权的用户才能访问相应的资源。

## 目录结构

### 根文件

#### 1. **session.go** - 会话管理
定义认证会话的核心数据结构：

```go
type Session struct {
    Principal types.Principal  // 已认证的主体（用户/服务账户）
    Metadata  Metadata         // 认证相关的元数据
}
```

**主要功能**：
- 存储已认证主体的信息
- 包含认证相关的元数据（令牌 ID、SSH 密钥 ID、访问权限等）
- 作为请求上下文中传递的认证信息载体

#### 2. **metadata.go** - 认证元数据
定义不同类型的认证元数据：

**Metadata 接口**：
- 所有元数据类型都实现此接口
- `ImpactsAuthorization()` - 指示元数据是否影响授权决策

**元数据类型**：

- **EmptyMetadata** - 空元数据
  - 用于没有额外元数据的认证会话
  - 不影响授权决策

- **TokenMetadata** - 令牌元数据
  - 存储令牌类型（个人访问令牌、服务账户令牌等）
  - 存储令牌 ID
  - 不影响授权决策

- **MembershipMetadata** - 成员元数据
  - 存储临时成员授权信息
  - 包含空间 ID 和角色
  - **影响授权决策**

- **AccessPermissionMetadata** - 访问权限元数据
  - 存储每个空间的权限信息
  - 用于细粒度的访问控制
  - **影响授权决策**

#### 3. **anonymous.go** - 匿名用户
定义匿名用户的处理：

**主要组件**：
- `AnonymousPrincipal` - 匿名主体的内存表示
  - ID 为 -1
  - UID 为特殊的匿名用户标识
  - 类型为用户类型

- `IsAnonymousSession()` - 检查会话是否为匿名会话
  - 用于区分已认证和未认证的请求
  - 授权器负责处理匿名访问的权限

### authn/ - 身份验证子包

身份验证（Authentication）负责验证用户身份。

#### 1. **authenticator.go** - 认证器接口
定义身份验证的核心接口：

**Authenticator 接口**：
```go
type Authenticator interface {
    Authenticate(r *http.Request) (*auth.Session, error)
}
```

**返回值说明**：
- `(session, nil)` - 请求包含认证数据且主体已验证
- `(nil, ErrNoAuthData)` - 请求不包含任何认证数据
- `(nil, err)` - 请求包含认证数据但验证失败

**错误定义**：
- `ErrNoAuthData` - 请求中没有可用于认证的数据

#### 2. **jwt.go** - JWT 认证器
实现基于 JWT（JSON Web Token）的身份验证：

**JWTAuthenticator 结构**：
- `cookieName` - Cookie 名称
- `principalStore` - 主体存储（用于查找用户）
- `tokenStore` - 令牌存储（用于验证令牌）
- `anonymousUserSecret` - 匿名用户密钥

**主要功能**：
- 从 HTTP 请求中提取 JWT 令牌
  - 支持 `Authorization` 头部（Bearer 令牌）
  - 支持 Cookie
  - 支持 RemoteAuth 头部
- 解析和验证 JWT 令牌
  - 验证签名算法（HMAC）
  - 验证令牌有效性
  - 验证令牌是否已撤销
- 从数据库加载主体信息
- 构建认证会话对象
- 处理不同类型的令牌（个人访问令牌、服务账户令牌等）

**令牌提取逻辑**：
1. 检查 Authorization 头部（Bearer 或 RemoteAuth 前缀）
2. 检查 Cookie
3. 返回找到的令牌字符串

**认证流程**：
1. 提取令牌字符串
2. 解析令牌获取主体 ID（最小解析）
3. 从数据库加载完整主体信息
4. 使用主体密钥验证令牌签名
5. 检查令牌是否已撤销
6. 构建并返回认证会话

#### 3. **wire.go** - 依赖注入
使用 Google Wire 进行依赖注入配置：
- 提供认证器的实例化
- 配置依赖关系

### authz/ - 授权子包

授权（Authorization）负责检查用户是否有权限执行特定操作。

#### 1. **authz.go** - 授权器接口
定义授权检查的核心接口：

**Authorizer 接口**：
```go
type Authorizer interface {
    Check(ctx, session, scope, resource, permission) (bool, error)
    CheckAll(ctx, session, permissionChecks...) (bool, error)
}
```

**Check 方法**：
- 检查主体是否有权限在指定范围内对资源执行操作
- 返回值：
  - `(true, nil)` - 操作被允许
  - `(false, nil)` - 操作不被允许
  - `(false, err)` - 检查过程中发生错误，操作应被拒绝

**CheckAll 方法**：
- 检查主体是否有权限执行所有请求的操作
- 返回值：
  - `(true, nil)` - 所有请求的操作都被允许
  - `(false, nil)` - 至少一个操作不被允许
  - `(false, err)` - 检查过程中发生错误，所有操作应被拒绝

**错误定义**：
- `ErrNoPermissionCheckProvided` - 没有提供权限检查

#### 2. **membership.go** - 成员授权器
基于成员关系的授权实现：

**MembershipAuthorizer 结构**：
- `permissionCache` - 权限缓存（提高性能）
- `spaceFinder` - 空间查找器
- `publicAccess` - 公共访问服务

**授权检查流程**：

1. **公共访问检查**
   - 首先检查资源是否允许公共访问
   - 如果允许公共访问且权限匹配，立即返回允许

2. **管理员检查**
   - 系统管理员可以调用任何 API
   - 如果主体是管理员，立即返回允许

3. **成员权限检查**
   - 从权限缓存获取主体在空间中的权限
   - 检查主体是否有所需的权限
   - 如果有权限，返回允许

4. **元数据权限检查**
   - 检查会话元数据中的临时权限
   - 支持 MembershipMetadata（临时成员授权）
   - 支持 AccessPermissionMetadata（细粒度权限）

**权限继承**：
- 空间权限从父空间继承
- 子空间继承父空间的成员关系和权限
- 支持多级空间的权限传递

#### 3. **membership_cache.go** - 权限缓存
实现权限检查的缓存机制以提高性能：

**PermissionCacheKey** - 缓存键：
```go
type PermissionCacheKey struct {
    PrincipalID int64           // 主体 ID
    SpaceRef    string          // 空间引用
    Permission  enum.Permission // 权限
}
```

**PermissionCache** - 权限缓存接口：
- 基于 `cache.Cache` 的类型别名
- 缓存键值对：`(主体ID, 空间引用, 权限) -> 是否允许`

**permissionCacheGetter** - 缓存获取器：
- 实现缓存未命中时的权限查询逻辑
- 查找空间路径上的第一个存在的空间
- 从当前空间向上遍历查找成员关系
- 检查成员角色是否包含所需权限
- 支持多级空间的权限继承

**主要功能**：
- 减少数据库查询次数
- 提高授权检查性能
- 自动处理缓存过期
- 支持空间层级的权限查询

**权限查找逻辑**：
1. 查找空间路径上第一个存在的空间
2. 在当前空间查找成员关系
3. 检查成员角色是否包含所需权限
4. 如果没有找到，递归到父空间继续查找
5. 限制递归深度以防止无限循环

#### 4. **public_access.go** - 公共访问
检查资源的公共访问权限：

**CheckPublicAccess 函数**：
- 检查请求的权限是否对资源公开
- 支持的资源类型：
  - **Space（空间）** - 项目/命名空间
  - **Repo（代码仓库）** - Git 仓库
  - **Pipeline（流水线）** - CI/CD 流水线
  - **Registry（镜像仓库）** - 容器镜像仓库

**公共访问规则**：

1. **代码仓库和流水线**
   - 仅允许查看权限（`PermissionRepoView`、`PermissionPipelineView`）
   - 不允许修改、删除等操作

2. **空间**
   - 仅允许查看权限（`PermissionSpaceView`）
   - 不允许修改、删除等操作

3. **镜像仓库**
   - 仅允许查看和拉取权限
   - 不允许推送、删除等操作

**路径解析**：
- 根据资源类型和范围构建资源路径
- 使用 `paths.Concatenate` 组合路径
- 处理空间和资源的层级关系

**集成**：
- 与 `publicaccess.Service` 集成
- 检查资源是否配置为公共可访问
- 验证请求的权限是否在公共权限范围内

#### 5. **wire.go** - 依赖注入
使用 Google Wire 进行依赖注入配置：
- 提供授权器的实例化
- 配置权限缓存
- 设置依赖关系

## 架构模式

### 认证流程（Authentication Flow）

```
HTTP 请求
  ↓
1. 提取令牌（JWT/Cookie）
  ↓
2. 解析令牌获取主体 ID
  ↓
3. 从数据库加载主体信息
  ↓
4. 验证令牌签名和有效性
  ↓
5. 构建认证会话（Session）
  ↓
请求继续处理（带会话上下文）
```

### 授权流程（Authorization Flow）

```
授权请求（Session, Scope, Resource, Permission）
  ↓
1. 检查公共访问权限
  ├─ 允许 → 返回 true
  └─ 不允许 → 继续
  ↓
2. 检查是否为系统管理员
  ├─ 是 → 返回 true
  └─ 否 → 继续
  ↓
3. 检查权限缓存
  ├─ 命中 → 返回缓存结果
  └─ 未命中 → 继续
  ↓
4. 查询成员关系和权限
  ├─ 在当前空间查找
  ├─ 检查角色权限
  └─ 递归到父空间
  ↓
5. 检查会话元数据中的临时权限
  ├─ MembershipMetadata
  └─ AccessPermissionMetadata
  ↓
返回授权结果（允许/拒绝）
```

## 主要概念

### 1. 主体（Principal）
- 可以是用户（User）或服务账户（ServiceAccount）
- 包含 ID、UID、类型、邮箱、显示名称等信息
- 有管理员标志（Admin flag）

### 2. 会话（Session）
- 存储已认证主体的信息
- 包含认证元数据（令牌、SSH 密钥、临时权限等）
- 在请求处理过程中传递

### 3. 范围（Scope）
- 定义操作的上下文
- 包含空间路径、代码仓库等信息
- 用于确定权限检查的边界

### 4. 资源（Resource）
- 被访问的实体（空间、代码仓库、流水线等）
- 包含类型和标识符
- 权限检查的目标

### 5. 权限（Permission）
- 定义可以执行的操作
- 例如：查看、编辑、删除、推送等
- 基于枚举类型定义

### 6. 成员关系（Membership）
- 主体在空间中的成员身份
- 包含角色（Owner、Admin、Contributor、Reader 等）
- 角色定义了一组权限

### 7. 公共访问（Public Access）
- 允许未认证用户访问某些资源
- 仅限查看操作
- 可以为空间、代码仓库、镜像仓库配置

## 使用示例

### 身份验证示例

```go
// 创建 JWT 认证器
authenticator := authn.NewTokenAuthenticator(
    principalStore,
    tokenStore,
    "harness_session",
    anonymousUserSecret,
)

// 在 HTTP 处理器中使用
func handler(w http.ResponseWriter, r *http.Request) {
    session, err := authenticator.Authenticate(r)
    if err == authn.ErrNoAuthData {
        // 未认证请求，可能需要使用匿名会话
        session = &auth.Session{
            Principal: auth.AnonymousPrincipal,
            Metadata: &auth.EmptyMetadata{},
        }
    } else if err != nil {
        // 认证失败
        http.Error(w, "Unauthorized", http.StatusUnauthorized)
        return
    }
    
    // 继续处理请求，使用 session
}
```

### 授权检查示例

```go
// 创建成员授权器
authorizer := authz.NewMembershipAuthorizer(
    permissionCache,
    spaceFinder,
    publicAccessService,
)

// 检查单个权限
allowed, err := authorizer.Check(
    ctx,
    session,
    &types.Scope{SpacePath: "/root/myproject"},
    &types.Resource{
        Type: enum.ResourceTypeRepo,
        Identifier: "myrepo",
    },
    enum.PermissionRepoEdit,
)

if err != nil {
    // 处理错误
    return err
}

if !allowed {
    // 权限不足
    return errors.New("permission denied")
}

// 检查多个权限
scope := &types.Scope{SpacePath: "/root/myproject"}
allowed, err := authorizer.CheckAll(ctx, session,
    types.PermissionCheck{
        Scope: scope,
        Resource: &types.Resource{Type: enum.ResourceTypeRepo, Identifier: "repo1"},
        Permission: enum.PermissionRepoView,
    },
    types.PermissionCheck{
        Scope: scope,
        Resource: &types.Resource{Type: enum.ResourceTypeRepo, Identifier: "repo2"},
        Permission: enum.PermissionRepoView,
    },
)
```

## 安全考虑

### 认证安全
1. **令牌验证**
   - 验证 JWT 签名算法
   - 检查令牌是否已撤销
   - 使用主体特定的密钥

2. **令牌存储**
   - 使用 HTTPS 传输
   - 设置合理的过期时间
   - 支持令牌撤销

3. **匿名访问**
   - 明确标识匿名会话
   - 限制匿名用户的权限
   - 公共访问仅限查看操作

### 授权安全
1. **权限检查**
   - 总是在业务逻辑前检查权限
   - 使用最小权限原则
   - 明确拒绝未授权的操作

2. **权限继承**
   - 正确处理空间层级
   - 递归限制防止性能问题
   - 缓存权限结果提高性能

3. **公共访问**
   - 仅允许只读操作
   - 明确定义公共资源类型
   - 可配置的公共访问级别

## 性能优化

### 权限缓存
- 使用 TTL 缓存减少数据库查询
- 缓存键包含主体、空间和权限
- 自动处理缓存失效

### 批量检查
- `CheckAll` 支持一次检查多个权限
- 减少往返次数
- 提高批量操作性能

### 空间查找优化
- 使用 `refcache.SpaceFinder` 缓存空间信息
- 减少重复的空间查询
- 支持空间层级的快速遍历

## 集成点

### 与 API 层集成
- 在 `middleware/authn` 中使用认证器
- 在 `middleware/authz` 中使用授权器
- 将会话注入请求上下文

### 与存储层集成
- `store.PrincipalStore` - 主体存储
- `store.TokenStore` - 令牌存储
- `store.MembershipStore` - 成员关系存储

### 与服务层集成
- `publicaccess.Service` - 公共访问服务
- `refcache.SpaceFinder` - 空间查找服务

## 最佳实践

1. **认证**
   - 始终验证令牌签名
   - 检查令牌撤销状态
   - 为不同类型的令牌使用不同的密钥

2. **授权**
   - 在控制器层执行授权检查
   - 使用细粒度的权限
   - 记录授权失败以便审计

3. **会话管理**
   - 将会话存储在请求上下文中
   - 不要在会话中存储敏感数据
   - 使用元数据存储额外的认证信息

4. **错误处理**
   - 区分认证失败和授权失败
   - 提供清晰的错误消息
   - 记录安全相关的错误

5. **性能**
   - 使用权限缓存
   - 批量检查权限
   - 避免不必要的数据库查询

## 许可证

Apache License 2.0
