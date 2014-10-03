# Authenticated Encryption with Additional Data

## Example with Additional Data

```csharp
const string MESSAGE = "test";
var key = SecretBox.GenerateKey();
var nonce = SecretAead.GenerateNonce();
var additionalData = "123456";

//encrypt with Additional Data
var encrypted = SecretAead.Encrypt(Encoding.UTF8.GetBytes(MESSAGE), nonce, key, additionalData);
//decrypt with Additional Data
var decrypted = SecretAead.Decrypt(encrypted, nonce, key, additionalData);
```

## Example without Additional Data

```csharp
const string MESSAGE = "test";
var key = SecretBox.GenerateKey();
var nonce = SecretAead.GenerateNonce();

//encrypt without Additional Data
var encrypted = SecretAead.Encrypt(Encoding.UTF8.GetBytes(MESSAGE), nonce, key, null);
//decrypt without Additional Data
var decrypted = SecretAead.Decrypt(encrypted, nonce, key, null);
```

## Random Helpers

```csharp
public static byte[] GenerateNonce()
```
**Namespace:** `Sodium.SecretAead`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 8 byte nonce.

## Purpose

This operation:
- Encrypts a message with a key and a nonce to keep it confidential
- Computes an authentication tag. This tag is used to make sure that the message, as well as optional, non-confidential (non-encrypted) data, haven't been tampered with.

A typical use case for additional data is to store protocol-specific metadata about the message, such as its length and encoding.

## Usage

### Encrypt

```csharp
public static byte[] Encrypt(byte[] message, byte[] nonce, byte[] key, byte[] additionalData = null)
```
*This is the .NET equivalent of `crypto_aead_chacha20poly1305_encrypt`.*

**Namespace:** `Sodium.SecretAead`

The `Encrypt()` function encrypts a `message` using a secret `key` and a public `nonce`.

The `key` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `nonce` must be `8` bytes, otherwise the function throws a `NonceOutOfRangeException`.

The public `nonce` should never ever be reused with the same key. The recommended way to generate it is to use `SecretAead.GenerateNonce()` for the first message, and increment it for each subsequent message using the same key.

The `additionalData` may be `null` (if no additional data is required), or between `0` and `16` bytes, otherwise the function throws a `AdditionalDataOutOfRangeException`.

The function returns a byte array on success, or throws an `CryptographicException()` on failure.

### Decrypt

```csharp
public static byte[] Decrypt(byte[] cipher, byte[] nonce, byte[] key, byte[] additionalData = null)
```
*This is the .NET equivalent of `crypto_aead_chacha20poly1305_decrypt`.*

**Namespace:** `Sodium.SecretAead`

The `Decrypt()` function decrypts a message `cipher` using a secret `key` and a public `nonce`.

The `key` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `nonce` must be `8` bytes, otherwise the function throws a `NonceOutOfRangeException`.

The `additionalData` may be `null` (if no additional data is required), or between `0` and `16` bytes, otherwise the function throws a `AdditionalDataOutOfRangeException`.

The function returns a byte array on success, or throws an `CryptographicException()` on failure.

## Algorithm details

- Encryption: ChaCha20 stream cipher
- Authentication: Poly1305 MAC

## Notes

The nonce is 64 **bits** long. In order to prevent nonce reuse, if a key is being reused, it is recommended to increment the previous nonce instead of generating a random nonce for each message.
