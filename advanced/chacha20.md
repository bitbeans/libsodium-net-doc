# ChaCha20

ChaCha20 is a stream cipher developed by Daniel J. Bernstein that expands a 256-bit key into 2^64 randomly accessible streams, each containing 2^64 randomly accessible 64-byte (512 bits) blocks. It is a variant of Salsa20 with better diffusion.

ChaCha20 doesn't require any lookup tables and avoids the possibility of timing attacks.

Internally, ChaCha20 works like a block cipher used in counter mode. It uses a dedicated 64-bit block counter to avoid incrementing the nonce after each block.

## Example

```csharp
const string MESSAGE = "Test message to encrypt";
var nonce = StreamEncryption.GenerateNonceChaCha20();
var key = StreamEncryption.GenerateKey();

//encrypt it
var encrypted = StreamEncryption.EncryptChaCha20(MESSAGE, nonce, key);

//decrypt it
var decrypted = StreamEncryption.DecryptChaCha20(encrypted, nonce, key);
```

## Random Helpers

```csharp
public static byte[] GenerateKey()
```
**Namespace:** `Sodium.StreamEncryption`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 32 byte key.

```csharp
public static byte[] GenerateNonceChaCha20()
```
**Namespace:** `Sodium.StreamEncryption`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 8 byte nonce.

## Usage

### Encrypt

```csharp
public static byte[] EncryptChaCha20(byte[] message, byte[] nonce, byte[] key)

//there exists an overloaded version:
public static byte[] EncryptChaCha20(string message, byte[] nonce, byte[] key)
```
*This is the .NET equivalent of `crypto_stream_chacha20_xor`.*

**Namespace:** `Sodium.StreamEncryption`

The `EncryptChaCha20()` method encrypts a `message`, with a `key` and a `nonce`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The `nonce` must be `8` bytes, otherwise the method throws a `NonceOutOfRangeException`.

The method returns a byte array on success, or throws an `CryptographicException()` on failure.

The returned ciphertext is the message combined with the output of the stream cipher using the XOR operation, and doesn't include any authentication tag.

### Decrypt

```csharp
public static byte[] DecryptChaCha20(byte[] cipherText, byte[] nonce, byte[] key)

//there exists an overloaded version:
public static byte[] DecryptChaCha20(string cipherText, byte[] nonce, byte[] key)
```
*This is the .NET equivalent of `crypto_stream_chacha20_xor`.*

**Namespace:** `Sodium.StreamEncryption`

The `DecryptChaCha20()` method encrypts a `message`, with a `key` and a `nonce`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The `nonce` must be `8` bytes, otherwise the method throws a `NonceOutOfRangeException`.

The method returns a byte array on success, or throws an `CryptographicException()` on failure.

## Notes

The nonce is 64 bits long. In order to prevent nonce reuse, if a key is being reused, it is recommended to increment the previous nonce instead of generating a random nonce every time a new stream is required.

