# App/API 模块

`app/api` 模块是 Harness 应用程序的 HTTP API 层，提供所有 REST API 端点的实现。

## 概述

该模块包含了 Harness 系统的完整 API 实现，包括控制器、处理器、中间件、请求/响应处理和 OpenAPI 规范。它是应用程序的主要入口点，处理所有HTTP请求并将其路由到相应的业务逻辑。

## 目录结构

### 1. **controller/** - 控制器层
控制器包含业务逻辑实现，每个子目录代表一个资源域：

#### 代码管理控制器
- **repo/** - 代码仓库管理
  - `archive.go` - 代码仓库归档
  - `blame.go` - 文件追溯（显示每行代码的提交信息）
  - `commit.go` - 提交操作
  - `create.go` - 创建代码仓库
  - `create_branch.go` - 创建分支
  - `create_commit_tag.go` - 创建提交标签
  - `create_fork.go` - 创建分支仓库
  - `default_branch.go` - 默认分支管理
  - `delete_branch.go` - 删除分支
  - `delete_commit_tag.go` - 删除提交标签
  - `diff.go` - 差异比较
  - `find.go` - 查找代码仓库
  - `fork_sync.go` - 分支仓库同步
  - `get_branch.go` - 获取分支信息
  - `get_commit.go` - 获取提交信息
  - `get_commit_divergences.go` - 计算提交分歧
  - `git_info_refs.go` - Git 信息引用（Git 协议支持）
  - `git_service_pack.go` - Git 服务包（Git 协议支持）
  - `import.go` - 导入代码仓库
  - `import_progress.go` - 导入进度查询
  - `list_branches.go` - 列出分支
  - `list_commit_tags.go` - 列出提交标签
  - `list_commits.go` - 列出提交记录
  - `list_paths.go` - 列出路径
  - `list_pipelines.go` - 列出流水线
  - `merge_check.go` - 合并检查
  - `move.go` - 移动代码仓库
  - `pipeline_generate.go` - 生成流水线
  - `purge.go` - 永久删除代码仓库
  - `raw.go` - 原始文件内容
  - `rebase.go` - 变基操作
  - `restore.go` - 恢复代码仓库
  - `soft_delete.go` - 软删除代码仓库
  - `squash.go` - 压缩合并
  - `summary.go` - 代码仓库摘要
  - `update.go` - 更新代码仓库
  - `update_public_access.go` - 更新公共访问设置
  - `content_get.go` - 获取文件内容
  - `content_paths_details.go` - 获取路径详情
  - `codeowner_validate.go` - 验证代码所有者配置
  - `label_*.go` - 标签管理操作
  - `rule_*.go` - 保护规则管理

- **pullreq/** - 拉取请求管理
  - `pr_create.go` - 创建拉取请求
  - `pr_find.go` - 查找拉取请求
  - `pr_list.go` - 列出拉取请求
  - `pr_update.go` - 更新拉取请求
  - `pr_state.go` - 更新拉取请求状态
  - `pr_commits.go` - 获取拉取请求提交
  - `pr_diff.go` - 获取拉取请求差异
  - `pr_metadata.go` - 拉取请求元数据
  - `pr_branch_candidates.go` - 获取分支候选
  - `merge.go` - 合并拉取请求
  - `revert.go` - 还原拉取请求
  - `comment_create.go` - 创建评论
  - `comment_update.go` - 更新评论
  - `comment_delete.go` - 删除评论
  - `comment_status.go` - 评论状态
  - `comment_apply_suggestions.go` - 应用建议
  - `review_submit.go` - 提交审查
  - `reviewer_add.go` - 添加审查者
  - `reviewer_delete.go` - 删除审查者
  - `reviewer_list.go` - 列出审查者
  - `reviewer_combined_list.go` - 综合审查者列表
  - `usergroup_reviewer_add.go` - 添加用户组审查者
  - `usergroup_reviewer_delete.go` - 删除用户组审查者
  - `activity_list.go` - 活动列表
  - `check_list.go` - 检查列表
  - `codeowner.go` - 代码所有者
  - `branch_change_target.go` - 更改目标分支
  - `branch_delete.go` - 删除分支
  - `branch_restore.go` - 恢复分支
  - `file_view_*.go` - 文件查看管理
  - `label_*.go` - 标签管理

#### 项目与空间控制器
- **space/** - 空间（项目）管理
  - `create.go` - 创建空间
  - `find.go` - 查找空间
  - `list.go` - 列出空间
  - `update.go` - 更新空间
  - `soft_delete.go` - 软删除空间
  - `purge.go` - 永久删除空间
  - `restore.go` - 恢复空间
  - `move.go` - 移动空间
  - `update_public_access.go` - 更新公共访问
  - `export.go` - 导出空间
  - `export_progress.go` - 导出进度
  - `import.go` - 导入空间
  - `import_repositories.go` - 导入代码仓库
  - `events.go` - 空间事件
  - `usage.go` - 使用指标
  - `list_repos.go` - 列出代码仓库
  - `list_connectors.go` - 列出连接器
  - `list_executions.go` - 列出执行
  - `list_gitspaces.go` - 列出 Gitspaces
  - `list_infraproviders.go` - 列出基础设施提供商
  - `list_pipelines.go` - 列出流水线
  - `list_secrets.go` - 列出密钥
  - `list_service_accounts.go` - 列出服务帐户
  - `list_templates.go` - 列出模板
  - `pr_count.go` - 拉取请求计数
  - `pr_list.go` - 拉取请求列表
  - `membership_*.go` - 成员管理
  - `label_*.go` - 标签管理
  - `rule_*.go` - 保护规则管理

#### 流水线与执行控制器
- **pipeline/** - 流水线管理
  - `create.go` - 创建流水线
  - `find.go` - 查找流水线
  - `update.go` - 更新流水线
  - `delete.go` - 删除流水线

- **execution/** - 执行管理
  - `create.go` - 创建执行
  - `find.go` - 查找执行
  - `list.go` - 列出执行
  - `cancel.go` - 取消执行
  - `delete.go` - 删除执行

- **trigger/** - 触发器管理
  - `create.go` - 创建触发器
  - `find.go` - 查找触发器
  - `list.go` - 列出触发器
  - `update.go` - 更新触发器
  - `delete.go` - 删除触发器

#### 开发环境控制器
- **gitspace/** - Gitspace（开发环境）管理
  - `create.go` - 创建 Gitspace
  - `find.go` - 查找 Gitspace
  - `list_all_gitspaces.go` - 列出所有 Gitspaces
  - `update.go` - 更新 Gitspace
  - `delete.go` - 删除 Gitspace
  - `action.go` - Gitspace 操作（启动/停止）
  - `events.go` - Gitspace 事件
  - `logs_stream.go` - 日志流
  - `lookup_repo.go` - 查找代码仓库

- **infraprovider/** - 基础设施提供商管理
  - `create.go` - 创建提供商
  - `find.go` - 查找提供商
  - `delete.go` - 删除提供商

#### 用户与权限控制器
- **user/** - 用户管理
  - `find.go` - 查找用户
  - `update.go` - 更新用户
  - `update_admin.go` - 更新管理员状态
  - `create_access_token.go` - 创建访问令牌
  - `delete_token.go` - 删除令牌
  - `list_tokens.go` - 列出令牌
  - `membership_spaces.go` - 成员空间
  - `publickey_*.go` - SSH 公钥管理
  - `create_favorite.go` - 创建收藏
  - `delete_favorite.go` - 删除收藏

- **users/** - 用户管理（管理员）
  - `create.go` - 创建用户
  - `find.go` - 查找用户
  - `list.go` - 列出用户
  - `update.go` - 更新用户
  - `delete.go` - 删除用户

- **principal/** - 主体管理
  - `find.go` - 查找主体
  - `find_by_email.go` - 通过邮箱查找
  - `list.go` - 列出主体

- **usergroup/** - 用户组管理
  - `list.go` - 列出用户组

- **serviceaccount/** - 服务帐户管理
  - `create.go` - 创建服务帐户
  - `find.go` - 查找服务帐户
  - `delete.go` - 删除服务帐户
  - `create_token.go` - 创建令牌
  - `delete_token.go` - 删除令牌
  - `list_tokens.go` - 列出令牌

#### 集成控制器
- **connector/** - 连接器管理
  - `create.go` - 创建连接器
  - `find.go` - 查找连接器
  - `update.go` - 更新连接器
  - `delete.go` - 删除连接器
  - `test.go` - 测试连接器

- **webhook/** - Webhook 管理
  - `repo_create.go` - 创建代码仓库 Webhook
  - `repo_find.go` - 查找代码仓库 Webhook
  - `repo_list.go` - 列出代码仓库 Webhooks
  - `repo_update.go` - 更新代码仓库 Webhook
  - `repo_delete.go` - 删除代码仓库 Webhook
  - `repo_find_execution.go` - 查找执行
  - `repo_list_executions.go` - 列出执行
  - `repo_retrigger_execution.go` - 重新触发执行
  - `space_*.go` - 空间级 Webhook 操作

#### 其他控制器
- **secret/** - 密钥管理
  - `create.go` - 创建密钥
  - `find.go` - 查找密钥
  - `update.go` - 更新密钥
  - `delete.go` - 删除密钥

- **template/** - 模板管理
  - `create.go` - 创建模板
  - `find.go` - 查找模板
  - `update.go` - 更新模板
  - `delete.go` - 删除模板

- **plugin/** - 插件管理
  - `list.go` - 列出插件

- **logs/** - 日志管理
  - `find.go` - 查找日志
  - `tail.go` - 跟踪日志

- **upload/** - 上传管理
  - `upload.go` - 上传文件
  - `download.go` - 下载文件

- **system/** - 系统管理
  - `health.go` - 健康检查
  - `version.go` - 版本信息
  - `list_config.go` - 列出配置

- **reposettings/** - 代码仓库设置
  - `general_find.go` - 查找通用设置
  - `general_update.go` - 更新通用设置
  - `security_find.go` - 查找安全设置
  - `security_update.go` - 更新安全设置

- **githook/** - Git 钩子
  - `pre_receive.go` - 预接收钩子
  - `post_receive.go` - 后接收钩子
  - `update.go` - 更新钩子

- **lfs/** - Git LFS 支持
  - `upload.go` - 上传 LFS 对象
  - `download.go` - 下载 LFS 对象
  - `transfer.go` - 传输 LFS 对象

- **migrate/** - 迁移工具
  - `create_repo.go` - 创建代码仓库
  - `pullreq.go` - 迁移拉取请求
  - `label.go` - 迁移标签
  - `rules.go` - 迁移规则
  - `webhooks.go` - 迁移 Webhooks
  - `update_state.go` - 更新状态

- **keywordsearch/** - 关键字搜索
  - `search.go` - 执行搜索

- **check/** - 检查管理
  - 检查和验证相关功能

- **service/** - 服务管理
  - 服务相关操作

### 2. **handler/** - HTTP 处理器层
处理器负责 HTTP 请求的解析、验证和响应，每个处理器对应一个 API 端点。处理器调用控制器完成业务逻辑，并格式化响应。

处理器目录结构与控制器相同，每个 `handler/<resource>/<operation>.go` 文件对应一个 API 端点。

### 3. **middleware/** - 中间件层
中间件提供请求处理的横切关注点：

- **authn/** - 身份验证
  - `authn.go` - 身份验证中间件，验证用户令牌和会话

- **authz/** - 授权
  - `authz.go` - 授权中间件，检查用户权限

- **principal/** - 主体注入
  - `principal.go` - 将已验证的主体注入请求上下文

- **logging/** - 请求日志
  - `logging.go` - 记录 HTTP 请求和响应

- **address/** - 地址处理
  - `address.go` - 处理客户端地址信息

- **encode/** - 内容编码
  - `encode.go` - 处理响应编码（压缩等）

- **nocache/** - 禁用缓存
  - `nocache.go` - 设置禁用缓存的响应头

- **goget/** - Go Get 支持
  - `goget.go` - 支持 `go get` 命令获取包

- **web/** - Web 中间件
  - `public_access.go` - 公共访问控制

### 4. **request/** - 请求解析
定义请求参数结构和解析逻辑：

- `auth.go` - 身份验证请求
- `repo.go` - 代码仓库请求
- `space.go` - 空间请求
- `pullreq.go` - 拉取请求请求
- `pipeline.go` - 流水线请求
- `execution.go` - 执行请求
- `gitspace.go` - Gitspace 请求
- `connector.go` - 连接器请求
- `webhook.go` - Webhook 请求
- `secret.go` - 密钥请求
- `template.go` - 模板请求
- `user.go` - 用户请求
- `principal.go` - 主体请求
- `token.go` - 令牌请求
- `publickey.go` - 公钥请求
- `membership.go` - 成员请求
- `label.go` - 标签请求
- `rule.go` - 规则请求
- `check.go` - 检查请求
- `git.go` - Git 请求
- `diff.go` - 差异请求
- `archive.go` - 归档请求
- `lfs.go` - LFS 请求
- `infra_provider.go` - 基础设施提供商请求
- `favorite.go` - 收藏请求
- `common.go` - 通用请求参数
- `context.go` - 上下文辅助函数
- `time.go` - 时间处理
- `util.go` - 实用工具

### 5. **render/** - 响应渲染
处理 HTTP 响应的格式化和渲染：

- `render.go` - 主渲染器，处理 JSON/XML 响应
- `render_error.go` - 错误响应渲染
- `header.go` - HTTP 头部处理
- `sse.go` - 服务器发送事件（Server-Sent Events）
- `util.go` - 渲染工具函数
- **platform/** - 平台特定渲染
  - `render.go` - 平台响应格式

### 6. **openapi/** - OpenAPI 规范
定义 OpenAPI/Swagger 规范和文档：

- `openapi.go` - 主 OpenAPI 配置
- `common.go` - 通用定义
- `repo.go` - 代码仓库 API 规范
- `space.go` - 空间 API 规范
- `pullreq.go` - 拉取请求 API 规范
- `pipeline.go` - 流水线 API 规范
- `gitspace.go` - Gitspace API 规范
- `connector.go` - 连接器 API 规范
- `webhook.go` - Webhook API 规范
- `secret.go` - 密钥 API 规范
- `template.go` - 模板 API 规范
- `user.go` - 用户 API 规范
- `users.go` - 用户管理 API 规范
- `principals.go` - 主体 API 规范
- `account.go` - 帐户 API 规范
- `service.go` - 服务 API 规范
- `resource.go` - 资源 API 规范
- `rules.go` - 规则 API 规范
- `system.go` - 系统 API 规范
- `upload.go` - 上传 API 规范
- `check.go` - 检查 API 规范
- `plugin.go` - 插件 API 规范
- `infra_provider.go` - 基础设施提供商 API 规范
- `wire.go` - 依赖注入配置

### 7. **auth/** - 认证注册
认证相关功能：

- `registry.go` - 认证注册表

### 8. **usererror/** - 用户错误处理
用户友好的错误处理：

- `usererror.go` - 用户错误类型定义
- `translate.go` - 错误信息翻译

### 9. 根文件
- `api.go` - API 包入口

## 架构模式

该模块遵循分层架构：

1. **HTTP Handler 层** (`handler/`)
   - 接收 HTTP 请求
   - 解析请求参数（使用 `request/`）
   - 调用控制器
   - 渲染响应（使用 `render/`）

2. **Controller 层** (`controller/`)
   - 实现业务逻辑
   - 协调服务调用
   - 处理事务
   - 返回结果

3. **中间件层** (`middleware/`)
   - 身份验证和授权
   - 请求日志
   - 错误处理
   - 性能监控

## API 端点分类

### 代码管理 API
- 代码仓库 CRUD
- 分支管理
- 提交操作
- 标签管理
- 合并和变基
- 差异比较
- 文件浏览

### 协作 API
- 拉取请求
- 代码审查
- 评论系统
- 审查者管理
- 活动流

### CI/CD API
- 流水线定义
- 执行管理
- 触发器配置
- 日志查看

### 开发环境 API
- Gitspace 管理
- 基础设施提供商
- 环境操作

### 用户管理 API
- 用户 CRUD
- 令牌管理
- SSH 密钥
- 成员管理
- 权限控制

### 集成 API
- 连接器
- Webhooks
- 密钥管理

### 系统 API
- 健康检查
- 版本信息
- 配置管理

## 使用示例

### 创建代码仓库
```
POST /api/v1/spaces/{space}/repos
```

### 创建拉取请求
```
POST /api/v1/repos/{repo}/pullreqs
```

### 执行流水线
```
POST /api/v1/repos/{repo}/pipelines/{pipeline}/executions
```

### 创建 Gitspace
```
POST /api/v1/spaces/{space}/gitspaces
```

## OpenAPI 文档

完整的 API 文档可通过以下端点访问：
```
GET /openapi.yaml
GET /swagger
```

## 认证

API 使用以下认证方式：
1. **Bearer Token** - 个人访问令牌
2. **Session Cookie** - Web 会话
3. **Basic Auth** - Git 操作

## 错误处理

API 使用标准 HTTP 状态码：
- `200` - 成功
- `201` - 创建成功
- `400` - 请求错误
- `401` - 未认证
- `403` - 未授权
- `404` - 未找到
- `409` - 冲突
- `500` - 服务器错误

错误响应格式：
```json
{
  "error": "error_code",
  "message": "用户友好的错误消息"
}
```

## 最佳实践

1. **请求验证** - 所有输入在 `request/` 包中验证
2. **错误处理** - 使用 `usererror` 包提供用户友好的错误
3. **权限检查** - 在控制器中执行授权检查
4. **事务管理** - 使用 `tx.go` 管理数据库事务
5. **日志记录** - 使用结构化日志记录重要操作
6. **API 版本控制** - 通过 URL 路径进行版本控制

## 许可证

Apache License 2.0
