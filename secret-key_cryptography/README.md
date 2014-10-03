# Secret-key cryptography

`libsodium-net` secret-key cryptography areas:


## Secret-key authenticated encryption

- Encrypts a message with a key and a nonce to keep it confidential
- Computes an authentication tag. This tag is used to make sure that the message hasn't been tampered with before decrypting it.

[Secret-key authenticated encryption](secret-key_cryptography/authenticated_encryption.md)

## Secret-key authentication

Authenticate or verifie a message with an authentication tag, this does not encrypt the message.

[Secret-key authentication](secret-key_cryptography/secret-key_authentication.md)

## Authenticated Encryption with Additional Data

- Encrypts a message with a key and a nonce to keep it confidential
- Computes an authentication tag. This tag is used to make sure that the message, as well as optional, non-confidential (non-encrypted) data, haven't been tampered with.

A typical use case for additional data is to store protocol-specific metadata about the message, such as its length and encoding.

[Authenticated Encryption with Additional Data](secret-key_cryptography/aead.md)
