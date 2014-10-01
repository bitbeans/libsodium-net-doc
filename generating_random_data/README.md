# Generating random data

The library uses the `libsodium` functions to generate random data, thus:

- On Windows systems, the `RtlGenRandom()` function is used
- On OpenBSD and Bitrig, the `arc4random()` function is used
- On other Unices, the `/dev/urandom` device is used

## Usage

```csharp
public static byte[] GetRandomBytes(int count)
```
*This is the .NET equivalent of `randombytes_buf`.*

**Namespace:** `Sodium.SodiumCore`

The `GetRandomBytes()` method takes a integer `count`, and produces an unpredictable sequence of bytes.

This is suitable for use as a key, salt or nonce.

**Note:** In classes where appropriate, there are `GenerateKey()` and/or `GenerateNonce()` and/or `GenerateSalt()` functions that return a byte array of the correct size.
