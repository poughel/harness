# Encrypt 模块

`encrypt` 模块为敏感数据提供字段级加密和解密。

## 概述

该模块提供加密和解密字符串值的抽象，支持多种加密算法。它主要用于保护静态的敏感配置值和密钥。

## 加密算法

- **AESGCM**：Galois/Counter 模式的 AES 加密（认证加密）
- **None**：用于开发/测试的直通实现

## 核心接口

### Encrypter（加密器）
主接口提供：
- `Encrypt(plaintext string) ([]byte, error)`：加密字符串值
- `Decrypt(ciphertext []byte) (string, error)`：解密加密数据

## 使用示例

```go
// 使用 32 字节密钥创建加密器
encrypter, err := encrypt.NewAESGCM(encryptionKey)

// 加密敏感数据
ciphertext, err := encrypter.Encrypt("sensitive-value")

// 解密数据
plaintext, err := encrypter.Decrypt(ciphertext)
```

## 安全注意事项

- AESGCM 需要 32 字节的加密密钥
- 密文包含用于完整性验证的认证标签
- 每次加密使用唯一的随机数
- 密钥应安全存储，永远不要提交到版本控制

## 使用场景

- 数据库字段加密
- 配置密钥保护
- 凭据存储
- API 令牌加密

## 许可证

Apache License 2.0
