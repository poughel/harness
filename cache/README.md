# Cache 模块

`cache` 模块提供灵活的缓存抽象，支持多种缓存实现。

## 概述

该模块提供通用缓存接口，支持简单的键值缓存和具有批量操作的扩展缓存。它包括内存缓存、基于 TTL 的缓存和基于 Redis 的分布式缓存的实现。

## 缓存实现

- **NoCache**：绕过缓存的直通实现
- **TTLCache**：具有生存时间过期的内存缓存
- **RedisCache**：使用 Redis 的分布式缓存

## 核心接口

### Cache[K, V]（缓存）
基本缓存接口提供：
- `Get`：通过键检索值
- `Evict`：从缓存中删除值
- `Stats`：获取缓存统计信息（命中、未命中）

### ExtendedCache[K, V]（扩展缓存）
具有额外操作的扩展接口：
- `Map`：按键批量检索多个值

### Getter[K, V]（获取器）
数据源接口：
- `Find`：从底层数据源检索值

## 使用示例

```go
// 使用 getter 创建缓存
cache := cache.NewTTLCache(getter, ttl, maxSize)

// 获取值（缓存未命中会调用 getter）
value, err := cache.Get(ctx, key)

// 清除值
cache.Evict(ctx, key)

// 获取缓存统计信息
hits, misses := cache.Stats()
```

## 功能特性

- 泛型类型支持，用于类型安全的缓存
- 缓存未命中时自动填充
- 批量操作，实现高效的多键检索
- 统计跟踪
- 可配置的 TTL 和大小限制

## 许可证

Apache License 2.0
