# Fluree Cryptography 

A collection of Javascript cryptography functions for Fluree

```
npm install @fluree/crypto-utils
```

>This library has a dependency on the @fluree/crypto-base library, which can be downloaded via `npm install @fluree/crypto-base`

## API

### Generate Keys

Returns a hex string of a public and private key pair. 

```javascript
import { generateKeyPair } from '@fluree/crypto-utils';

const { publicKey, privateKey }  = generateKeyPair();

```

### Get Auth ID from Public Key

Returns the `_auth/id` that accompanies a given public key. 

```javascript
import { generateKeyPair, getSinFromPublicKey } from '@fluree/crypto-utils';

const { publicKey }  = generateKeyPair();
const authId = getSinFromPublicKey(publicKey);

```

### Sign Transaction

signTransaction returns an object with the keys: sig, cmd, which should then be sent in the body of a request to the `/command` endpoint. 

```javascript
import { generateKeyPair, getSinFromPublicKey, signTransaction } from '@fluree/crypto-utils';

const { publicKey, privateKey }  = generateKeyPair();
const authId = getSinFromPublicKey(publicKey);

const db = "test/one";
const expire = Date.now() + 1000;
const fuel = 100000;
const nonce = 1; 
// Deps is an optional parameter - it is a array of _tx/ids that must have succeeded
// for the current transaction to be accepted.
const deps = null; 

const tx = JSON.stringifiy([{
    "_id": "_tag",
    "id": "tag/test" }])

const command = signTransaction(authId, db, expire, fuel, nonce, privateKey, tx, deps)

const fetchOpts = {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(body)
  };

const fullURI = `https://localhost:8090/fdb/${db}/command`;

fetch(fullURI, fetchOpts)
```

### Sign Query

signQuery returns an object with the keys: header, method, body, which should then be sent to any of the query endpoints (`/query`, `/multi-query`, `history`, `block`).

```javascript
import { generateLeyPair, getSinFromPublicKey, signQuery } from '@fluree/crypto-utils';

const { publicKey, privateKey }  = generateKeyPair();
const authId = getSinFromPublicKey(publicKey);

const param = JSON.stringify({select: ["*"], from: "_collection"});
const db = "test/one";
const host = "localhost";
const queryType = "query";


const fetchOpts = signQuery(privateKey, param, queryType, host, db)

const fullURI = `https://localhost:8090/fdb/${db}/query`;

fetch(fullURI, fetchOpts)
```


### Sign Request

signRequest returns an object containing the keys: header, method and body. This object can be used to sign requests that are not related to transactions or queries.

```javascript

import { generateLeyPair, getSinFromPublicKey, signRequest } from '@fluree/crypto-utils';

const { publicKey, privateKey }  = generateKeyPair();
const authId = getSinFromPublicKey(publicKey);

var endpoint = 'http://localhost:8090/fdb/delete-db';

var body = JSON.stringify({
  "db/id": 'test/deleteme',
  "auth": authId
});

var fetchOpts = signRequest("POST", endpoint, body, privateKey, authId);

fetch(endpoint, fetchOpts)
```

