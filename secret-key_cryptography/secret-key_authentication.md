# Secret-key authentication

## Example

```csharp
const string message = "Arbitrary message to authenticate";
var key = SecretKeyAuth.GenerateKey(); //32 byte key

//returns a 32 byte authentication code
var signature = SecretKeyAuth.Sign(message, key);

if (SecretKeyAuth.Verify(message, signature, key))
{
	//message ok
}
```

## Purpose

This operation computes an authentication tag for a message and a secret key, and provides a way to verify that a given tag is valid for a given message and a key.

The function computing the tag deterministic: the same (message, key) tuple will always produce the same output.

However, even if the message is public, knowing the key is required in order to be able to compute a valid tag. Therefore, the key should remain confidential. The tag, however, can be public.

A typical use case is:
- `A` prepares a message, add an authentication tag, sends it to `B`
- `A` doesn't store the message
- Later on, `B` sends the message and the authentication tag to `A`
- `A` uses the authentication tag to verify that it created this message.

This operation does *not* encrypt the message. It only computes and verifies an authentication tag.


## Random Helpers

```csharp
public static byte[] GenerateKey()
```
**Namespace:** `Sodium.SecretKeyAuth`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 32 byte key.

## Usage

### Sign

```csharp
public static byte[] Sign(byte[] message, byte[] key)

//there exists an overloaded version:
public static byte[] Sign(string message, byte[] key)
```
*This is the .NET equivalent of `crypto_auth`.*

**Namespace:** `Sodium.SecretKeyAuth`

The `Sign()` function signs a `message` with a `key`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The function returns a `32` byte signature (authentication code) as a byte array.

#### Verify

```csharp
public static bool Verify(byte[] message, byte[] signature, byte[] key)

//there exists an overloaded version:
public static bool Verify(string message, byte[] signature, byte[] key)
```
*This is the .NET equivalent of `crypto_auth_verify`.*

**Namespace:** `Sodium.SecretKeyAuth`

The `Verify()` function verifies a `message` with a `signature` and a `key` signed by `Sign()`.

The `signature` must be `32` bytes, otherwise the method throws a `SignatureOutOfRangeException`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The method returns `true` on success, otherwise `false` on failure.


## Algorithm details

- HMAC-SHA512256

