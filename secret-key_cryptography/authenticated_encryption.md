# Secret-key authenticated encryption

## Example

```csharp
var nonce = Sodium.SecretBox.GenerateNonce(); //24 byte nonce
var key = Sodium.SecretBox.GenerateKey(); //32 byte key
var message = "This is an easy example";

//encrypt the message
var ciphertext = SecretBox.Create(message, nonce, key);
//decrypt it again
var message2 = SecretBox.Open(ciphertext, nonce, key);
```

## Purpose

This operation:
- Encrypts a message with a key and a nonce to keep it confidential
- Computes an authentication tag. This tag is used to make sure that the message hasn't been tampered with before decrypting it.

A single key is used both to encrypt/sign and verify/decrypt messages. For this reason, it is critical to keep the key confidential.

The nonce doesn't have to be confidential, but it should never ever be reused with the same key.

The easiest way to generate a nonce is to use `Sodium.SecretBox.GenerateNonce()` which uses `Sodium.SodiumCore.GetRandomBytes()` with the proper length.

## Random Helpers

```csharp
public static byte[] GenerateKey()
```
**Namespace:** `Sodium.SecretBox`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 32 byte key.

```csharp
public static byte[] GenerateNonce()
```
**Namespace:** `Sodium.SecretBox`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 24 byte nonce.

## Combined mode

In combined mode, the authentication tag and the encrypted message are stored together. This is usually what you want, as

### Encrypt

```csharp
public static byte[] Create(byte[] message, byte[] nonce, byte[] key)

//there exists an overloaded version:
public static byte[] Create(string message, byte[] nonce, byte[] key)
```
*This is the .NET equivalent of `crypto_secretbox_easy`.*

**Namespace:** `Sodium.SecretBox`

The `Create()` method encrypts a `message`, with a `key` and a `nonce`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the method throws a `NonceOutOfRangeException`.

This method writes the authentication tag, whose length is `16` bytes, in the retuned byte array, immediately followed by the encrypted message.

The method returns a byte array on success, or throws an `CryptographicException()` on failure.

### Decrypt

```csharp
public static byte[] Open(byte[] cipherText, byte[] nonce, byte[] key)

//there exists an overloaded version:
public static byte[] Open(string cipherText, byte[] nonce, byte[] key)
```
*This is the .NET equivalent of `crypto_secretbox_open_easy`.*

**Namespace:** `Sodium.SecretBox`

The `Open()` method decrypts a `cipherText` produced by `Create()`, with a `key` and a `nonce`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the method throws a `NonceOutOfRangeException`.

The `cipherText` is a combination of an authentication tag, whose length is `16` bytes immediately followed by the encrypted message.

The method returns a byte array on success, or throws an `CryptographicException()` on failure.


## Detached mode

Some applications may need to store the authentication tag and the encrypted message at different locations.

For this specific use case, "detached" variants of the functions above are available.

**Note:** Because `libsodium-net` do not work with pointers like `libsodium` we make use of a `DetachedBox` object instead:

```csharp
/// <param name="cipherText">The cipher.</param>
/// <param name="mac">The 16 byte mac.</param>
public DetachedBox(byte[] cipherText, byte[] mac)
{
  CipherText = cipherText;
  Mac = mac;
}
```

### Encrypt
```csharp
public static DetachedBox CreateDetached(byte[] message, byte[] nonce, byte[] key)

//there exists an overloaded version:
public static DetachedBox CreateDetached(string message, byte[] nonce, byte[] key)
```
*This is the .NET equivalent of `crypto_secretbox_detached`.*

**Namespace:** `Sodium.SecretBox`

The `CreateDetached()` method encrypts a `message`, with a `key` and a `nonce`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the method throws a `NonceOutOfRangeException`.

The method returns a `DetachedBox` on success, or throws an `CryptographicException()` on failure.

### Decrypt

```csharp
public static byte[] OpenDetached(byte[] cipherText, byte[] mac, byte[] nonce, byte[] key)

//there exists some overloaded versions:
public static byte[] OpenDetached(string cipherText, byte[] mac, byte[] nonce, byte[] key)
public static byte[] OpenDetached(DetachedBox detached, byte[] nonce, byte[] key)
```
*This is the .NET equivalent of `crypto_secretbox_open_detached`.*

**Namespace:** `Sodium.SecretBox`

The `OpenDetached()` method decrypts a `cipherText` produced by `CreateDetached()`, with a `mac`, `key` and a `nonce`.

The `mac` must be `16` bytes, otherwise the method throws a `MacOutOfRangeException`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the method throws a `NonceOutOfRangeException`.

The same exceptions will be thrown on the overloaded version which used the `detached` object.

The method returns a byte array on success, or throws an `CryptographicException()` on failure.


## Algorithm details

- Encryption: XSalsa20 stream cipher
- Authentication: Poly1305 MAC
