# Public-key authenticated encryption

## Example combined mode

```csharp
const string MESSAGE = "hello bob";
var alice = PublicKeyBox.GenerateKeyPair();
var bob = PublicKeyBox.GenerateKeyPair();

var nonce = PublicKeyBox.GenerateNonce();

//alice encrypts a message for bob
var encrypted = PublicKeyBox.Create(MESSAGE, nonce, alice.PrivateKey, bob.PublicKey);
//bob decrypt the message
var decrypted = PublicKeyBox.Open(encrypted, nonce, bob.PrivateKey, alice.PublicKey);
```

## Purpose

Using public-key authenticated encryption, Bob can encrypt a confidential message specifically for Alice, using Alice's public key.

Using Bob's public key, Alice can verify that the encrypted message was actually created by Bob and was not tampered with, before eventually decrypting it.

Alice only needs Bob's public key, the nonce and the ciphertext. Bob should never ever share his secret key, even with Alice.

And in order to send messages to Alice, Bob only needs Alice's public key. Alice should never ever share her secret key either, even with Bob.

Alice can reply to Bob using the same system, without having to generate a distinct key pair.

The nonce doesn't have to be confidential, but it should be used with just one invokation of `Open()` for a particular pair of public and secret keys.

One easy way to generate a nonce is to use `PublicKeyBox.GenerateNonce()`, considering the size of nonces the risk of any random collisions is negligible. For some applications, if you wish to use nonces to detect missing messages or to ignore replayed messages, it is also ok to use a simple incrementing counter as a nonce.

When doing so you must ensure that the same value can never be re-used (for example you may have multiple threads or even hosts generating messages using the same key pairs).

This system provides mutual authentication. However, a typical use case is to secure communications between a server, whose public key is known in advance, and clients connecting anonymously.

## Random Helpers

```csharp
public static KeyPair GenerateKeyPair()
```
**Namespace:** `Sodium.PublicKeyBox`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a key pair based on a random seed.

```csharp
public static KeyPair GenerateKeyPair(byte[] privateKey)
```
**Namespace:** `Sodium.PublicKeyBox`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a key pair based on the provided private key.

The `privateKey` seed must be `32` bytes, otherwise the function throws a `SeedOutOfRangeException`.

`libsodium-net` [KeyPair](../helpers/README.md)

```csharp
public static byte[] GenerateNonce()
```
**Namespace:** `Sodium.PublicKeyBox`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 24 byte nonce.

## Combined mode

In combined mode, the authentication tag and the encrypted message are stored together. This is usually what you want, as

### Encrypt

```csharp
public static byte[] Create(byte[] message, byte[] nonce, byte[] secretKey, byte[] publicKey)

//there exists an overloaded version:
public static byte[] Create(string message, byte[] nonce, byte[] secretKey, byte[] publicKey)
```
*This is the .NET equivalent of `crypto_box_easy`.*

**Namespace:** `Sodium.PublicKeyBox`

The `Create()` function encrypts a `message`, with a recipient's `publicKey`, a sender's `secretKey` and a `nonce`.

The `secretKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `publicKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the function throws a `NonceOutOfRangeException`.

The function returns a byte array (encrypted message) on success, or throws an `CryptographicException()` on failure.

### Decrypt

```csharp
public static byte[] Open(byte[] cipherText, byte[] nonce, byte[] secretKey, byte[] publicKey)
```
*This is the .NET equivalent of `crypto_box_open_easy`.*

**Namespace:** `Sodium.PublicKeyBox`

The `Open()` function decrypts a message `cipherText`, with a recipient's `secretKey`, a sender's `publicKey` and a `nonce`.

The `secretKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `publicKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the function throws a `NonceOutOfRangeException`.

The function returns a byte array (unencrypted message) on success, or throws an `CryptographicException()` on failure.

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
public static DetachedBox CreateDetached(byte[] message, byte[] nonce, byte[] secretKey, byte[] publicKey)

//there exists an overloaded version:
public static DetachedBox CreateDetached(string message, byte[] nonce, byte[] secretKey, byte[] publicKey)
```
*This is the .NET equivalent of `crypto_box_detached`.*

**Namespace:** `Sodium.PublicKeyBox`

The `CreateDetached()` function encrypts a `message`, with a recipient's `publicKey`, a sender's `secretKey` and a `nonce`.

The `secretKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `publicKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the function throws a `NonceOutOfRangeException`.

The function returns a `DetachedBox` on success, or throws an `CryptographicException()` on failure.

### Decrypt

```csharp
public static byte[] OpenDetached(byte[] cipherText, byte[] mac, byte[] nonce, byte[] secretKey, byte[] publicKey)

//there exists some overloaded versions:
public static byte[] OpenDetached(string cipherText, byte[] mac, byte[] nonce, byte[] secretKey, byte[] publicKey)
public static byte[] OpenDetached(DetachedBox detached, byte[] nonce, byte[] secretKey, byte[] publicKey)
```
*This is the .NET equivalent of `crypto_box_open_detached`.*

**Namespace:** `Sodium.PublicKeyBox`

The `OpenDetached()` function decrypts a message `cipherText`, with a recipient's `secretKey`, a sender's `publicKey`, a`mac` and a `nonce`.

The `secretKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `publicKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `nonce` must be `24` bytes, otherwise the function throws a `NonceOutOfRangeException`.

The `mac` must be `16` bytes, otherwise the method throws a `MacOutOfRangeException`.

The same exceptions will be thrown on the overloaded version which used the `detached` object.

The function returns a byte array (unencrypted message) on success, or throws an `CryptographicException()` on failure.

## Algorithm details

- Key exchange: Curve25519
- Encryption: XSalsa20 stream cipher
- Authentication: Poly1305 MAC
