# Short-input hashing

## Example

```csharp
var key = ShortHash.GenerateKey(); //16 byte key
var message = "This is an easy example";

//returns a 8 byte hash
var shorthash = ShortHash.Hash(message, key);
```

## Purpose

Many applications and programming language implementations were
recently found to be vulnerable to denial-of-service attacks when
a hash function with weak security guarantees, such as Murmurhash 3, was
used to construct a hash table.

In order to address this, libsodium-net provides the `Sodium.ShortHash` function,
which outputs short but unpredictable (without knowing the secret key)
values suitable for picking a list in a hash table for a given key.

This function is optimized for short inputs.

The output of this function is only 64 **bits**. Therefore, it should *not* be considered collision-resistant.

Use cases:
- Hash tables
- Probabilistic data structures such as Bloom filters
- Integrity checking in interactive protocols

## Random Helpers

```csharp
public static byte[] GenerateKey()
```
**Namespace:** `Sodium.ShortHash`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a `16` byte key.

## Usage

```csharp
public static byte[] Hash(byte[] message, byte[] key)

//there exists some overloaded versions:
public static byte[] Hash(string message, byte[] key)
public static byte[] Hash(string message, string key)
```

*This is the .NET equivalent of `crypto_shorthash`.*

**Namespace:** `Sodium.ShortHash`

The `Hash()` method computes a `8` byte fingerprint for the `message`, using a `16` byte `key`.

The `key` must be `16` bytes, otherwise the method throws a `KeyOutOfRangeException`.

The same message hashed with the same key will always produce the same output.


## Algorithm details

SipHash-2-4

