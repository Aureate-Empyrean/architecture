# Janus Security Design

**Status: not started. This document is a placeholder and status boundary. It contains no cryptographic design.**

[Janus](janus.md) requires a dedicated security/cryptographic design and independent review before any implementation is considered production-safe. Nothing in the architecture repository selects, or should be read as selecting, an algorithm, parameter, key hierarchy, file format or protocol. The properties Janus must satisfy are recorded in [janus](janus.md#security-and-custody-model); this document is where the design that satisfies them will be recorded once it exists and has been reviewed.

## Rules for this document

- A choice is recorded here only after it has been designed and reviewed, together with its rationale and the threat model it addresses.
- Established cryptographic primitives and reviewed libraries are used; no home-grown cryptography or custom random-number generator.
- Nothing here claims "zero knowledge", "military grade", "unbreakable" or similar. Terminology is precise and justified by the reviewed design.
- Until the design is reviewed, Janus implementations are not to be presented as production-safe for real secrets.

## Areas that need design (all Open)

- Threat model
- Cryptographic primitives; KDF and parameters
- Key hierarchy; master-password handling
- Vault encryption format; per-item vs. vault-level encryption
- Attachment encryption
- Metadata confidentiality
- Secure local storage; decrypted-memory lifecycle
- Clipboard handling/clearing
- TOTP secret handling
- Passkey architecture
- Biometric/system-credential unlock
- Recovery-key design; key-loss behavior
- Trusted-device protocol; device enrollment/revocation
- Nexus ciphertext sync protocol; conflict resolution
- Backup/restore cryptographic interaction
- Secure deletion limitations on modern storage
- Encrypted transfer format
- Import handling of plaintext source exports
- Browser-extension threat model
- Android autofill threat model
- Breach-check privacy protocol
- Security audit/review process (who reviews, when, and what gates production use)
