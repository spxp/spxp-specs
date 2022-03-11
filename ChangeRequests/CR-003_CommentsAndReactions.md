# CR-002 - Publish To Connected Profile
Draft - Target SPXP 0.4

...

## Proposed changes to SPXP Spec

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

Update first post in example:
```json
        {
            "seqts" : "2018-09-17T14:04:27.373",
            "createts" : "2018-09-16T12:23:18.751",
            "type" : "text",
            "message" : "Hello, world!",
            "contribute" : {
                "comment" : true,
                "react" : true,
                "group" : "grp-friends"
            },
            "signature" : {
                "key" : "C8xSIBPKRTcXxFix",
                "sig" : ...
            }
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
