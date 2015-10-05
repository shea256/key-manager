# Keychain Manager

[![CircleCI](https://img.shields.io/circleci/project/blockstack/keychain-manager-js.svg)](https://circleci.com/gh/blockstack/keychain-manager-js)
[![npm](https://img.shields.io/npm/v/keychain-manager.svg)](https://www.npmjs.com/package/keychain-manager)
[![npm](https://img.shields.io/npm/dm/keychain-manager.svg)](https://www.npmjs.com/package/keychain-manager)
[![npm](https://img.shields.io/npm/l/keychain-manager.svg)](https://www.npmjs.com/package/keychain-manager)
[![Slack](http://slack.blockstack.org/badge.svg)](http://slack.blockstack.org/)

A key system based around accounts that each have hierarchical deterministic (HD) keychains with ECDSA keypairs (the ones Bitcoin uses).

### Getting started

```
$ npm install keychain-manager
```

```js
var keychainManager = require('keychain-manager'),
    KeychainGenerator = keychainManager.KeychainGenerator,
    PrivateKeychain = keychainManager.PrivateKeychain,
    PublicKeychain = keychainManager.PublicKeychain
```

### Keychain Generator

```js
var keychainGenerator = new KeychainGenerator(),
    accountNumber = 0,
    privateKeychain = keychainGenerator.getPrivateKeychain(accountNumber),
    publicKeychain = keychainGenerator.getPublicKeychain(accountNumber)
```

A keychain generator is the highest level abstraction of keys. In a sense, it represents the root or master private key for the application and/or device. Account-specific private keychains can be derived from it, as well as account-specific public keychains.

Note that knowledge of the master private key of a keychain derived from the keychain generator does not provide knowledge of the master key of the keychain generator.


### Private Keychain

```js
var keyName = 'blockstack.org',
    message = 'Hello, World!'

var chainPathHash = privateKeychain.getChainPathHash(keyName),
    privateKey = privateKeychain.getPrivateKey(chainPathHash),
    signature = privateKeychain.signWithKey(privateKey, message),
    publicKeychain = privateKeychain.getPublicKeychain()
```

A private keychain is a collection of private keys with a chain-specific master key or "ancestor" key that helps derive all of the child keys.

A private keychain can be created on it's own but it is recommended that it is derived from a keychain generator. This adds a nice capability where an entirely new keychain can be issued if the master key of a particular keychain is ever compromised.

Note that every private key in the keychain can be traced back to the ancestor key in the chain. That said, with a chain path with enough entropy, it would be intractable to brute-force the path from the child private key to the ancestor private key.

### Public Keychain

```js
var publicKey = publicKeychain.getPublicKey(chainPathHash),
    address = publicKeychain.getAddress(chainPathHash),
    signatureVerified = publicKeychain.signatureMatchesAddress(message, signature, address)
```

A public keychain is the public equivalent of a private keychain, where every public key has a corresponding private key in the corresponding keychain.

A public keychain may be derived either from the corresponding private keychain OR directly from a keychain generator.

Note that every public key in the public keychain can be traced back to the ancestor public key in the chain. But again, if the chain path is random and long enough, it would be intractable to brute-force the path from the child public key to the ancestor public key.
