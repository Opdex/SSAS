# Stratis Open Auth Protocol

Decentralized wallet identification and authorization through message signing and signature validations.

## Benefits

- Decentralized authentication
- No third party access to sensitive user info

___

## Wallet Implementation

Wallet compatibility relies on the implementation of the following requirements:

- Scan QR code or retrieve Base64 authentication request
- Decode Base64 string authentication request into:
    ```JSON
    {
        "callbackUrl": "https://api.example.com/auth?connectionId=someConnection",
        "nonce": "some-guid"
    }
    ```
- User or wallet should validate the callback url of the authentication request
- Sign the nonce property value of the decoded request
- _**POST**_ the signed message and public key to the callbackUrl property value of the decoded string
    ```JSON
    {
        "signature": "signed-message",
        "publicKey": "public-key"
    }
    ```
___

## Backend Implementation

Backend compatibility relies on server side implementation of the following requirements:

### Using Sockets

- Connect to web socket with session or connection identifier
  - Generate nonce (GUID) and callbackUrl on socket connection
  - Store nonce, callbackUrl and timestamp
  - Create and Base64 encode an auth request shown in [Wallet Implementation](#wallet-implementation)
- Display QR code of Base64 encoded auth request
- Implement public _**POST**_ endpoint at the callbackUrl that signatures will be sent to
  - Example: `https://api.example.com/auth?connectionId=someConnection`
  - With requests shown in [Wallet Implementation](#wallet-implementation)
  - Validate signatures with public key and timestamp
  - Generate auth token, remove auth request from store
  - Pass auth token via socket to client

### Using Polling

Alternatively, polling can be used with additional backend steps:

- Client makes an auth request to the server
  - Server generates and returns an auth request with connectionId, nonce, timestamp and secret accessCode
- Wallet signs and uses the callbackUrl to return a signature
- Server validates signature, timestamp and connectionId, updating the data store with the results
  - Throws away signature after use, not stored
- Client polls API using connectionId and secret accessCode
  - When found, generate and return auth token, deleting stored auth request records and access codes

___

## Security Concerns

- Revocation of signatures once sent by the wallet is not possible
- Wallet and private keys must always be secured
- Callback URLs that signatures are sent should be validated or whitelisted by the user

___

![Auth Flows](./StratisOpenAuth.jpeg?raw=true)
