# Helpers

## Sodium Version

```csharp
public static string SodiumVersionString()
```
*This is the .NET equivalent of `sodium_version_string`.*

**Namespace:** `Sodium.SodiumCore`

The `SodiumVersionString()` function returns the version string on the `libsodium` library in use. Note, this is **not** the version of `libsodium-net`.

## KeyPair

```csharp
public KeyPair(byte[] publicKey, byte[] privateKey)
```
**Namespace:** `Sodium`

This class represents a `libsodium-net` key pair.

`privateKey` length must be a multiple of `16` bytes, otherwise it throws a `KeyOutOfRangeException`.

## Hexadecimal encoding/decoding

```csharp
public static string BinaryToHex(byte[] data)
```
*This is the .NET equivalent of `sodium_bin2hex`.*

**Namespace:** `Sodium.Utilities`

The `BinaryToHex()` function takes a byte array `data`, and produces a lower-case hex-encoded string.

The returned string includes a nul byte (`\0`) terminator.

The function returns a string on success, or throws an `Exception()` on failure.


```csharp
public static byte[] HexToBinary(string hex)
```
*This is the .NET equivalent of `sodium_hex2bin`.*

**Namespace:** `Sodium.Utilities`

The `HexToBinary()` function converts a hex-encoded string `hex` (lower-case, upper-case or with some extra chars like: Hyphen, Colon and Space) to a byte array.

The parser stops when a non-hexadecimal, non-ignored character is found.

The function returns a byte array on success, or throws an `Exception()` on failure.


### Non-libsodium Methods

```csharp
public static string BinaryToHex(byte[] data, HexFormat format, HexCase hcase = HexCase.Lower)
```

**Namespace:** `Sodium.Utilities`

This overload takes a byte array `data`, and produces a lower-case or upper-case hex-encoded string, it also can add some extra chars (Hyphen, Colon and Space) to generate a human readable format.

#### The possible formats are:
```csharp

public enum HexFormat
{
  None, //a hex string without any seperators.
  Colon, //a hex string with colons (dd:33:dd).
  Hyphen, //a hex string with hyphens (dd-33-dd).
  Space //a hex string with spaces (dd 33 dd).
}

public enum HexCase
{
  Lower, //lower-case hex-encoded.
  Upper //upper-case hex-encoded
}
```
**Note:** This overload doesn't use the sodium_bin2hex implementation.
