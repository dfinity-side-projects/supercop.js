# supercop.js (DFINITY variant)
[orlp/ed25519](https://github.com/orlp/ed25519) compiled to pure javascript using Emscripten

## modifications
The [public key generation](https://tools.ietf.org/html/rfc8032#section-5.1.5) has been changed as follows:

The private key is not hashed (skips step 1). The private key is directly pruned and used as a scalar (steps 2-4). The same modification applies in signatures.

# example
## signing and verifying stuff
``` javascript
var lib = require('supercop.js')

var seed = lib.createSeed()
var keys = lib.createKeyPair(seed)
var msg = new Buffer('hello there')
var sig = lib.sign(msg, keys.publicKey, keys.secretKey)
console.log(lib.verify(sig, msg, keys.publicKey)) // true
```

## storing keypairs
``` javascript
var lib = require('supercop.js')
var fs = require('fs')

var seed = lib.createSeed()
var keys = lib.createKeyPair(seed)

fs.writeFileSync('keys.json', JSON.stringify({
  publicKey: keys.publicKey.toString('base64'),
  secretKey: keys.secretKey.toString('base64')
}))
```

## loading keypairs
``` javascript
var fs = require('fs')

var keys = require('./keys.json')
keys = {
  publicKey: new Buffer(keys.publicKey, 'base64'),
  secretKey: new Buffer(keys.secretKey, 'base64')
}
```

# api
## var seed = lib.createSeed()
Generates a cryptographically-secure 32-byte seed.

## var keys = lib.createKeyPair(seed)
Generates a pubkey from the provided 32-byte secret key with the following properties:
* `keys.publicKey` - A 32 byte public key as a buffer.
* `keys.secretKey` - The secret key

## var sig = lib.sign(msg, publicKey, secretKey)
Signs a given message of any length.
* `msg` - A buffer of any length containing a message.
* `publicKey` - The public key to sign with as a buffer.
* `secretKey` - The private key to sign with as a buffer.
* `sig` - The resulting signature as a buffer of length 64 bytes.

## var valid = lib.verify(sig, msg, publicKey)
Verifies a given signature goes with the message and key.
* `sig` - The signature to verify.
* `msg` - The message that the signature represents.
* `publicKey` - The public key used to generate the signature.
* `valid` - A boolean telling whether the signature is valid(`true`) or invalid(`false`).
