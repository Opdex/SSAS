# Stratis Open Auth Protocol

Decentralized wallet identification and authorization through message signing and signature validations.

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

A Stratis ID URI can easily be parsed by a wallet. It consists of three required parts:

- The scheme - `sid`
- The callback - a protocol-relative URL which the wallet will send a HTTPS request
- The UID - a unique identifier for a request, which is present within the query string of the callback

**Example:** `sid:api.example.com/auth?uid=some-guid`

### Wallet Compatibility

Wallet compatibility relies on the implementation of the following requirements:

- Scan QR code or retrieve Stratis ID URI
- User or wallet should validate the callback url of the authentication request
- Sign the UID query string value of the request
- _**POST**_ the JSON-encoded signed message and and public key to the callback URI, using HTTPS
    ```
    POST https://api.example.com/auth?uid=some-guid
    Content-Type: application/json
    {
        "signature": "signed-message",
        "publicKey": "public-key"
    }
    ```

___

## Security Concerns

- Revocation of signatures once sent by the wallet is not possible
- Wallet and private keys must always be secured
- Callback URLs that signatures are sent should be validated or whitelisted by the user
