# Hashing

`libsodium-net` offers a couple of hash functions for the following purposes:


## Generic hashing
#### Common use cases:
* File integrity checking
* Creating unique identifiers to index arbitrary long data

*Note: If you are looking for a faster and more secure alternative than `MD5` or `SHA1` you want this!*

[Generic hashing documentation](generic_hashing.md)

## Short-input hashing
#### Common use cases:

* Hash tables
* Probabilistic data structures such as Bloom filters
* Integrity checking in interactive protocols

[Short-input hashing documentation](short-input_hashing.md)

##Password hashing
#### Common use cases:

* Deriving a key from a password and a salt
* Protecting an on-disk secret key with a password,
* Password storage, or rather: storing what it takes to verify a password without having to store the actual password.

[Password hashing documentation](../password_hashing/README.md)
