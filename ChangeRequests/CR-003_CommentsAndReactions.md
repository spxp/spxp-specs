# CR-002 - Publish To Connected Profile
Draft - Target SPXP 0.4

...

## Proposed changes to SPXP Spec

### In section "8.2 Authorized signing keys"

Change the table of grant types as follows:

| Grant | Allows to sign |
|---|---|
| `post` | Individual post objects of types other than `comment` and `reaction`. If not combined with `impersonate`, then only in their own name. |
| `comment` | Individual post objects of type `comment`. If not combined with `impersonate`, then only in their own name. |
| `react` | Individual post objects of type `reaction`. If not combined with `impersonate`, then only in their own name. |

Change example:
```json
{
    "publicKey" : {
        "kid" : "czlHMPEJcLb7jMUI",
        "kty" : "OKP",
        "crv" : "Ed25519",
        "x" : "vg42ogNHigJnwZ0pwwMzUtaXZA49eqcfGYl2u9GR8vg"
    },
    "grant" : [ "post", "comment", "react" ],
    "signature" : {
        "key" : "C8xSIBPKRTcXxFix",
        "sig" : "HBOSFxl09FpZ5vMJFaoOTPUMwkCDz43pb5cHydj-rxrd0oI7IXCcvTN_JhbZUJ7FR7VT2b0oCWfHRoZ26fzMAQ"
    }
}
```

### In section "10 Posts endpoint"

Introduce a new sub-section header "10.1 Post object" before "Each single post". Change references to individual post
objects in the spec text to this new sub-section, e.g. in 15.2

### In the new sub-section "10.1 Post object"

Add the following member to the post object:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `contribute` | Object | optional | JSON object governing contributions like comments or reactions to this post |

After the note, add the following:

The optional `contribute` object consists of these members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `comment` | Boolean | optional | Flag indicating if comments referencing this post are allowed, `false` by default |
| `react` | Boolean | optional | Flag indicating if reactions referencing this post are allowed, `false` by default |
| `group` | String | optional | Publishing group in which comments and reactions to this post must be published |

Add the following two post types:

#### Type `comment`:
| Name | Type | Mandatory | Description |
|---|---|---|---|
| `message` | String | required | Text message |
| `ref` | Object | required | [Post reference object](#102-post-reference-object) referencing post this comment refers to|

#### Type `reaction`:
| Name | Type | Mandatory | Description |
|---|---|---|---|
| `emoji` | String | required | Single unicode character text emoji |
| `ref` | Object | required | [Post reference object](#102-post-reference-object) referencing post this reaction refers to|

Update example:
```json
{
    "data" : [
        {
            "seqts" : "2018-09-17T14:04:27.373",
            "createts" : "2018-09-16T12:23:18.751",
            "type" : "text",
            "message" : "Hello, world!",
            "signature" : {
                "key" : "C8xSIBPKRTcXxFix",
                "sig" : "bDOgcT4uxTKYMTuOJXDbAPc1UA2p-aGdxwplUWNStzyDRIRPu9UxaTU1IoZ1ELjBY5iRf4FEBPV09Uw9TOYuCA"
            }
        }, {
            "seqts" : "2018-09-15T12:35:47.735",
            "type" : "web",
            "message" : "Interesting read...",
            "link" : "https://example.com",
            "signature" : {
                "key" : "C8xSIBPKRTcXxFix",
                "sig" : "skQBzttDURV-N4kqK9fgyWw4Ddixsmld4nnilC_XUqSZhXfeNfw_4PrIlLwaFdHDTO-au4iaZM64oSWLP-z0BA"
            }
        }, {
            "seqts" : "2018-09-16T13:35:47.735",
            "createts" : "2018-09-16T12:25:13.614",
            "author" : "https://example.com/ctypto.bob",
            "type" : "photo",
            "message" : "Look at this",
            "full" : "https://example.com/full-image.jpeg",
            "small" : " https://example.com/small-image.jpeg",
            "signature" : {
                "key" : {
                    "publicKey" : {
                        "kid" : "czlHMPEJcLb7jMUI",
                        "kty" : "OKP",
                        "crv" : "Ed25519",
                        "x" : "vg42ogNHigJnwZ0pwwMzUtaXZA49eqcfGYl2u9GR8vg"
                    },
                    "grant" : [ "post", "comment", "react" ],
                    "signature" : {
                        "key" : "C8xSIBPKRTcXxFix",
                        "sig" : "HBOSFxl09FpZ5vMJFaoOTPUMwkCDz43pb5cHydj-rxrd0oI7IXCcvTN_JhbZUJ7FR7VT2b0oCWfHRoZ26fzMAQ"
                    }
                },
                "sig" : "94dyGxvPcVuueFjVj_RwedWy5m3dasRDYf1iOxnYXUEYDS33LYzn9kqe6aIRMZchxWqlM1K_fX-uHVFDRjzSAg"
            }
        }, {
            "seqts" : "2018-09-19T05:12:18.264",
            "createts" : "2018-09-19T05:12:17.157",
            "type" : "reaction",
            "author" : "https://example.com/ctypto.bob",
            "emoji" : "❤",
            "ref" : {
                "seqts" : "2018-09-17T14:04:27.373",
                "hash" : "wu-qP3IBAyttD_6QVI1mlypRVMTzxTIpH6jrkFkHEct57bHN2xBj0GYvaniLPiVgLNJxqSwDCMaPMEYYzprVWg"
            },
            "signature" : {
                "key" : {
                    "publicKey" : {
                        "kid" : "czlHMPEJcLb7jMUI",
                        "kty" : "OKP",
                        "crv" : "Ed25519",
                        "x" : "vg42ogNHigJnwZ0pwwMzUtaXZA49eqcfGYl2u9GR8vg"
                    },
                    "grant" : [ "post", "comment", "react" ],
                    "signature" : {
                        "key" : "C8xSIBPKRTcXxFix",
                        "sig" : "HBOSFxl09FpZ5vMJFaoOTPUMwkCDz43pb5cHydj-rxrd0oI7IXCcvTN_JhbZUJ7FR7VT2b0oCWfHRoZ26fzMAQ"
                    }
                },
                "sig" : "mWLyCJs1QN0ZG8W53QnQNMXqh4059RsFRhLw_LafjrXVRQFnEoioYyG-KfZbyTsiCXmvABiDAberRF8doPyDAA"
            }
        }
    ],
    "more" : true
}
```

### Insert new sub-section "10.2 Post reference object"
Posts in a profile's timeline are referenced by combining the `seqts` of the post with a SHA512 hash of the post. This
guarantees that referenced post is exactly what has been referenced in a comment or reaction. References to other posts
are described by a JSON object with these members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `seqts` | timestamp | required | Sequence timestamp of referenced post |
| `seqts` | String | required | [Profile URI](#12-profile-uri) of referenced profile |
| `hash` | String | required | Base64Url encoded SHA512 hash value of post object |

To calculate the `hash` value of a post object, the `private` ([11](#11-private-data)) data is processed based on the
reader keys available to the client. It is then converted into [Canonical JSON](#811-canonical-json) and converted to a
byte stream using UTF-8 character encoding. The calculated SHA512 hash value is then represented as Base64Url encoded.
In contrast to signatures, no fields are removed from the object before creating the hash value.
