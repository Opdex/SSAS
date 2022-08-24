# Stratis Signature Auth Specification

Decentralized wallet identification and authentication through message signing and signature validations.

## Usage

Stratis Signature Auth Specification can be used for the following purposes:

* Personalising the data that a dApp stores about and displays to a user
* Verifying that a user has access to the private key of a wallet address

## Benefits

- Decentralized authentication
- No third party access to sensitive user info

## Packages

- [SSAS C#](https://github.com/Opdex/SSAS.NET)

___

## Implementation

### Stratis ID

A Stratis ID consists of the following parts:

- The callback - a protocol-relative URL to which the wallet will send a HTTPS request
- The UID - a unique identifier for a request, which is present within the query string of the callback
- The expiry datetime (optional) - a unix timestamp that specifies when the signature should expire, which is present within the query string of the callback

**Example:** `api.example.com/auth?uid=4606287adc774829ab643816a021efbf&exp=1647216000`

#### Handling the Stratis ID URI

You can handle the Stratis ID in two ways:

1. As a URI, using the scheme _sid:_
    ```
    sid:api.example.com/auth?uid=4606287adc774829ab643816a021efbf&exp=1647216000
    ```
2. With a protocol handler URI, using the scheme _web+sid:_
    ```
    web+sid:api.example.com/auth?uid=4606287adc774829ab643816a021efbf&exp=1647216000
    ```

### Wallet Compatibility

Wallet compatibility relies on the implementation of the following requirements:

- Ability to scan QR code or input Stratis ID URI
- The wallet is registered to be able to handle the _web+sid:_ protocol
- User or wallet _must_ validate the callback url of the authentication request
- Wallet _should_ verify that the expiry datetime has not passed, if it is present
- Sign the full Stratis ID callback
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

- Generate and display a QR code containing a Stratis ID URI to the user
- Include a button link for the protocol handler URL
- Host a HTTPS endpoint that is used in the Stratis ID URI callback
  - The endpoint _must_ validate the structure and contents of the request body
  - The endpoint _must_ verify the signature and its contents
  - The endpoint _must_ verify the expiry datetime has not passed, if it present

___

## Security Concerns

- Revocation of signatures once sent by the wallet is not possible
- Wallet and private keys must always be secured
- Callback URLs that signatures are sent should be validated or whitelisted by the user
