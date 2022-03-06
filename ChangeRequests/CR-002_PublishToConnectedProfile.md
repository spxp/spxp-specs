# CR-002 - Publish To Connected Profile
Draft - Target SPXP 0.4

Version 0.3 of the SPXP spec already incorporates the possibility for profiles to publish posts on connected peer
profiles. For this, it defines [authorized signing keys (8.2)](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#82-authorized-signing-keys) and how
these can be exchanged in the [connection package(14.3)](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#143-connection-package) as part of the
connection process. Posts can not only be signed by the profile owner, but by any authorized key. These posts can then
impersonate the profile owner or be in their own name by containing a profile reference to the original author.

What is missing yet is a process how profile clients can send these posts to the profile server of a connected peer.
The server then needs to be able to authenticate and process this contribution. In the case of a private post, this
needs to be done without revealing information about the post or its author to the profile server. This requirement is
challenging as we need to protect the profile server against session replay attacks and different kinds of DoS attacks
as well.

## Proposed changes

### In section "5 Social profile root document"
Add the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `publishEndpoint` | String | optional | _URI-reference_ as defined in [RFC 3986 Section 4.1](https://tools.ietf.org/html/rfc3986#section-4.1) pointing to the “publish endpoint” as specified in [chapter 15](#15-publishing) |

### In section "14.3 Connection Package"
Replace the member `publishingCertificate` with the following:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `publishing` | Object | optional | Object containing additional information for the publishing process as specified in [chapter 15](#15-publishing) if the peer is authorised to publish on this profile |

Example:
```json
{
    "type" : "connection_package",
    "ver" : "0.3",
    "establishId" : "K4dwfD4wA67xaD-t",
    "readerKey" : {
        "kid" : "ABCD.1234",
        "kty" : "oct",
        "alg" : "A256GCM",
        "k" : "Dl3fyz_0lHaSeFl-TJxSTr2NET5H6t2SELmI5tiCFno"
    },
    "signature" : {
        "sig" : XXXXX,
        "key" : "C8xSIBPKRTcXxFix"
    }
}
```

### Insert new section before "Profile Relocation"

#### 15 Publishing
The information exposed through SPXP can originate from any backend, like a content management system. If a profile
accepts contributions from peer profiles, or any other authorized source, it declares a `publishEndpoint` in the profile
root document.  
The authorized source needs to be in possession of a valid [authorized signing key (8.2)](../SPXP-Spec.md#82-authorized-signing-keys)
to sign its contribution. In the case of private contributions, the source additionally needs to possess a _publishing
key_ and needs to know the encryption group to be used. All of these are contained in the [publishing object
(15.1)](#151-publishing-object-in-connection-package) of the [connection package](../SPXP-Spec.md#143-connection-package)
exchanged during the connection process.

##### 15.1 Publishing object in connection package
If a profile grants its peer publishing permissions during a connection process, it provides the required information
as part of the `publishing` object in the [connection package](../SPXP-Spec.md#143-connection-package) with the following
members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `certificate` | Object | required | JSON object describing the certificate containing the [authorized signing key](#82-authorized-signing-keys) |
| `postPublic` | Boolean | required | Flag indicating if the peer is authorised to make public unencrypted posts on this profile |
| `postPrivate` | Object | optional | JSON object containing additional information for the private publishing process |

The optional `postPrivate` JSON objects has the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `publishKey` | Object | required | JSON object describing the private and public key of the signing key pair used in the private publishing process as JWK defined in [RFC 7517 “JSON Web Key (JWK)”](https://tools.ietf.org/html/rfc7517) using the Ed25519 curve specifier as defined in [RFC 8037 Section 3.1](https://tools.ietf.org/html/rfc8037#section-3.1) |
| `groups` | Object | required | JSON object containing a mapping between end user display names and group ids of publish groups which can be used for encrypted private posts |

Example within a connection package:
```json
{
    "type" : "connection_package",
    "ver" : "0.3",
    "establishId" : "K4dwfD4wA67xaD-t",
    "readerKey" : {
        "kid" : "ABCD.1234",
        "kty" : "oct",
        "alg" : "A256GCM",
        "k" : "Dl3fyz_0lHaSeFl-TJxSTr2NET5H6t2SELmI5tiCFno"
    },
    "publishing" : {
        "postPublic" : true,
        "postPrivate" : {
            "publishKey" : {
                "kid" : "xxx",
                "kty" : "OKP",
                "crv" : "Ed25519",
                "x" : "xxx",
                "d" : "xxx"
            },
            "groups" : {
                "Friends" : "grp-friends",
                "Family" : "grp-family"
            }
        },
      "certificate" : {
            "publicKey" : {
                "kid" : "czlHMPEJcLb7jMUI",
                "kty" : "OKP",
                "crv" : "Ed25519",
                "x" : "vg42ogNHigJnwZ0pwwMzUtaXZA49eqcfGYl2u9GR8vg"
            },
            "grant" : [ "post" ],
            "signature" : {
                "key" : "C8xSIBPKRTcXxFix",
                "sig" : "PEekh7oCLQa0O4rCUPrH19yCJCLtEZfnumUlPrH0TPbq66Bj_aO71enf-P6gUttlgJFRRfvD1D7wAAYZaX6PCQ"
            }
        }
    },
    "signature" : {
        "sig" : XXXXX,
        "key" : "C8xSIBPKRTcXxFix"
    }
}
```

##### 15.2 Publish endpoint
To publish a piece of information, like a post, the protocol client sends an HTTP POST request to the “publish endpoint“
with a JSON object as HTTP body. This object is composed of the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `type` | String | required | Type of request. Use `post` to publish the payload as peer post. |
| `ver` | String | required | Most recent version of the SPXProtocol supported by the client |
| `payload` | Object | required | The object to be published |
| `token` | String | required | One time authentication token as specified in [chapter 15.3](#153-authentication-tokens) |

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

##### 15.3 Authentication tokens
To prepare the publishing process, the protocol client first needs to obtain a one time authentication token. To obtain
an `authToken` for the publishing process, the protocol client sends an HTTP POST request to the “publish  endpoint“ 
with a JSON object as HTTP body. This object is composed of the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `type` | String | required | Type of request. Use `prepare_post` to obtain a one time authentication token. |
| `ver` | String | required | Most recent version of the SPXProtocol supported by the client |
| `timestamp` | Timestamp | required | Current time |
| `group` | String | optional | Publish group id the client wants to use |

This JSON object must be signed as defined in [chapter 8.1](#81-signing-json-objects) by either the profile key if the
protocol client wants to make a public post or by the "publish key" if the client wants to make a private post.

The server responds with one of these status codes.

| Status code | Meaning |
|---|---|
| `200` | A one time authentication token has been issued |
| `403` | The signature is not present, invalid or the signing key is not authorised to post |
| `429` | The client IP address has sent too many requests to the profile server in a given time window |

If the request is accepted, the protocol server responds with a `200` status code and a JSON object with these members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `token` | String | required | The issued one time authentication token |
| `groupRound` | String | optional | If `group` has been present in the request, the most recent round id of this group |

##### 15.4 Attack mitigations

###### 15.4.1 Preventing Session Replay attacks against token acquisition
It is possible that a malicious actor captures a `prepare_post` request and simply replays this request to the server to
get a fresh authentication token. To prevent this, the server needs to record the last used `timestamp` per signing key
and only accept requests with a more recent timestamp.  
This is also limiting DoS attacks as the client needs to sign each new request before being able to send it.

###### 15.4.2 Preventing Session Replay attacks on the publishing endpoint
Since the server does not know anything about the content of the encrypted payload, the publishing endpoint is
vulnerable to session replay attacks. An actor with publishing permissions could simply take any existing post, encrypt
it again, acquire a fresh authentication token, and then send it to the publishing endpoint. The server will always
accept  it as long as the authentication token is valid.  
By enforcing that the authentication token must be embedded as AAD in the JWE object, we can bind the encrypted object
to this individual publishing request in a way that can be checked by the server without exposing details about the
encrypted data. The individual clients then need to validate the AAD on the JWE against the signature in the decrypted
plaintext.

