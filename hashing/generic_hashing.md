# Generic hashing

## GenericHash without a key

```csharp
var message = "Arbitrary data to hash";

//returns a 32 byte hash
var generichash = GenericHash.Hash(message, null, 32);
```

## GenericHash with a key

```csharp
var key = GenericHash.GenerateKey(); //64 byte key
var message = "Arbitrary data to hash";

//returns a 64 byte hash
var generichash = GenericHash.Hash(message, key, 64);
```

## GenericHash without a key but a salt and a personal

```csharp
var salt = "5b6b41ed9b343fe0";
var personal = "5126fb2a37400d2a";
var message = "Arbitrary data to hash";

//returns a 64 byte hash
var generichash = GenericHash.HashSaltPersonal(message, null, salt, personal);
```

## GenericHash with a key, a salt and a personal

```csharp
var key = GenericHash.GenerateKey(); //64 byte key
var salt = "5b6b41ed9b343fe0";
var personal = "5126fb2a37400d2a";
var message = "Arbitrary data to hash";

//returns a 64 byte hash
var generichash = GenericHash.HashSaltPersonal(message, key, salt, personal);
```

## Multi-part examples

**Note:** Only libsodium's simplified interface is currently supported; the streaming interface is not implemented at this time.

## Purpose

This function computes a fixed-length fingerprint for an arbitrary long message.

Sample use cases:
- File integrity checking
- Creating unique identifiers to index arbitrary long data

## Random Helpers

```csharp
public static byte[] GenerateKey()
```
**Namespace:** `Sodium.GenericHash`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 64 byte key.

## Usage

### Hash

```csharp
public static byte[] Hash(byte[] message, byte[] key, int bytes)

//there exists some overloaded versions:
public static byte[] Hash(string message, byte[] key, int bytes)
public static byte[] Hash(string message, string key, int bytes)
```

*This is the .NET equivalent of `crypto_generichash`.*

**Namespace:** `Sodium.GenericHash`

The `Hash()` method returns a fingerprint of the `message` as a byte array.
The output size can be chosen by the application.

The minimum recommended output size is `64`. This size makes it practically impossible for two messages to produce the same fingerprint.

But for specific use cases, the size can be any value between `16` (included) and `64` (included) otherwise the method throws a `BytesOutOfRangeException`.

The `key` can be `NULL`. In this case, a message will always have the same fingerprint, similar to the `MD5` or `SHA-1` functions for which `Hash()` is a faster and more secure alternative.

But a key can also be specified. A message will always have the same fingerprint for a given key, but different keys used to hash the same message are very likely to produce distinct fingerprints.

In particular, the key can be used to make sure that different applications generate different fingerprints even if they process the same data.

The recommended key size is `64` bytes.

The key size can by any value between `16` (included) and `64` (included) otherwise the method throws a `KeyOutOfRangeException`.

### HashSaltPersonal

```csharp
public static byte[] HashSaltPersonal(byte[] message, byte[] key, byte[] salt, byte[] personal)

//there exists an overloaded version:
public static byte[] HashSaltPersonal(string message, string key, string salt, string personal)
```
*This is the .NET equivalent of `crypto_generichash_blake2b_salt_personal`.*

**Namespace:** `Sodium.GenericHash`

The `HashSaltPersonal()` method returns a fingerprint based on a salt, a personal and an optional key.

The output size is always `64` byte.

The `key` can be `NULL`, or must be in range between `16` (included) and `64` (included), otherwise the method throws a `KeyOutOfRangeException`.

The recommended key size is `64` bytes.

The `salt` must be `16` bytes, otherwise the method throws a `SaltOutOfRangeException`.

The `personal` must be `16` bytes, otherwise the method throws a `PersonalOutOfRangeException`.

If there is no `message`, `salt` or `personal` specified the method will throw an `ArgumentNullException`.

## Algorithm details

Blake2b

## Notes

Unlike functions such as MD5, SHA-1 and SHA-256, this function is safe against hash length extension attacks.
