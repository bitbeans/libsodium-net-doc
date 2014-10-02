# HMAC-SHA-2

Keyed message authentication using HMAC-SHA-256, HMAC-SHA-512 and HMAC-SHA512/256 (truncated HMAC-SHA-512) are provided.


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

## Random Helpers

```csharp
public static byte[] GenerateKey()
```
**Namespace:** `Sodium.SecretKeyAuth`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 32 byte key.

## Usage

### HMAC-SHA-256

#### Sign

```csharp
public static byte[] SignHmacSha256(byte[] message, byte[] key)

//there exists an overloaded version:
public static byte[] SignHmacSha256(string message, byte[] key)
```
*This is the .NET equivalent of `crypto_auth_hmacsha512`.*

**Namespace:** `Sodium.SecretKeyAuth`

The `SignHmacSha256()` function signs a `message` with a `key`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The function returns a `32` byte signature (authentication code) as a byte array.

#### Verify

```csharp
public static bool VerifyHmacSha256(byte[] message, byte[] signature, byte[] key)

//there exists an overloaded version:
public static bool VerifyHmacSha256(string message, byte[] signature, byte[] key)
```
*This is the .NET equivalent of `crypto_auth_hmacsha512_verify`.*

**Namespace:** `Sodium.SecretKeyAuth`

The `VerifyHmacSha256()` function verifies a `message` with a `signature` and a `key` signed by `Sign()`.

The `signature` must be `32` bytes, otherwise the method throws a `SignatureOutOfRangeException`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The method returns `true` on success, otherwise `false` on failure.

### HMAC-SHA-512

#### Sign

```csharp
public static byte[] SignHmacSha512(byte[] message, byte[] key)

//there exists an overloaded version:
public static byte[] SignHmacSha512(string message, byte[] key)
```
*This is the .NET equivalent of `crypto_auth_hmacsha512`.*

**Namespace:** `Sodium.SecretKeyAuth`

The `SignHmacSha512()` function signs a `message` with a `key`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The function returns a `64` byte signature (authentication code) as a byte array.

#### Verify

```csharp
public static bool VerifyHmacSha512(byte[] message, byte[] signature, byte[] key)

//there exists an overloaded version:
public static bool VerifyHmacSha512(string message, byte[] signature, byte[] key)
```
*This is the .NET equivalent of `crypto_auth_hmacsha512_verify`.*

**Namespace:** `Sodium.SecretKeyAuth`

The `VerifyHmacSha512()` function verifies a `message` with a `signature` and a `key` signed by `Sign()`.

The `signature` must be `64` bytes, otherwise the method throws a `SignatureOutOfRangeException`.

The `key` must be `32` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The method returns `true` on success, otherwise `false` on failure.

### HMAC-SHA-512/256

HMAC-SHA-512/256 is implemented as HMAC-SHA-512 with the output truncated to 256 bits. This is slightly faster than HMAC-SHA-256.

#### Sign

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


## Notes

- `SignHmacSha256()` can be used to create AWS HmacSHA256 request signatures.

