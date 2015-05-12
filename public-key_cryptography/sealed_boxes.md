# Sealed Boxes

## Example

```csharp
const string MESSAGE = "Message";

//Recipient creates a long-term key pair */
var recipientKeyPair = PublicKeyBox.GenerateKeyPair();

//Anonymous sender encrypts a message using an ephemeral key pair
//and the recipient's public key
var encryptedMessage = SealedPublicKeyBox.Create(MESSAGE, recipientKeyPair.PublicKey);

//Recipient decrypts the ciphertext
var decryptedMessage = SealedPublicKeyBox.Open(encryptedMessage, recipientKeyPair);
```

## Purpose

Sealed boxes are designed to anonymously send messages to a recipient given its public key.

Only the recipient can decrypt these messages, using its private key. While the recipient can verify the integrity of the message, it cannot verify the identity of the sender.

A message is encrypted using an ephemeral key pair, whose secret part is destroyed right after the encryption process.

Without knowing the secret key used for a given message, the sender cannot decrypt its own message later. And without additional data, a message cannot be correlated with the identity of its sender.

## Usage

### Encrypt

```csharp
public static byte[] Create(byte[] message, byte[] recipientPublicKey)

//there exists some overloaded versions:
public static byte[] Create(string message, byte[] recipientPublicKey)
public static byte[] Create(byte[] message, KeyPair recipientKeyPair)
public static byte[] Create(string message, KeyPair recipientKeyPair)
```
*This is the .NET equivalent of `crypto_box_seal`*

**Namespace:** `Sodium.SealedPublicKeyBox`

The function creates a new key pair for each message, and attaches the public key to the ciphertext. The secret key is overwritten and is not accessible after this function returns.

The `Create()` function encrypts a `message` for a recipient whose public key is `recipientPublicKey`.

The `recipientPublicKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The function returns a byte array (encrypted message) on success, or throws an `CryptographicException()` on failure.

### Decrypt

```csharp
public static byte[] Open(byte[] cipherText, byte[] recipientSecretKey, byte[] recipientPublicKey)

//there exists some overloaded versions:
public static byte[] Open(string cipherText, byte[] recipientSecretKey, byte[] recipientPublicKey)
public static byte[] Open(byte[] cipherText, KeyPair recipientKeyPair)
public static byte[] Open(string cipherText, KeyPair recipientKeyPair)
```
*This is the .NET equivalent of `crypto_box_seal_open`.*

**Namespace:** `Sodium.SealedPublicKeyBox`

The `Open()` function decrypts the ciphertext `cipherText` using the key pair (`recipientSecretKey`, `recipientPublicKey`).

Key pairs are compatible with other `crypto_box_*` operations and can be created using `PublicKeyBox.GenerateKeyPair()` or `PublicKeyBox.GenerateKeyPair(byte[] privateKey)`.

This function doesn't require passing the public key of the sender, as the ciphertext already includes this information.

## Algorithm details

Sealed boxes leverage the `crypto_box` construction (Curve25519, XSalsa20-Poly1305).

The format of a sealed box is
```
ephemeral_pk || box(m, recipient_pk, ephemeral_sk, nonce=blake2b(ephemeral_pk || recipient_pk))
```

## Availability

`SealedPublicKeyBox` was introduced in libsodium-net 0.8 and thus libsodium 1.0.3.
