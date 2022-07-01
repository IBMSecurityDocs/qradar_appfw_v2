# Secure data storage and encryption

Securely storing app data is very important when developing an app. Use the following methods to safely store data in a QRadar
app.

## QPyLib encdec

Encdec is an encryption and decryption module available in the QPyLib Python
library. You can use the encdec module to handle storing secrets
and sensitive data within a QRadar app.

### Encrypting values

You can use encdec to encrypt values and store them in a file identified by
the provided user value. This file stores the encrypted values, allowing them
to be retrieved later, referenced by a name.

Encdec stores the file based on the user property, because an app
can contain different secrets for different users who use the app.

Encryption setup example:

```python
enc = Encryption({'name': 'mytoken', 'user': 'myuser'})
```

This example sets up an instance of the Encryption class that is dedicated to handling
a single secret. In this example, the secret `mytoken` for user `myuser`.

The value is then encrypted and saved to the encryption file referenced by the
name:

```python
value = "value to be encrypted"
encrypted = enc.encrypt(_value_)
```

The encrypt function then returns the encrypted value, while also saving to the encryption file.

### Decrypting values

Encdec can also decrypt previously encrypted values, retrieving from the file
referenced by the user value that the secret was previously saved to.

Decryption setup example:

```python
enc = Encryption({'name': 'mytoken', 'user': 'myuser'})
```

The value is then retrieved and decrypted, if it exists:

```python
decrypted = enc.decrypt()
```

The decrypt function returns the decrypted value referenced by the name
property. If no name is found with the encrypted value, or there is an
issue with the encryption configuration, an `EncryptionError` is initiated.

### Encryption engines

The QPyLib encdec functionality supports decrypting secrets from older
encryption engine versions, and encrypting secrets at the latest engine
version.

There are currently four encdec encryption engines:

- `v1` - Unsupported - Previously distributed as a separate module from qpylib.
- `v2` - AES/CFB encryption.
- `v3` - Modified version of `v2` engine AES/CFB encryption.
- `v4` - Fernet encryption.

If an app has secrets encrypted using versions 2, 3, or 4, the encdec module
supports decryption. Encdec automatically determines the encryption engine to use. Once a secret that was
originally encrypted using an older engine version is decrypted, encdec automatically re-encrypts the secret and overrides the old secret. This allows
the encdec module to be backward compatible. When new
engine versions are released, old secrets are automatically migrated to newer
encryption engine versions.

The encdec module automatically stores secrets with the latest
designated encryption engine. Coupled with the backward compatibility,
decryption and re-encryption of old versions provide smooth
transitions to the recommended encryption engine version; only requiring QPyLib
to be updated.
