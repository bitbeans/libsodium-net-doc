# Password hashing

## Example 1: key derivation

```csharp
const string PASSWORD = "Correct Horse Battery Staple";
const string SALT = "qa~t](84z<1t<1oz:ik.@IRNyhG=8q(o";
const long OUTPUT_LENGTH = 512;

//this will produce a 512 byte hash
var hash = PasswordHash.ScryptHashBinary(PASSWORD, SALT, PasswordHash.Strength.Moderate, OUTPUT_LENGTH);
```

## Example 2: password storage

```csharp
const string PASSWORD = "Correct Horse Battery Staple";

//this will produce a 32 byte hash
var hash = PasswordHash.ScryptHashString(PASSWORD, PasswordHash.Strength.Moderate);

if (PasswordHash.ScryptHashStringVerify(hash, PASSWORD) 
{
	//correct password
}
```

## Purpose

Secret keys used to encrypt or sign confidential data have to be chosen from a very large keyspace. However, passwords are usually short, human-generated strings, making dictionary attacks practical.

The `PasswordHash` operation derives a secret key of any size from a password and a salt.

- The generated key has the size defined by the application, no matter what the password length is.
- The same password hashed with same parameters will always produce the same key.
- The same password hashed with different salts will produce different keys.
- The function deriving a key from a password and a salt is CPU intensive and intentionally requires a fair amount of memory. Therefore, it mitigates brute-force attacks by requiring a significant effort to verify each password.

Common use cases:
- Protecting an on-disk secret key with a password,
- Password storage, or rather: storing what it takes to verify a password without having to store the actual password.

## Random Helpers

```csharp
public static byte[] GenerateSalt()
```
**Namespace:** `Sodium.PasswordHash`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a 32 byte salt.

## Predefined Values

```csharp
/// <summary>Represents predefined and useful limits for ScryptHashBinary() and ScryptHashString().</summary>
public enum Strength
{
  /// <summary>For interactive sessions (fast: uses 16MB of RAM).</summary>
  Interactive,
  /// <summary>For normal use (moderate: uses 100MB of RAM).</summary>
  Moderate,
  /// <summary>For highly sensitive data (slow: uses more than 1GB of RAM).</summary>
  Sensitive
}
```

**Namespace:** `Sodium.PasswordHash`

## Key derivation

```csharp
public static byte[] ScryptHashBinary(byte[] password, byte[] salt, long opsLimit, int memLimit, long outputLength = SCRYPT_SALSA208_SHA256_SALTBYTES)

//there exists some overloaded versions:
public static byte[] ScryptHashBinary(string password, string salt, long opsLimit, int memLimit, long outputLength = SCRYPT_SALSA208_SHA256_SALTBYTES)
public static byte[] ScryptHashBinary(byte[] password, byte[] salt, Strength limit = Strength.Interactive, long outputLength = SCRYPT_SALSA208_SHA256_SALTBYTES)
public static byte[] ScryptHashBinary(string password, string salt, Strength limit = Strength.Interactive, long outputLength = SCRYPT_SALSA208_SHA256_SALTBYTES)
```

*This is the .NET equivalent of `crypto_pwhash_scryptsalsa208sha256`.*

**Namespace:** `Sodium.PasswordHash`

The `ScryptHashBinary()` function derives an `outputLength` bytes long key from a `password` and a `salt` whose fixed length is `32` bytes.

`opslimit` represents a maximum amount of computations to perform. Raising this number will make the function require more CPU cycles to compute a key.

`memlimit` is the maximum amount of RAM that the function will use, in bytes. It is highly recommended to allow the function to use at least 16 megabytes.

In **libsodium-net** are some predefined values: `Strength.Interactive`, `Strength.Moderate` and `Strength.Sensitive`.

For interactive sessions, `Strength.Interactive` provide a safe base line for the `opsLimit` and `memLimit` parameters. However, using higher values may improve security. The `Strength.Interactive` uses `16777216` bytes of dedicated RAM and `524288` CPU cycles.

For normal data, `Strength.Moderate` can be used. These setting use 100MB of RAM, and takes roughly 5 seconds to complete. The `Strength.Moderate` uses `100000000` bytes of dedicated RAM and `8388608` CPU cycles.

For highly sensitive data, `Strength.Sensitive` can be used as an alternative. But with these parameters, deriving a key takes more than 10 seconds on a 2.8 Ghz Core i7 CPU and requires up to 1 gigabyte of dedicated RAM. The `Strength.Sensitive` uses `1073741824` bytes of dedicated RAM and `33554432` CPU cycles.

The `salt` should be unpredictable. `GenerateSalt()` is the easiest way to fill the bytes of the salt.

Keep in mind that in order to produce the same key from the same password, the same salt, and the same values for `opslimit` and `memlimit` have to be used. Therefore, these parameters have to be stored for each user.

The `salt` must be `32` bytes, otherwise the function throws a `SaltOutOfRangeException`.

The `opsLimit`, `memlimit` and `outputLength` must be greater `0`, otherwise the function throws an `ArgumentOutOfRangeException`.

If there is no `password` or `salt` specified the function will throw an `ArgumentNullException`.

The function returns a byte array on success, and throws an `OutOfMemoryException()` if the computation didn't complete, usually because the operating system refused to allocate the amount of requested memory.


## Password storage

### Hash

```csharp
public static string ScryptHashString(string password, long opsLimit, int memLimit)

//there exists an overloaded version:
public static string ScryptHashString(string password, Strength limit = Strength.Interactive)						   
```
*This is the .NET equivalent of `crypto_pwhash_scryptsalsa208sha256_str`.*

**Namespace:** `Sodium.PasswordHash`


The `ScryptHashString()` function returns an UTF8 encoded string, which includes:
- the result of a memory-hard, CPU-intensive hash function applied to the `password`
- the automatically generated salt used for the previous computation
- the other parameters required to verify the password: `opsLimit` and `memLimit`.

The `opsLimit` and `memLimit` values for `ScryptHashString()` are the same as for `ScryptHashBinary`. So you also can use the predefined values.

The output string is zero-terminated. No additional information has to be stored in order to verify the password.

The `opsLimit` and `memlimit` must be greater `0`, otherwise the function throws an `ArgumentOutOfRangeException`.

If there is no `password` specified the function will throw an `ArgumentNullException`.

The function returns a byte array on success, and throws an `OutOfMemoryException()` if the computation didn't complete, usually because the operating system refused to allocate the amount of requested memory.

### Verify

```csharp
public static bool ScryptHashStringVerify(byte[] hash, byte[] password)

//there exists an overloaded version:
public static bool ScryptHashStringVerify(string hash, string password)					   
```
*This is the .NET equivalent of `crypto_pwhash_scryptsalsa208sha256_str_verify`.*

**Namespace:** `Sodium.PasswordHash`


This function verifies that the password `hash` is a valid password verification string (as generated by `ScryptHashString()`) for `password`.

If there is no `hash` or `password` specified the function will throw an `ArgumentNullException`.

It returns `true` if the verification succeeds, and `false` on error.
