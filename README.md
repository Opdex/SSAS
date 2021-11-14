# Stratis Open Auth Protocol

Decentralized wallet identification and authentication through message signing and signature validations.

## Usage

Stratis Open Auth Protocol can be used for the following purposes:

* Personalising the data that a dApp stores about and displays to a user
* Verifying that a user has access to the private key of a wallet address

## Benefits

- Decentralized authentication
- No third party access to sensitive user info

___

## Implementation

### Stratis ID URI

A Stratis ID URI consists of three required parts, as well as optional parts:

- The scheme - `sid`
- The callback - a protocol-relative URL which the wallet will send a HTTPS request
- The UID - a unique identifier for a request, which is present within the query string of the callback
- The expiry datetime (optional) - a unix timestamp that specifies when the signature should expire, which is present within the query string of the callback

**Example:** `sid:api.example.com/auth?uid=4606287adc774829ab643816a021efbf&exp=1647216000`

### Wallet Compatibility

Wallet compatibility relies on the implementation of the following requirements:

- Ability to scan QR code or retrieve Stratis ID URI
- User or wallet _must_ validate the callback url of the authentication request
- Wallet _should_ verify that the expiry datetime has not passed, if it is present
- Sign the callback present in the Stratis ID URI
    ```
    message.Sign("api.example.com/auth?uid=4606287adc774829ab643816a021efbf&exp=1647216000")
    ```
- _**POST**_ the JSON-encoded signed message and and public key to the callback URL, using HTTPS
    ```
    POST https://api.example.com/auth?uid=4606287adc774829ab643816a021efbf&exp=1647216000
    Content-Type: application/json
    {
        "signature": "signed-message",
        "publicKey": "public-key"
    }
    ```
    
### dApp Compatibility

dApp compatibility relies on the implementation of the following requirements:

- Generate a QR code containing a Stratis ID URI and display this to the user
- Host a HTTPS endpoint that is used in the Stratis ID URI callback
  - The endpoint _must_ validate the structure and contents of the request body
  - The endpoint _must_ verify the signature and its contents
  - The endpoint _must_ verify the expiry datetime has not passed, if it present

___

## Security Concerns

- Revocation of signatures once sent by the wallet is not possible
- Wallet and private keys must always be secured
- Callback URLs that signatures are sent should be validated or whitelisted by the user
