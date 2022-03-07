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
        "sig": "MYD_wt5ARYtLCpjDbCxFMmjhO5V4VOEbgs10snKifa13cwZgsNBwuCjI4b2AvGznAQKft2EoiQf-TD3kNOm8Ag",
        "key" : "C8xSIBPKRTcXxFix"
    }
}
```

### In section "14.5 Connect Message"

Change description of `publish` offering value as follows:

| Offering | Providing |
|---|---|
| `publish` | When accepting this request, the requestee is given additional information to publish posts on this profile (only in its own name) |

### In section "14.8 Connection package exchange"

Replace Example response:
```json
{
    "type" : "connection_finish",
    "ver" : "0.3",
    "establishId" : "K4dwfD4wA67xaD-t",
    "package" : {
        "protected": "eyJlbmMiOiJBMjU2R0NNIn0",
        "unprotected": {"alg": "A256GCMKW"},
        "recipients": [{
            "encrypted_key": "hK_3SKBrl0o762L_4R8CSaKql9nzitYLhLOfBf4Uxd4",
            "header": {
                "kid": "T7n_19BqWjU17l1s",
                "tag": "TEAJd6Ky6u6ujogap4NHeQ",
                "iv": "QrGSBt9t5KWIRhfS"
            }
        }],
        "iv": "gERFsZnJVr-VmMf0",
        "ciphertext": "VYSJXeISVSnMmQ0cL8WXHhRQrI5zfLT6x6jcYPUJ5RuPdikv53LonU_bjI95erMOjP1f4GLzSeTm0Ghrp9IfpNUiPoN5JMiqcGhZANO2K9eoKU3lfQnApaNotxLdNl8zM99pmJQ5Jn6mzUhGW1RuMIrBJAkGM8TVNGbHLHsvkfNriqrhwDiOvuIJ7zsYTTp9axbhxHQ61QyT-2oj8_kC2Xx0CtpnFmSH0C5X0fEH5ZcB3bFT4OKZq7Ma_9GBpg1QUG9pjzyi8fCE2Ci-11rO37hbJuATHcbr8BuaJqpMTmBhb52PXIfuXj7pHFTe0aLAgNZegDUqjKuIE84a_4Wc9RL4Z9TpLveZgQ-QZngJ1uJXBp5o-vB6FxME9FXoV7vojBfmsxTt3EXyHlj886tmy62dnGaJmoXvKT2ZoDCG0SN4KVi4il9xQV-3Tf2YKcAekoKQH2AhRDxVrK6UwNG8m5hdEGc_FPzfzWY222f0GGbzO089g0PFlqqbISmBg2ylblLQFHOHOsd0hXc7rW3CLh6HqomSw6dlIQSs_dGKMAnENIfP57Z6SkabTBkROwv42BcItJfWCTD7y0o_8Mn4G2PsHeVjqfDtQnLv4Id4KUgbuMxz3SKj0S0YxxjFtNMWe-5tlETJtuDOeYxvpTRYK3jH93meoM9VtQCNZrsQ3QnDv3TqYCuR1DDAJDGBM8loIzEMKU1AbeRZTA0HAqdw_kEL9KBxIL6htUDREoJ53PcHocBu79Z230DyYOjLrSEkdSKfA1EKQJhax8J3tvO-sSGSZgCCivb4n6yOdo_5Dlp0bkVA5qjOuamcbZyG10c3_uSOxN8QXcYvptqE1klTkA9oIo35qISos6xzZnO798937iEIiV-mZIdd7DprmBlhwjwU1rnavACqdyl1YKckQHJuRFYh8eqe3hIKunDqnqD5euFC5WJyKacLRT3WQlgWogFakmBIuhCSJwq4hrgblW9uE6PpqCaz1oKy-UmEmXdo5a-U6FnXAshUY7I_rg2x0LKVDaRKK1PviASnaipMQ3cgx1Lzl_7Si8K6Ui92hj2lhJ4ClhXvM8SYcHs_CGeZ3o5r0lVqIWTBnbQoDA8bRmOuGBdRYL3xbN8Bganj1YvIvnCaDP1zPY8cl8ODtWE6iMq1v44RLo1uH643",
        "tag": "uk3bFnvhaZdu7XTlh4pIMA"
    }
}
```


### Insert new section before "Profile Relocation"

#### 15 Publishing
The information exposed through SPXP can originate from any backend, like a content management system. If a profile
accepts contributions from peer profiles, or any other authorized source, it declares a `publishEndpoint` in the profile
root document ([5](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#5-social-profile-root-document)).  
The authorized source needs to be in possession of a valid [authorized signing key (8.2)](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#82-authorized-signing-keys)
to sign its contribution. In the case of private contributions, the source additionally needs to possess a _publishing
key_ and needs to know the encryption group to be used. All of these are contained in the [publishing object
(15.1)](#151-publishing-object-in-connection-package) of the [connection package](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#143-connection-package)
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
                "kid": "QcUQRaiTiOuchvSy",
                "kty": "OKP",
                "crv": "Ed25519",
                "x": "rHdyo3zVbl50ufXSajF71HjidGdBwk-YQSKDM2hS5Yc",
                "d": "_2d29YJOonjeuCdiYmou43Upi2McbSkJZ3rI-bR09ZI"
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
| `post` | Object | required | The [post object](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#10-posts-endpoint) to be published. Can be encrypted. |
| `token` | String | required | One time publishing token as specified in [chapter 15.3](#153-authentication-tokens) |

The server responds with one of these status codes.

| Status code | Meaning |
|---|---|
| `200` | The information has been accepted and stored |
| `403` | The authentication token is not present or invalid |
| `429` | The client IP address has sent too many requests to the profile server in a given time window |

Example:
```json
{
    "type" : "post",
    "ver" : "0.3",
    "post" : {
        "createts" : "2018-09-16T12:23:18.751",
        "type" : "text",
        "message" : "Hello, world!",
        "signature" : {
            "key" : "C8xSIBPKRTcXxFix",
            "aad" : "a0b1c2d3e4f5g6h7i8j9",
            "sig" : XXXX
        }
    },
    "token" : "a0b1c2d3e4f5g6h7i8j9"
}
```

To prevent session replay attacks, the token needs to be embedded in the actual signature as additional authenticated
data (`aad`). On unencrypted payloads, the server needs to verify that the `token` value is part of the signature
(see 8.1](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#81-signing-json-objects)) and that this signature is
valid. On encrypted payloads, the server needs to verify that the `token` is set  as `aad` on the JWE object. Clients
with the appropriate reader key then need to verify that the authentication tag on  the JWE object is  valid and that
this `aad` is included in the signature within the encrypted object (see
[11.4](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#114-private-data-and-signatures)).

##### 15.3 Publishing tokens
To prepare the publishing process, the protocol client first needs to obtain a one time publishing token by sending
an HTTP POST request to the “publish  endpoint“  with a JSON object as HTTP body. This object is composed of the
following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `type` | String | required | Type of request. Use `prepare_post` to obtain a one time publishing token. |
| `ver` | String | required | Most recent version of the SPXProtocol supported by the client |
| `timestamp` | Timestamp | required | Current time |
| `group` | String | optional | In case of private posts, publish group id the client wants to use |

This JSON object must be signed as defined in [chapter 8.1](#81-signing-json-objects) by either the profile key if the
protocol client wants to make a public post or by the "publish key" ([15.1](#151-publishing-object-in-connection-package))
if the client wants to make a private post.

The server responds with one of these status codes.

| Status code | Meaning |
|---|---|
| `200` | A one time authentication token has been issued |
| `403` | The signature is not present, invalid or the signing key is not authorised to post |
| `429` | The client IP address has sent too many requests to the profile server in a given time window |

If the request is accepted, the protocol server responds with a `200` status code and a JSON object with these members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `token` | String | required | The issued one time publishing token |
| `groupRound` | String | optional | If `group` has been present in the request, the most recent round id of this group |

##### 15.4 Attack mitigations
This protocol wants to enable social interactions between profiles, like comments and reactions, while still maintaining
end to end privacy. This makes authenticating legitimate requests more challenging for the protocol server if the goal
is to prevent the server from learning too many details about the contributor. This chapter discusses different attacks
on this process and how these are mitigated.

###### 15.4.1 Preventing Session Replay attacks against token acquisition
It is possible that a malicious actor captures a `prepare_post` request and simply replays this request to the server to
get a fresh token. To prevent this, the server needs to record the last used `timestamp` per signing key and only accept
requests with a more recent timestamp. This is also limiting DoS attacks as the client needs to sign each new request
before being able to send it.

###### 15.4.2 Preventing Session Replay attacks on the publishing endpoint
Since the server does not know anything about the content of the encrypted payload, the publishing endpoint is
vulnerable to session replay attacks. An actor with publishing permissions could simply take any existing post, encrypt
it again with a new IV or round key, acquire a fresh token, and then send it to the publishing endpoint. The server
would always accept it as long as the token is valid. It is intentionally not possible for the server to detect if the
request to the "publish endpoint" has been sent by the same actor who signed the post.  
By embedding the token in the JWE object as additionally authenticated data, we can bind the encrypted object to this
individual token in a way that can be checked by the server without exposing details about the encrypted data. Protocol
clients consuming and decrypting this JWE object then need to validate the `aad` outside on the encrypted JWE against
the `adad` in the signature in the decrypted plaintext.

###### 15.4.3 Privacy guarantees
When a public, unencrypted post is sent to the publishing endpoint, the server obviously knows about the content and
the author. Just like anybody else. But when an encrypted post is sent to the server, the server should only be able to
learn as little as possible its author or any other metadata.  
By issuing publishing keys to individual contributors, we are able to hide the actual identity of the contributor from
the protocol server. However, the server is still able to correlate all posts from the same contributor and link them
to the publishing key.  
There are more sophisticated authentication schemes available like ring signatures, however there is only very little
support for these in today's standard cryptographic libraries. This publishing mechanism is assumed to provide a good
balance between security, privacy concerns and ease of implementation to be able to benefit as many people as possible.
