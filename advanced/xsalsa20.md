# XSalsa20

XSalsa20 is a stream cipher based upon Salsa20 but with a much longer nonce: 192 bits instead of 64 bits.

XSalsa20 uses a 256-bit key as well as the first 128 bits of the nonce in order to compute a subkey. This subkey, as well as the remaining 64 bits of the nonce, are the parameters of the Salsa20 function used to actually generate the stream.

Like Salsa20, XSalsa20 is immune to timing attacks and provides its own 64-bit block counter to avoid incrementing the nonce after each block.

But with XSalsa20's longer nonce, it is safe to generate nonces using `StreamEncryption.GenerateNonce()` for every message encrypted with the same key without having to worry about a collision.

Sodium exposes XSalsa20 with 20 rounds as the `Encrypt()` operation.


## Example

```csharp
const string MESSAGE = "Test message to encrypt";
var nonce = StreamEncryption.GenerateNonce();
var key = StreamEncryption.GenerateKey();

//encrypt it
var encrypted = StreamEncryption.Encrypt(MESSAGE, nonce, key);

//decrypt it
var decrypted = StreamEncryption.Decrypt(encrypted, nonce, key);
```

## Random Helpers

```csharp
public static byte[] GenerateKey()
```
**Namespace:** `Sodium.StreamEncryption`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 32 byte key.

```csharp
public static byte[] GenerateNonce()
```
**Namespace:** `Sodium.StreamEncryption`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 24 byte nonce.

## Usage

### Encrypt

```csharp
public static byte[] Encrypt(byte[] message, byte[] nonce, byte[] key)

//there exists an overloaded version:
public static byte[] Encrypt(string message, byte[] nonce, byte[] key)
```
*This is the .NET equivalent of `crypto_stream_xor`.*

**Namespace:** `Sodium.StreamEncryption`

The `Encrypt()` method encrypts a `message`, with a `key` and a `nonce`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the method throws a `NonceOutOfRangeException`.

The method returns a byte array on success, or throws an `CryptographicException()` on failure.

The returned ciphertext is the message combined with the output of the stream cipher using the XOR operation, and doesn't include any authentication tag.

### Decrypt

```csharp
public static byte[] Decrypt(byte[] cipherText, byte[] nonce, byte[] key)

//there exists an overloaded version:
public static byte[] Decrypt(string cipherText, byte[] nonce, byte[] key)
```
*This is the .NET equivalent of `crypto_stream_xor`.*

**Namespace:** `Sodium.StreamEncryption`

The `Decrypt()` method encrypts a `message`, with a `key` and a `nonce`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the method throws a `NonceOutOfRangeException`.

The method returns a byte array on success, or throws an `CryptographicException()` on failure.
 No newline at end of file

