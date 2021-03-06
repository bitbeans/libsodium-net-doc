# Public-key signatures

## Example (combined mode)

```csharp
const string MESSAGE = "test";
var keyPair = PublicKeyAuth.GenerateKeyPair(); //64 byte private key | 32 byte public key

//sign the message
var signedMessage = PublicKeyAuth.Sign(MESSAGE, keyPair.PrivateKey);

//get the unsigned message again
var unsigendMessage = PublicKeyAuth.Verify(signedMessage, keyPair.PublicKey);
```

## Example (detached mode)

```csharp
const string MESSAGE = "test";
var keyPair = PublicKeyAuth.GenerateKeyPair(); //64 byte private key | 32 byte public key

//get a signature for the message
var signature = PublicKeyAuth.SignDetached(MESSAGE, keyPair.PrivateKey);

//check the signature for the message
if (PublicKeyAuth.VerifyDetached(signature, MESSAGE, keyPair.PublicKey))
{
    //message ok
}
```

## Purpose

In this system, a signer generates a key pair:
- a secret key, that will be used to append a seal to any number of messages
- a public key, that anybody can use to verify that the seal appended to a message was actually issued by the creator of the public key.

Verifiers need to already know and ultimately trust a public key before messages sealed using it can be verified.

*Warning:* this is different from authenticated encryption. Appending a seal does not change the representation of the message itself.

## Random Helpers

```csharp
public static KeyPair GenerateKeyPair()
```
**Namespace:** `Sodium.PublicKeyAuth`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a key pair based on a random seed.

```csharp
public static KeyPair GenerateKeyPair(byte[] privateKey)
```
**Namespace:** `Sodium.PublicKeyAuth`

Uses `Sodium.SodiumCore.GetRandomBytes()` to generate a key pair based on the provided private key.

The `privateKey` seed must be `32` bytes, otherwise the function throws a `SeedOutOfRangeException`.

`libsodium-net` [KeyPair](../helpers/README.md)

## Combined mode

In combined mode, the signature is stored with a copy of the original message to it.

### Sign

```csharp
public static byte[] Sign(byte[] message, byte[] key)

//there exists an overloaded version:
public static byte[] Sign(string message, byte[] key)
```
*This is the .NET equivalent of `crypto_sign`.*

**Namespace:** `Sodium.PublicKeyAuth`

The `Sign()` function prepends a seal to a `message` using the secret key `key`.

The `key` must be `64` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The function returns a byte array (signed message).

### Verify

```csharp
public static byte[] Verify(byte[] signedMessage, byte[] key)
```
*This is the .NET equivalent of `crypto_sign_open`.*

**Namespace:** `Sodium.PublicKeyAuth`

The `Verify()` function checks that the `signedMessage` has a valid seal for the public key  `key`.

The `key` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The function returns a byte array (message without signature) on success, otherwise it throws a `CryptographicException`.


## Detached mode

In detached mode, the signature is stored without attaching a copy of the original message to it.


### Sign

```csharp
public static byte[] SignDetached(byte[] message, byte[] key)

//there exists an overloaded version:
public static byte[] SignDetached(string message, byte[] key)
```
*This is the .NET equivalent of `crypto_sign_detached`.*

**Namespace:** `Sodium.PublicKeyAuth`

The `SignDetached()` function prepends a seal to a `message` using the secret key `key`.

The `key` must be `64` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The function returns a byte array (signature without message).

### Verify

```csharp
public static bool VerifyDetached(byte[] signature, byte[] message, byte[] key)
```
*This is the .NET equivalent of `crypto_sign_verify_detached`.*

**Namespace:** `Sodium.PublicKeyAuth`

The `VerifyDetached()` function checks that the `message` has a valid seal `signature` for the public key  `key`.

The `key` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `signature` must be `64` bytes, otherwise the function throws a `SignatureOutOfRangeException`.

The function returns `true` on success, otherwise `false`.

## Extracting from the secret key

The secret key actually includes the seed (either a random seed or the one given to `GenerateKeyPair(byte[] privateKey)`) as well as the public key.

While the public key can always be derived from the seed, the precomputation saves a significant amount of CPU cycles when signing.

If required, Sodium provides two functions to extract the seed and the public key from the secret key:

### Extracting the seed from the secret key

```csharp
public static byte[] ExtractEd25519SeedFromEd25519SecretKey(byte[] ed25519SecretKey)
```
*This is the .NET equivalent of `crypto_sign_ed25519_sk_to_seed`.*

**Namespace:** `Sodium.PublicKeyAuth`

### Extracting the public key from the secret key

```csharp
public static byte[] ExtractEd25519PublicKeyFromEd25519SecretKey(byte[] ed25519SecretKey)
```
*This is the .NET equivalent of `crypto_sign_ed25519_sk_to_pk`.*

**Namespace:** `Sodium.PublicKeyAuth`

## Algorithm details

- Signature: Ed25519
