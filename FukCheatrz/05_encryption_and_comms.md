# 05 - Encryption and Communications

## Overview

All communication between ExLoader and its backend infrastructure at exloader.net is encrypted using a full NaCl/libsodium-style authenticated encryption stack. This means network traffic cannot be read in plaintext by a passive observer or intercepted without breaking the key exchange. The implementation is production-quality, not experimental.

## Cryptographic Primitives

The following primitive-level strings were extracted from app.so. These originate from either the Dart cryptography package or bundled NaCl bindings.

NaCl core functions:

```
crypto_box_keypair
crypto_box_beforenm
crypto_box_afternm
crypto_box_open_afternm
crypto_core_salsa20
crypto_core_hsalsa20
crypto_stream_salsa20
crypto_stream_salsa20_xor
crypto_scalarmult
crypto_scalarmult_base
crypto_secretbox
crypto_secretbox_open
```

These represent the full NaCl public-key box and secret-key box APIs. crypto_box uses Curve25519 for key exchange and XSalsa20-Poly1305 for authenticated encryption. crypto_secretbox uses XSalsa20-Poly1305 with a pre-shared key. Both provide authenticated encryption, meaning tampering with the ciphertext is detectable.

Dart-level cryptography package source paths, confirming the above are not dead library symbols:

```
package:cryptography/src/dart/chacha20.dart
package:cryptography/src/dart/poly1305.dart
```

ChaCha20-Poly1305 is a modern AEAD (Authenticated Encryption with Associated Data) cipher. It is used in TLS 1.3 and is the encryption scheme behind WireGuard. Its presence here confirms the application implements its own encrypted transport layer independently of TLS.

## Key Exchange and Session Management

Application-level strings that describe the session lifecycle:

```
ClientCryptoInit
ServerCryptoInit
PacketCryptoIsolate
```

ClientCryptoInit and ServerCryptoInit indicate a handshake is performed on connection, during which the client and server exchange public keys and derive a shared session key. PacketCryptoIsolate shows that cryptographic operations run in a dedicated Dart isolate, which is a separate execution thread. Running crypto in an isolate is a deliberate performance optimization that prevents encryption overhead from blocking the UI thread. This level of implementation care is consistent with a professionally maintained codebase.

## Integrity Verification

Additional hash function strings extracted from app.so:

```
MD5
SHA256
zipCrypto
```

MD5 and SHA256 are used for file integrity verification, likely applied to downloaded mod packages before execution. zipCrypto indicates encrypted ZIP archives are handled, consistent with mod packages being distributed in password-protected or encrypted ZIP format.

## Practical Implication for Analysis

Because the application implements its own encrypted transport layer using session keys negotiated per connection, standard network interception approaches (proxy, packet capture) will not yield readable traffic without access to the session key material. Dynamic analysis in a controlled network environment would be required to observe C2 communications, which is outside the scope of this static analysis.
