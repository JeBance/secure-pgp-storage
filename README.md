# secure-pgp-storage
==========
[secure-pgp-storage](https://jebance.github.io/secure-pgp-storage/) is a class on top of [OpenPGP.js](https://openpgpjs.org/)

Read and run test.js

OpenPGP.js [![BrowserStack Status](https://automate.browserstack.com/badge.svg?badge_key=N1l2eHFOanVBMU9wYWxJM3ZnWERnc1lidkt5UkRqa3BralV3SWVhOGpGTT0tLVljSjE4Z3dzVmdiQjl6RWgxb2c3T2c9PQ==--5864052cd523f751b6b907d547ac9c4c5f88c8a3)](https://automate.browserstack.com/public-build/N1l2eHFOanVBMU9wYWxJM3ZnWERnc1lidkt5UkRqa3BralV3SWVhOGpGTT0tLVljSjE4Z3dzVmdiQjl6RWgxb2c3T2c9PQ==--5864052cd523f751b6b907d547ac9c4c5f88c8a3) [![Join the chat on Gitter](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/openpgpjs/openpgpjs?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[OpenPGP.js](https://openpgpjs.org/) is a JavaScript implementation of the OpenPGP protocol. It implements [RFC4880](https://tools.ietf.org/html/rfc4880) and parts of [RFC4880bis](https://tools.ietf.org/html/draft-ietf-openpgp-rfc4880bis-10).

**Table of Contents**

- [secure-pgp-storage](#secure-pgp-storage)
    - [Getting started](#getting-started)
        - [Node.js](#nodejs)
    - [Examples](#examples)
        - [Generate new ECC key pair](#generate-new-ecc-key-pair)
        - [Checking class variables for data](#checking-class-variables-for-data)
        - [Encrypt *String* data with PGP keys](#encrypt-string-data-with-pgp-keys)
        - [Decrypt *String* data with PGP keys](#decrypt-string-data-with-pgp-keys)
        - [Symmetric encryption of *String* data with compression](#symmetric-encryption-of-string-data-with-compression)
        - [Symmetric decryption of *String* data with compression](#symmetric-decryption-of-string-data-with-compression)
        - [Storage encryption](#storage-encryption)
        - [Clearing data from storage](#clearing-data-from-storage)
        - [Storage decryption](#storage-decryption)
    - [License](#license)

### secure-pgp-storage

* The secure-pgp-storage class is designed to minimize code when working with the OpenPGP.js library.

* The `index.js` bundle works well in Node.js. It is used by default when you `require('secure-pgp-storage')` in Node.js.


### Getting started

#### Node.js

Install secure-pgp-storage using npm:

```sh
npm install secure-pgp-storage
```

And import it as a CommonJS module:

```js
const sPGPs = require('secure-pgp-storage');
```


### Examples

Here are some examples of how to use secure-pgp-storage. Please review the test.js file to understand how the secure-pgp-storage class works.

#### Generate new ECC key pair

The `createStorage` function creates a new ECC key pair and stores them in class variables.

```js
(async () => {
	await sPGPs.createStorage('John Smith', 'john.smith@gmail.com', '1q2w3e4r5t6y7u8i9o0p');
	console.log('Nickname:', sPGPs.nickname);
	console.log('E-mail:', sPGPs.email);
	console.log('Fingerprint:', sPGPs.fingerprint);
	console.log('Public key:');
	console.log(sPGPs.publicKeyArmored);
})();
```

#### Checking class variables for data

The `createStorage` function creates a new ECC key pair and stores them in class variables.

```js
(async () => {
	console.log('All data is activated:', await sPGPs.checkAllData());
})();
```

#### Encrypt *String* data with PGP keys

Encryption will use the algorithm preferred by the public (encryption) key (defaults to aes256 for keys generated in OpenPGP.js).

```js
(async () => {
	const recipientPublicKeyArmored = sPGPs.publicKeyArmored;	// For example, we will use our public key.
	let encrypted = await sPGPs.encryptMessage(recipientPublicKeyArmored, 'Hello world!');
	console.log('Encrypted message:');
	console.log(encrypted);
	console.log('Check message:', await sPGPs.checkMessage(encrypted));
})();
```

#### Decrypt *String* data with PGP keys

Decryption will use the algorithm used for encryption.

```js
(async () => {
	const senderPublicKeyArmored = sPGPs.publicKeyArmored;
	let decrypted = await sPGPs.decryptMessage(senderPublicKeyArmored, encrypted);
	console.log('Decrypted message:');
	console.log(decrypted);
})();
```

#### Symmetric encryption of *String* data with compression

By default, `encryptMessageSymmetricallyWithCompression` will use `openpgp.enums.compression.zlib` symmetric encryption compression.

```js
(async () => {
	encrypted = await sPGPs.encryptMessageSymmetricallyWithCompression('Hello again!', '1234567890');
	console.log('Encrypted message:');
	console.log(encrypted);
	console.log('Check message:', await sPGPs.checkMessage(encrypted));
})();
```

#### Symmetric decryption of *String* data with compression

```js
(async () => {
	decrypted = await sPGPs.decryptMessageSymmetricallyWithCompression(encrypted, '1234567890');
	console.log('Decrypted message:');
	console.log(decrypted);
})();
```

#### Storage encryption

The `encryptStorage` function puts `publicKeyArmored` and `privateKeyArmored` into JSON and encrypts them with symmetric encryption using the password that was used to create the key pair.

```js
(async () => {
	const encryptedStorage = await sPGPs.encryptStorage();
	console.log('Encrypted storage:');
	console.log(encryptedStorage);
	console.log('Check message:', await sPGPs.checkMessage(encryptedStorage));
	console.log('encodeURIComponent (for file href html):', await sPGPs.generateSecureFile());	
})();
```

#### Clearing data from storage

The `eraseAllData` function clears class variables.

```js
(async () => {
	await sPGPs.eraseAllData();
	console.log('Nickname:', sPGPs.nickname);
	console.log('E-mail:', sPGPs.email);
	console.log('Fingerprint:', sPGPs.fingerprint);
	console.log('Public key:');
	console.log(sPGPs.publicKeyArmored);
	console.log('All data is activated:', await sPGPs.checkAllData());
})();
```

#### Storage decryption

The `decryptStorage` function decrypts a message with a key pair inside. After parsing the JSON and reading the keys, the class variables are filled.

```js
(async () => {
	const decryptedStorage = await sPGPs.decryptStorage(encryptedStorage, '1q2w3e4r5t6y7u8i9o0p');
	console.log('Decrypted storage:', decryptedStorage);
	console.log('Nickname:', sPGPs.nickname);
	console.log('E-mail:', sPGPs.email);
	console.log('Fingerprint:', sPGPs.fingerprint);
	console.log('Public key:');
	console.log(sPGPs.publicKeyArmored);
	console.log('All data is activated:', await sPGPs.checkAllData());
})();
```

### License

[GNU Lesser General Public License](https://www.gnu.org/licenses/lgpl-3.0.en.html) (3.0 or any later version). Please take a look at the [LICENSE](LICENSE) file for more information.
