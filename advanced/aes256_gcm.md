# Authenticated Encryption with Additional Data using AES-GCM

## Example with Additional Data

```csharp
var key = new byte[]
{
    0x42, 0x90, 0xbc, 0xb1, 0x54, 0x17, 0x35, 0x31, 0xf3, 0x14, 0xaf,
    0x57, 0xf3, 0xbe, 0x3b, 0x50, 0x06, 0xda, 0x37, 0x1e, 0xce, 0x27,
    0x2a, 0xfa, 0x1b, 0x5d, 0xbd, 0xd1, 0x10, 0x0a, 0x10, 0x07
};

var nonce = new byte[]
{
    0xcd, 0x7c, 0xf6, 0x7b, 0xe3, 0x9c, 0x79, 0x4a, 0x79, 0xc0, 0xd1, 0x10
};

var ad = new byte[]
{
    0x87, 0xe2, 0x29, 0xd4, 0x50, 0x08, 0x45, 0xa0, 0x79, 0xc0
};

var m = new byte[]
{
    0x86, 0xd0, 0x99, 0x74, 0x84, 0x0b, 0xde, 0xd2, 0xa5, 0xca
};

// check CPU support
if (SecretAeadAes.IsAvailable())
{
    //encrypt with Additional Data
    var encrypted = SecretAeadAes.Encrypt(m, nonce, key, ad);
    //decrypt with Additional Data
    var decrypted = SecretAeadAes.Decrypt(encrypted, nonce, key, ad);
}
else
{
    Console.WriteLine("Missing AES support");
}
```

## Example without Additional Data

```csharp
var key = new byte[]
{
    0x42, 0x90, 0xbc, 0xb1, 0x54, 0x17, 0x35, 0x31, 0xf3, 0x14, 0xaf,
    0x57, 0xf3, 0xbe, 0x3b, 0x50, 0x06, 0xda, 0x37, 0x1e, 0xce, 0x27,
    0x2a, 0xfa, 0x1b, 0x5d, 0xbd, 0xd1, 0x10, 0x0a, 0x10, 0x07
};

var nonce = new byte[]
{
    0xcd, 0x7c, 0xf6, 0x7b, 0xe3, 0x9c, 0x79, 0x4a, 0x79, 0xc0, 0xd1, 0x10
};

var m = new byte[]
{
    0x86, 0xd0, 0x99, 0x74, 0x84, 0x0b, 0xde, 0xd2, 0xa5, 0xca
};

// check CPU support
if (SecretAeadAes.IsAvailable())
{
    //encrypt with Additional Data
    var encrypted = SecretAeadAes.Encrypt(m, nonce, key);
    //decrypt with Additional Data
    var decrypted = SecretAeadAes.Decrypt(encrypted, nonce, key);
}
else
{
    Console.WriteLine("Missing AES support");
}
```

## Purpose

This operation:
- Encrypts a message with a key and a nonce to keep it confidential
- Computes an authentication tag. This tag is used to make sure that the message, as well as optional, non-confidential (non-encrypted) data, haven't been tampered with.

A typical use case for additional data is to store protocol-specific metadata about the message, such as its length and encoding.

It can also be used as a MAC, with an empty message.

Decryption will never be performed, even partially, before verification.

When supported by the CPU, AES-GCM is the fastest AEAD cipher available in this library.

## Limitations

The current implementation of this construction is hardware-accelerated and requires the Intel SSSE3 extensions, as well as the `aesni` and `pclmul` instructions.

Intel Westmere processors (introduced in 2010) and newer meet the requirements.

There are no plans to support non hardware-accelerated implementations of AES-GCM. If portability is a concern, use ChaCha20-Poly1305 instead.

## Random Helpers

```csharp
public static byte[] GenerateNonce()
```
**Namespace:** `Sodium.SecretAeadAes`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 12 byte nonce.

## Usage

### Check CPU support

```csharp
public static bool IsAvailable()
```
*This is the .NET equivalent of `crypto_aead_aes256gcm_is_available`.*

**Namespace:** `Sodium.SecretAeadAes`

Returns `true` if the current CPU supports the AES256-GCM implementation, and `false` if it doesn't.

### Encrypt

```csharp
public static byte[] Encrypt(byte[] message, byte[] nonce, byte[] key, byte[] additionalData = null)
```
*This is the .NET equivalent of `crypto_aead_aes256gcm_encrypt`.*

**Namespace:** `Sodium.SecretAeadAes`

The `Encrypt()` function encrypts a `message` using a secret `key` and a public `nonce`.

The `key` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `nonce` must be `12` bytes, otherwise the function throws a `NonceOutOfRangeException`.

The public `nonce` should never ever be reused with the same key. The recommended way to generate it is to use `SecretAead.GenerateNonce()` for the first message, and increment it for each subsequent message using the same key.

The `additionalData` may be `null` (if no additional data is required), or between `0` and `16` bytes, otherwise the function throws a `AdditionalDataOutOfRangeException`.

The function returns a byte array on success, or throws an `CryptographicException()` on failure.

### Decrypt

```csharp
public static byte[] Decrypt(byte[] cipher, byte[] nonce, byte[] key, byte[] additionalData = null)
```
*This is the .NET equivalent of `crypto_aead_aes256gcm_decrypt`.*

**Namespace:** `Sodium.SecretAeadAes`

The `Decrypt()` function decrypts a message `cipher` using a secret `key` and a public `nonce`.

The `key` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `nonce` must be `12` bytes, otherwise the function throws a `NonceOutOfRangeException`.

The `additionalData` may be `null` (if no additional data is required), or between `0` and `16` bytes, otherwise the function throws a `AdditionalDataOutOfRangeException`.

The function returns a byte array on success, or throws an `CryptographicException()` on failure.

## Notes

The nonce is 96 **bits** long. In order to prevent nonce reuse, if a key is being reused, it is recommended to increment the previous nonce instead of generating a random nonce for each message. To prevent nonce reuse in a client-server protocol, either use different keys for each direction, or make sure that a bit is masked in one direction, and set in the other.
