# The SHA-2 hash functions family

The SHA-256 and SHA-512 functions are provided for interoperability with other applications.

These functions are not keyed and are thus deterministic. In addition, they are vulnerable to length extension attacks.

If you are looking for a generic hash function and not specifically SHA-2, using [`GenericHash`](../hashing/generic_hashing.md) (Blake2b) might be a better choice.

## SHA-256 example

```csharp
const string MESSAGE = "Message to hash";

//returns a 32 byte hash
var hash = CryptoHash.Sha256(MESSAGE);	  
```

## SHA-512 example

```csharp
const string MESSAGE = "Message to hash";

//returns a 64 byte hash
var hash = CryptoHash.Sha512(MESSAGE);	  
```

## Usage

### Default

```csharp
public static byte[] Hash(byte[] message)

//there exists an overloaded version:
public static byte[] Hash(string message)
```
*This is the .NET equivalent of `crypto_hash`.*

**Namespace:** `Sodium.CryptoHash`

The `Hash()` function hashes a `message`, with the default defined by libsodium, what`s currently the `SHA-512` hash function.

The function returns a `64` byte array.

### SHA-256

```csharp
public static byte[] Sha256(byte[] message)

//there exists an overloaded version:
public static byte[] Sha256(string message)
```
*This is the .NET equivalent of `crypto_hash_sha256`.*

**Namespace:** `Sodium.CryptoHash`

The `Sha256()` function hashes a `message`, with the `SHA-256` hash function.

The function returns a `32` byte array.

### SHA-512

```csharp
public static byte[] Sha512(byte[] message)

//there exists an overloaded version:
public static byte[] Sha512(string message)
```
*This is the .NET equivalent of `crypto_hash_sha512`.*

**Namespace:** `Sodium.CryptoHash`

The `Sha512()` function hashes a `message`, with the `SHA-512` hash function.

The function returns a `64` byte array.

