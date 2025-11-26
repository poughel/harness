# Encrypt Module

The `encrypt` module provides field-level encryption and decryption for sensitive data.

## Overview

This module offers an abstraction for encrypting and decrypting string values, supporting multiple encryption algorithms. It's primarily used for protecting sensitive configuration values and secrets at rest.

## Encryption Algorithms

- **AESGCM**: AES encryption in Galois/Counter Mode (authenticated encryption)
- **None**: Pass-through implementation for development/testing

## Key Interface

### Encrypter
The main interface providing:
- `Encrypt(plaintext string) ([]byte, error)`: Encrypts a string value
- `Decrypt(ciphertext []byte) (string, error)`: Decrypts encrypted data

## Usage

```go
// Create an encrypter with a 32-byte key
encrypter, err := encrypt.NewAESGCM(encryptionKey)

// Encrypt sensitive data
ciphertext, err := encrypter.Encrypt("sensitive-value")

// Decrypt data
plaintext, err := encrypter.Decrypt(ciphertext)
```

## Security Notes

- Requires a 32-byte encryption key for AESGCM
- Ciphertext includes authentication tag for integrity verification
- Each encryption uses a unique nonce
- Keys should be stored securely and never committed to version control

## Use Cases

- Database field encryption
- Configuration secret protection
- Credential storage
- API token encryption

## License

Apache License 2.0
