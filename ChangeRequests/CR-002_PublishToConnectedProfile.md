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

## Proposed changes to SPXP Spec

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
| `publish` | When accepting this request, the requestee is given additional information in the `publishing` object of the connection package to publish posts on this profile (only in its own name) |

Example:
```json
{
    "type" : "connection_request",
    "ver" : "0.3",
    "timestamp" : "2020-01-12T09:40:17.734",
    "expires" : "2020-07-12T09:40:17.734",
    "establishId" : "K4dwfD4wA67xaD-t",
    "requester" : {
        "uri" : "https://example.com/spxp/alice",
        "publicKey" : {
            "kid" : "C8xSIBPKRTcXxFix",
            "kty" : "OKP",
            "crv" : "Ed25519",
            "x" : "skpRppgAopeYo9MWRdExl26rGA_z701tMoiuJ-jIjU8"
        }
    },
    "requestee" : {
        "uri" : "https://example.com/spxp/bob",
        "publicKey" : {
            "kid" : "czlHMPEJcLb7jMUI",
            "kty" : "OKP",
            "crv" : "Ed25519",
            "x" : "vg42ogNHigJnwZ0pwwMzUtaXZA49eqcfGYl2u9GR8vg"
        }
    },
    "offering" : [
        "read"
    ],
    "establishKey" : {
        "kid" : "T7n_19BqWjU17l1s",
        "kty" : "oct",
        "alg" : "A256GCM",
        "k" : "AnkrwD_Et1E0-FB0XYU38hpmdEGr0LOBO8O2HRdzgOw"
    },
    "signature" : {
        "sig" : "hLNJLtg76uoynSDcW419Kt01tF3dylYBnhRvu3gUxLiyI7kcdQiwmv6Fmg-X0u0JGK2AHuzO14HIF3QQqz5xDA",
        "key" : "C8xSIBPKRTcXxFix"
    }
}
```

### In section "14.7 Connect endpoint"

Replace Example:
```json
{
    "type" : "connection_request",
    "ver" : "0.3",
    "msg" : {
        "protected" : "eyJlbmMiOiJBMjU2R0NNIn0",
        "unprotected" : {
            "alg" : "ECDH-ES"
        },
        "recipients" : [ {
            "header" : {
                "kid" : "-aiWD4qECT28QUyh",
                "epk" : {
                    "kid" : "aRXfOEpa0iKhx5s9",
                    "kty" : "OKP",
                    "crv" : "X25519",
                    "x" : "Hdn21J5bP2y-Hw-LVDGOa9CNhZ3cRYXGJC0BhjAoVyI"
                }
            }
        } ],
        "iv": "0BDtOlc3hG-FR3bG",
        "ciphertext": "KYsJ16xQMcpBy7IAJ9sxZPgjdZFEvoVhceEAO4LxSaW3cXXq2deCIUDyFzQlQ1PBQG_J9_seAEquUFu1bSX4LBEQojaIef5Ki7xdGr93hzO0TGysD_2LMwC1P16abjsbN8ton8Pwa8nFeQFPAhNl9w2Bw92OKgw-3ZuNm1q3F0GjJfFoqTEutsw2HHQXOByXlR2515r6qjyEfI0vSWfXk97nch5HAyRlPEBid4256K0AOlCQGsIJRF332KLP4pneP8maSYcRYTmbUmzT_-qHqlQ-dP1Eqiq7pNVNyww_VnlllAadte9XW_QERoR34rSlqEajSleo8Osp4AfHBR2HPcirm8CF0v15V6gnE3GdjY07VWz9pm129ScXKxm5bx3Ke-66c1pMxHwi3ElnMaIWLRimRkXWzrwQfJKx1HLmrs4rxMLee1LvCoS75nms46eiIJt882I2y3-xCL5FkGqHrMOACmdbznWM6GwHmtqr7fW5xVPDUrP7gImF9YK97Bf40D3NZxl6iLJQtVdBoIRpJ39Vi-S7VnSR9bZs4I_ZqcMSWVgUh-d6WfyksW1RANzU00SfXQTC_7vPDj1y9Z6IROcCH4Lc0mMxdRc0gQIJLt53JgythMwwbJpVX3TRRRyPHHnjf6Qfm71kBEDHHtiGpQ0HkeosovzZaQPgWe7sS64xWjJAfb6G6c1fP28NpHe9T6wqK0lqMmBWrWCBpjoSqOEF5pgnS_ljc_Xr9MNsekRzPg68dHlpEDdedrXPsECcsgdeAbwH0Bhqt0Wf9HESSmDTO_Os2R9XusdQvoBMSMtv__bIV_tcDZt47LrE-Fk-khY8n3JjSFfz-OKuPd2JNnp8RcCSMVwqMzY7Ifpsfz4YcxwORxsciBKPsejg3ijW6E6QfpGTVeo54oa7KSDgBapdzXjchfX_ZLW6Rgvsf49RzqSKesyddYyyXInb88C5S4KY-MaCyc1fJO6S0Lqk_hQck2mUe2rSfbs_rU-Imz2iiwQuZNk4d5YOTe1M7XLCSV6xOK1ZxCStBp-YI7hHD_8pV5Br107LVcsin0h3PDgNiXoLUzuu1ThxQEAyQINUAg3a7MnAcsSeRAtCQdZ8PAuK6KKYpkKfy7l5RlSEPZuARZbkhtngZa6y2TgCBSK-emTT_9ujZziigj3BwTIisvrVUCs5WKYUYmrRCyK-fQNaNGvGn2NXe0-qGgkbhayYOsfE3SGYE1aZ0GxT7PISpZrz6JgSJ249TEnViiyU6lXu41rzGBSvKRRPDOA4pjjXcIHetg7oWDe2W6bBfYgP_6Dd4meyc7x8PwUDyGY6H_MI_1AIZLIwkMBGd1pXFTJpEnAWv56nL0kEkXCVm2k7rcK575FzsYGJpapR9T_cB0FVwwoP7JkLc6ZBYw61xfSi5Iyd7ijGeJIrSIT9oM0xCRgaOAlj6jm4IVLEN3hXnNUOZxzjlG0tQB2-5dw6rb6ZShcieLESTIu7ppx3q0tLWooKmJZ8di_J6F4df4YP03W3iJTWI9IK",
        "tag": "8E15Cnsiu300B77pn3PjiA"
    },
    "token" : {
        "method" : "spxp.org:webflow:1.0",
        "value" : "some-token-value"
    }
}
```

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
If a profile grants publishing permissions to a peer profile during a connection process, it provides the required
information  as part of the `publishing` object in the [connection package](../SPXP-Spec.md#143-connection-package) with
the following  members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `certificate` | Object | required | JSON object describing the certificate containing the [authorized signing key](#82-authorized-signing-keys) |
| `postPublic` | Boolean | required | Flag indicating if the peer is authorised to make public unencrypted posts on this profile |
| `postPrivate` | Object | optional | JSON object containing additional information for the private publishing process |

The optional `postPrivate` JSON objects has the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `publishKey` | Object | required | JSON object describing the signing key pair used in the private publishing process as JWK defined in [RFC 7517 “JSON Web Key (JWK)”](https://tools.ietf.org/html/rfc7517) using the Ed25519 curve specifier as defined in [RFC 8037 Section 3.1](https://tools.ietf.org/html/rfc8037#section-3.1) |
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
        "sig": "p5XKQ3uuQW7UwZUy72qLcfNV_jDzFUSdmu5mAsMDn5ju4mPpHx7bJatohbVQEhW8t8CFiN6LT1xZ-g0j9ZXABA",
        "key" : "C8xSIBPKRTcXxFix"
    }
}
```

##### 15.2 Publish endpoint
To publish a post on the peer profile, the protocol client sends an HTTP POST request to the “publish endpoint“ with a
JSON object as HTTP  body. This object is composed of the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `type` | String | required | Fixed text string `post` |
| `ver` | String | required | Most recent version of the SPXProtocol supported by the client |
| `post` | Object | required | The [post object](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#10-posts-endpoint) to be published. Can be encrypted. |
| `token` | String | required | One time publishing token as specified in [chapter 15.3](#153-publishing-tokens) |

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
    "post" : {
        "createts": "2018-09-16T12:23:18.751",
        "type": "text",
        "message": "Hello, world!",
        "signature": {
            "key": "czlHMPEJcLb7jMUI",
            "aad": "a0b1c2d3e4f5g6h7i8j9",
            "sig": "PYXU88UoBpwAh_rp8pB2S5JwQaioeo-fcrZDjI9BMLPe8uZFtTj_dNSHM_ec_cPSy9J-jgr_y_qve7zhEkVTDw"
        }
    },
    "token" : "a0b1c2d3e4f5g6h7i8j9"
}
```

To prevent session replay attacks, the token needs to be embedded in the actual signature as additional authenticated
data (`aad`). On unencrypted payloads, the server needs to verify that the `token` value is part of the signature
(see [8.1](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#81-signing-json-objects)) and that this signature is
valid. On encrypted payloads, the server needs to verify that the `token` is set  as `aad` on the JWE object. Clients
with the appropriate reader key then need to verify that the authentication tag on  the JWE object is valid and that
this `aad` is included in the signature within the encrypted object (see
[11.4](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#114-private-data-and-signatures)).

##### 15.3 Publishing tokens
To prepare the publishing process, the protocol client first needs to obtain a one time publishing token by sending
an HTTP POST request to the “publish  endpoint“  with a JSON object as HTTP body. This object is composed of the
following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `type` | String | required | Fixed text string `prepare_post` |
| `ver` | String | required | Most recent version of the SPXProtocol supported by the client |
| `timestamp` | Timestamp | required | Timestamp when the client created this request |
| `group` | String | optional | In case of private posts, publish group id the client wants to use |

This JSON object must be signed as defined in [chapter 8.1](#81-signing-json-objects) by either the profile key if the
protocol client wants to make a public post or by the "publish key" ([15.1](#151-publishing-object-in-connection-package))
if the client wants to make a private post.

The server responds with one of these status codes.

| Status code | Meaning |
|---|---|
| `200` | A one time publishing token has been issued |
| `403` | The signature is not present, invalid or the signing key is not authorised to post |
| `429` | The client IP address has sent too many requests to the profile server in a given time window |

If the request is accepted, the protocol server responds with a `200` status code and a JSON object with these members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `token` | String | required | The issued one time publishing token |
| `groupRound` | String | optional | If `group` has been present in the request, the most recent round id of this group |

Example POST request:
```json
{
    "type" : "prepare_post",
    "ver" : "0.3",
    "timestamp" : "2021-06-21T14:11:35.621",
    "group" : "grp-friends",
    "signature": {
        "key": "QcUQRaiTiOuchvSy",
        "sig": "vsHv1qr5nVwPOg0BJABMelvKf4KqG92Tf9HAARC-Tq8gcXHeBlbiVrJUZ7z8EcnNDyEHyccpvdSkpR2KpuWYDg"
    }
}
```
Example response:
```json
{
    "token" : "a0b1c2d3e4f5g6h7i8j9",
    "groupRound" : "key2"
}
```

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
learn as little as possible about its author or any other metadata.  
By issuing publishing keys to individual contributors, we are able to hide the actual identity of the contributor from
the protocol server. However, the server is still able to correlate all encrypted posts from the same contributor and
link them  to a publishing key.  
There are more sophisticated authentication schemes available like ring signatures, however there is only very little
support for these in today's standard cryptographic libraries. The publishing scheme used in this protocol version is
assumed to provide a good  balance between security, privacy concerns and ease of implementation to be able to benefit
as many people as possible.

## Proposed changes to SPXP-PME Spec

### In section "3 Service info"
Add the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `endpoints.publishEndpoint` | String | optional | `publishEndpoint` to be used in the profile root document |

Update the example:
```json
{
    "server" : {
        "vendor" : "ACME Corp.",
        "product" : "Fancy SPXP Server (tm)",
        "version" : "1.0",
        "helpUri" : "http://acme.example.com/fancy-server/user-help.html"
    },
    "endpoints" : {
        "friendsEndpoint" : "friends/alice",
        "postsEndpoint" : "posts?profile=alice",
        "keysEndpoint" : "keys/alice",
        "connectEndpoint" : "connect/alice",
        "connectResponseEndpoint" : "connectResponse/alice",
        "publishEndpoint" : "publish/alice"
    },
    "limits" : {
        "maxMediaSize" : 10485760
    }
}
```

### Insert new section at the end

#### 10 Publishing key management
To be able to authenticate requests for publishing tokens on the [publishing endpoint](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md##153-publishing-tokens),
the server needs to know the set of keys which are authorized to post publicly and those authorized to post privately.  
Although fundamentally different from reader keys, the lifecycle is similar: They are exchanged as part of connection
packages and either need to be activated during the package exchange or they get published most commonly together with
a set of reader keys when accepting connections. Hence, we tread these keys like reader keys and use the usual
[key management endpoints](https://github.com/spxp/spxp-specs/blob/master/SPXP-PME-Spec.md#8-key-management) and [package
preparation endpoints](https://github.com/spxp/spxp-specs/blob/master/SPXP-PME-Spec.md#91-preparing-a-package) with
the following 3 level JSON object structure:

| Level | Content |
|---|---|
| Outermost level | Fixed text string `@publish@` |
| Middle level | key id |
| Inner level | Fixed text string `public` or `private` depending on allowed key use |

To be compatible with round keys, the JWK of the public signing key is transferred as a String containing the JSON
serialisation of the JWK.

Example on the `<baseUri>/keys` endpoint:
```json
{
    "@publish@" : {
        "QcUQRaiTiOuchvSy" : {
            "private" : "{\"kid\":\"QcUQRaiTiOuchvSy\",\"kty\":\"OKP\",\"crv\":\"Ed25519\",\"x\":\"rHdyo3zVbl50ufXSajF71HjidGdBwk-YQSKDM2hS5Yc\"}"
        },
        "czlHMPEJcLb7jMUI" : {
            "public" : "{\"kid\":\"czlHMPEJcLb7jMUI\",\"kty\":\"OKP\",\"crv\":\"Ed25519\",\"x\":\"vg42ogNHigJnwZ0pwwMzUtaXZA49eqcfGYl2u9GR8vg\"}"
        }
    }
}
```

Example on the `<baseUri>/connect/packages` endpoint:
```json
{
    "establishId" : "K4dwfD4wA67xaD-t",
    "expires" : "2020-07-12T09:40:17.734",
    "package" : {
        "..." : "..."
    },
    "keys" : {
        "audience1" : {
            "group1" : {
                "round1" : "<JWK>",
                "round2" : "<JWK>",
                "round3" : "<JWK>"
            },
            "group2" : {
                "round1" : "<JWK>",
                "round2" : "<JWK>",
                "round3" : "<JWK>"
            }
        },
        "@publish@" : {
            "QcUQRaiTiOuchvSy" : {
                "private" : "{\"kid\":\"QcUQRaiTiOuchvSy\",\"kty\":\"OKP\",\"crv\":\"Ed25519\",\"x\":\"rHdyo3zVbl50ufXSajF71HjidGdBwk-YQSKDM2hS5Yc\"}"
            },
            "czlHMPEJcLb7jMUI" : {
                "public" : "{\"kid\":\"czlHMPEJcLb7jMUI\",\"kty\":\"OKP\",\"crv\":\"Ed25519\",\"x\":\"vg42ogNHigJnwZ0pwwMzUtaXZA49eqcfGYl2u9GR8vg\"}"
            }
        }
    }
}
```
