# Diffie-Hellman function

Sodium provides Curve25519, a state-of-the-art Diffie-Hellman function suitable for a wide variety of applications.

## Example (compute the public key from the given secret key)

```csharp
var bobSecretKey = new byte[] {
	0x5d,0xab,0x08,0x7e,0x62,0x4a,0x8a,0x4b,
	0x79,0xe1,0x7f,0x8b,0x83,0x80,0x0e,0xe6,
	0x6f,0x3b,0xb1,0x29,0x26,0x18,0xb6,0xfd,
	0x1c,0x2f,0x8b,0x27,0xff,0x88,0xe0,0xeb
};

//get the public key
var bobPublicKey = ScalarMult.Base(bobSecretKey);
```

## Example (compute a secret shared by the two keys)

```csharp
var aliceSecretKey = new byte[] {
	0x77,0x07,0x6d,0x0a,0x73,0x18,0xa5,0x7d,
	0x3c,0x16,0xc1,0x72,0x51,0xb2,0x66,0x45,
	0xdf,0x4c,0x2f,0x87,0xeb,0xc0,0x99,0x2a,
	0xb1,0x77,0xfb,0xa5,0x1d,0xb9,0x2c,0x2a
};

var bobPublicKey = new byte[] {
	0xde,0x9e,0xdb,0x7d,0x7b,0x7d,0xc1,0xb4,
	0xd3,0x5b,0x61,0xc2,0xec,0xe4,0x35,0x37,
	0x3f,0x83,0x43,0xc8,0x5b,0x78,0x67,0x4d,
	0xad,0xfc,0x7e,0x14,0x6f,0x88,0x2b,0x4f
};

//get a secret shared
var secretShared = ScalarMult.Mult(aliceSecretKey, bobPublicKey);
```

## Extended example Bob and Alice

```csharp
// Generate Alice's and Bob's key pairs
var bob = PublicKeyBox.GenerateKeyPair();
var alice = PublicKeyBox.GenerateKeyPair();

// Now alice and bob exchange public keys
// To keep this example simple the network, and public key exchanges are
// simulated by using the variables alicePublicKey, and bobPublicKey
alicePublicKey = alice.PublicKey;
bobPublicKey = bob.PublicKey;

// Once Alice gets Bob's public key she can calculate the
// Diffie-Helman secret with her secret key and Bob's public key
var aliceSecret = ScalarMult.Mult(alice.PrivateKey, bobPublicKey);

// Bob also calculates the Diffie-Helman secret
var bobSecret = ScalarMult.Mult(bob.PrivateKey, alicePublicKey);

// Alice and Bob should now have the same secret and the key exchange is complete

// The Diffie-Helman secret should not be used directly as an encryption key
// You can take the secret and hash it using your "favorite" hash function.

```

## Usage

### Compute public key

```csharp
public static byte[] Base(byte[] secretKey)
```

*This is the .NET equivalent of `crypto_scalarmult_base`.*

**Namespace:** `Sodium.ScalarMult`

The `Base()` function computes the user's public key, from the given `secretKey`.

The `secretKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The function returns a `32` (public key) byte array.

### Compute secret shared

```csharp
public static byte[] Mult(byte[] secretKey, byte[] publicKey)
```

*This is the .NET equivalent of `crypto_scalarmult`.*

**Namespace:** `Sodium.ScalarMult`

The `Mult()` function computes a secret shared by a user's `secretKey` and another user's `publicKey`.

The `secretKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The `publicKey` must be `32` bytes, otherwise the function throws a `KeyOutOfRangeException`.

The function returns a `32` (secret shared) byte array.

This secret can then be used to authenticate and encrypt messages between the two users.

## Algorithm details

- Curve25519
