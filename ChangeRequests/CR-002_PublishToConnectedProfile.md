# CR-002 - Publish To Connected Profile
Draft - Target SPXP 0.4

Version 0.3 of the SPXP spec already incorporates the possibility for profiles to publish posts on connected peer
profiles. For this, it defines [authorized signing keys (8.2)](../SPXP-Spec.md#82-authorized-signing-keys) and how
these can be exchanged in the [connection package(14.3)](../SPXP-Spec.md#143-connection-package) as part of the
connection process. Posts can not only be signed by the profile owner, but by any authorized key, and they can contain a
profile reference to the original author.

What is missing yet is a process how profile clients can send these posts to the profile server of a connected peer.
The server then needs to be able to authenticate and process this contribution. In the case of a private post, this
needs to be done without revealing information about the post or its author to the profile server. This requirement is
challenging as we need to protect the profile server against session replay attacks and different kinds of DOS attacks
as well.

## Proposed changes

### In section "5 Social profile root document"
Add the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `publishEndpoint` | String | optional | _URI-reference_ as defined in [RFC 3986 Section 4.1](https://tools.ietf.org/html/rfc3986#section-4.1) pointing to the “publish endpoint” as specified in [chapter 15](#15-publishing) |

### Insert new section before "Profile Extensions"

#### 15 Publishing
The information exposed through SPXP can originate from any backend, like a content management system. If a profile
accepts contributions from peer profiles, or any other authorized source, it declares a `publishEndpoint` in the profile
root document.  
The authorized source needs to be in possession of a valid [authorized signing keys (8.2)](../SPXP-Spec.md#82-authorized-signing-keys)
to sign its contribution. In the case of private contributions, the source additionally needs to posses one or more
contribution keys and needs to know the encryption group to be used. All of these are exchanged as part of [connection
package](../SPXP-Spec.md#143-connection-package).

##### 15.1 Publish endpoint
To publish a piece of information, like a post, the protocol client sends an HTTP POST request to the “publish endpoint“
with a JSON object as HTTP body. This object is composed of the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `type` | String | required | Type of information to be published. In this version of the protocol, only `post` is supported. |
| `ver` | String | required | Most recent version of the SPXProtocol supported by the client |
| `payload` | Object | required | The object to be published |
| `token` | String | required | One time authentication token as specified in [chapter 15.2](#152-authentication-tokens) |

The server responds with one of these status codes.

| Status code | Meaning |
|---|---|
| `204` | The information has been accepted and stored |
| `403` | The authentication token is not present or invalid |
| `429` | The client IP address has sent too many requests to the profile server in a given time window |

Example:
```json
{
    "type" : "post",
    "ver" : "0.3",
    "payload" : {
        "createts" : "2018-09-16T12:23:18.751",
        "type" : "text",
        "message" : "Hello, world!",
        "signature" : {
            "key" : "C8xSIBPKRTcXxFix",
            "aad" : "a0b1c2d3e4f5g6h7i8j9",
            "sig" : XXXX
        }
    },
    "authToken" : "a0b1c2d3e4f5g6h7i8j9"
}
```

To prevent session replay attacks, the token needs to be embedded in the actual signature as additional authenticated
data. On unencrypted payloads, the server needs to verify that the `authToken` value is part of the signature and that
this signature is valid. On encrypted payloads, the server needs to verify that the `authToken` is
set as `aad` on the JWE object. Clients with the appropriate reader key then need to verify that the authentication
tag on the JWE object is valid and that this `aad` is included in the signature within the encrypted object.

##### 15.2 Authentication tokens
When a public, unencrypted payload is sent to the publishing endpoint, the server obviously knows about the content and
the author. Just like anybody else. But when an encrypted payload is sent to the server, the server should not be able to
learn anything about the plaintext content, its author or any other metadata.  
~This makes authenticating requests to the publishing endpoint and preventing session replay attacks challenging.~

###### 15.2.1 Acquiring an Authentication Token
**TO BE DEFINED - Work in progress**

###### 15.2.2 Preventing Session Replay
Since the server does not know anything about the content of the encrypted payload, the publishing endpoint is
vulnerable to session replay attacks. An actor with publishing permissions could simply take any existing post, encrypt
it again, acquire a fresh authentication token, and then send it to the publishing endpoint. The server will always accept
it as long as the authentication token is valid.  
By enforcing that the authentication token must be embedded as AAD in the JWE object, we can bind the encrypted object
to this individual publishing request in a way that can be checked by the server without exposing details about the
encrypted data. The individual clients then need to validate the AAD on the JWE against the signature in the decrypted
plaintext.

