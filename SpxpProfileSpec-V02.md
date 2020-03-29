# Social Profile Exchange Protocol Specification
Version 0.2

## 1 Social profile

### 1.1 Cryptographic profile key pair
Every social profile is uniquely identified by an asymmetric cryptographic key pair. This key pair must use Elliptic
Curve keys based on the curve “P-256” and has to comply with [RFC 7518 “JSON Web Algorithms
(JWA)”](https://tools.ietf.org/html/rfc7518).
> Note: The curve is subject to change in a later protocol version

XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX change in this version

### 1.2 Profile URL
Every social profile is addressed by a URL as defined in [RFC 1738 “Uniform Resource Locators
(URL)”](https://tools.ietf.org/html/rfc1738). Every protocol client must support at least the scheme “https” and can
choose to additionally support the scheme “http” and others.

### 1.3 Unique profile identification
The cryptographic key takes precedence over the URL. Two profiles published under different URLs but using the same
cryptographic key must be considered identical. And data signed with different cryptographic keys delivered by the same
URL must be treated as different profiles.  
If a profile client detects a change in the signing key used by the profile behind a URL, it has to warn the user and
must not present the data signed by different keys as belonging to the same profile, unless the profile has explicitly
announced a key rollover.
> This protocol version does not yet specify how data gets signed with the profile key.  
> Explicit announcements of profile relocations and key rollovers are not yet specified in this version of the SPXP
> protocol and will be added later.

XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX change in this version

## 2 Communication protocols
Data is exchanged between participating clients and servers via HTTP, preferably over TLS (i.e. HTTPS). Clients and
servers are encouraged to use the latest versions of these protocols, e.g. HTTP/2 and HTTP/3 and to prefer IPv6.

## 3 Transport encoding of structured data
Data is encoded as JSON according to [RFC 7519 “The JavaScript Object Notation (JSON) Data Interchange
Format”](https://tools.ietf.org/html/rfc7159). Please note that this standard defines the HTTP “ContentType” to be
“application/json” with no “charset” parameter. Clients must always use UTF-8 character encoding, irrespective of any
“charset” incorrectly sent by the server.

## 4 Protocol versioning
This protocol uses [Semantic Versioning](https://semver.org/). This document specifies protocol version is “0.2”.

## 5 Social profile root document
Protocol servers respond to requests for the social profile URL with the social profile root document. This JSON object
contains the following members:

| Name | Type | Req/Opt | Description |
|---|---|---|---|
| ver | String | required | Version of the SPXProtocol exposed by this URL. <br/> This specification is defining version “0.2” |
| name | String | required | The display name of this profile |
| about | String | optional | Additional description of this profile |
| gender | String | optional | Free text string specifying the gender of this profile. Clients should recognize the english text strings “female” and “male” and display localized text or icons. All other content can be displayed as-is. |
| website | String | optional | URL of the profile’s website |
| email | String | optional | Email address of this profile |
| birthDayAndMonth | String | optional | String of the format “dd-mm” with “dd” being a numeric value 1-31 and mm being a numeric value 1-12 specifying the day and month of birth in the Gregorian calendar |
| birthYear | String | optional | String containing a positive numeric integer specifying the birth  year of this profile in the Gregorian calendar |
| hometown | String | optional | Social profile URL of the profiles hometown |
| location | String | optional | Social profile URL of the profiles current location |
| coordinates | Object | optional | Object containing two members with numeric values “latitude” and “longitude” specifying the profiles current position in signed degrees format. <br/> Latitude ranges from -90 to +90 and longitude ranges from -180 to +180. |
| profilePhoto | String <br/> Object | optional | Relative or absolute URL pointing to a resource holding a profile photo. Clients should at least support images in JPEG and PNG format. <br/> or <br/> JSON object holding decryption details and the location of an encrypted profile photo resource. (see 6) |
| friendsEndpoint | String | optional | Relative or absolute URL pointing to the “friends endpoint” as specified in chapter 7 |
| postsEndpoint | String | optional | Relative or absolute URL pointing to the “posts endpoint” as specified in chapter 8 |
| keysEndpoint | String | optional | Relative or absolute URL pointing to the “keys endpoint” as specified in chapter 10.2 |
| publicKey | Object | optional | JSON object describing the public key of the profiles key pair as JWK defined in [RFC 7517 “JSON Web Key (JWK)”](https://tools.ietf.org/html/rfc7517) |
| private | Array | optional | Array of private data as specified in chapter 9 |

Example:
```json
{
    "ver" : "0.2",
    "name" : "Crypto Alice",
    "about" : "I love cryptography.",
    "gender" : "female",
    "website" : "https://en.wikipedia.org/wiki/Alice_and_Bob",
    "email" : "cryptoalice@example.com",
    "birthDayAndMonth" : "04-04",
    "birthYear" : "1977",
    "hometown" : "https://example.com/spxp/emerald.city",
    "location" : " https://hill-valley.example.com/profile",
    "coordinates" : {
        "latitude" : "-42.1604",
        "longitude" : "-23.4637"
    },
    "profilePhoto" : " https://images.example.com/alice.jpg",
    "friendsEndpoint" : "friends/alice",
    "postsEndpoint" : "posts?profile=alice",
    "publicKey": {
        "kty": "EC",
        "crv": "P-256",
        "kid": "abcd12345",
        "x": "AOxNxFXeTC9ePGlAWfxSuazIOE--Boz1jKX2Qh7FijGO",
        "y": "ALK3RqZrk5XrNpeyOwr2LfUgY5qFwiIjSef8YAbl03V5"
    }
}
```

## 6 Encrypted resources
External resources, like images and videos, can be stored encrypted with the decryption key being stored as part of the
SPXP JSON data. In this case, the resource is described as JSON object with these members:

| Name | Type | Req/Opt | Description |
|---|---|---|---|
| k | String | required | Octet key as defined in JWE |
| tag | String | required | Message authenticity tag as defined by JWE |
| enc | String | required | Encoding as defined by JWE |
| iv | String | required | Initialisation Vector as defined by JWE |
| url | String | required | Relative or absolute URL pointing to a resource containing the encrypted data |

The data is encrypted according to RFC 7516 “JSON Web Encryption (JWE)”. The entire object is integrity protected via
the document it is contained in.  
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX remove last sentance in this version
Example of a profile with an encrypted profile photo:
```json
{
    "name" : "Crypto Alice",
    "profilePhoto": {
        "k": "qgWVB5SupcdvHARM60knXv8h6tI2unD3GjhsPRKvT_I",
        "tag": "lN19Pd0PrGmIWWp26NctcQ",
        "enc": "A256GCM",
        "iv": "7Y_F7cQWZA-PYoMn",
        "url": "images_enc/alice.encrypted"
    }
}
```

## 7 Friends endpoint
Social profiles can expose a list of other social profiles as “friends”. If the profile root object declares a
“friendsEndpoint”, then it exposes a JSON object as follows:

| Name | Type | Req/Opt | Description |
|---|---|---|---|
| data | Array | required | List of profile URLs as Strings |
| private | Array | optional | Array of private data as specified in chapter 9 |

Example:
```json
{
    "data": [
        "https://example.com/spxp/alice",
        "https://example.com/spxp/bob"
    ]
}
```
	
## 8 Posts endpoint
Social profiles can publish a stream of timestamped messages, named “posts”. If a social profile declares a
“postsEndpoint” in the profile root document, then the server responds with the following JSON object:

| Name | Type | Req/Opt | Description |
|---|---|---|---|
| data | Array | required | List of post objects |
| more | Boolean | required | True if and only if there are more posts before the oldest post in the “data” array |

Each single post in the data array is a JSON object with these members:

| Name | Type | Req/Opt | Description |
|---|---|---|---|
| timestampUTC | String | required | Timestamp of post in the format “YYYY-MM-DD’T’hh:mm:ss.sss” always in UTC |
| type | String | required | Type of post. One of “text”, “web”, “photo”, “video”, “profile” |

Depending on the “type”, additional members are defined as follows:

#### Type “text”:
| Name | Type | Req/Opt | Description |
|---|---|---|---|
| message | String | required | Text message |
| place | String | optional | Social Profile URL of a place linked to the message |

#### Type “web”:
| Name | Type | Req/Opt | Description |
|---|---|---|---|
| message | String | optional | Text message |
| link | String | required | URL of linked web page |

#### Type “photo”:
| Name | Type | Req/Opt | Description |
|---|---|---|---|
| message | String | optional | Text message |
| small | String <br/> Object | required | Absolute URL pointing to a resource holding a preview image. Clients should at least support images in JPEG and PNG format. <br/> or <br/> JSON object holding decryption details and the location of an encrypted preview image resource. (see 6) |
| full | String <br/> Object | optional | Absolute URL pointing to a resource holding a high resolution image. Clients should at least support images in JPEG and PNG format. <br/> or <br/> JSON object holding decryption details and the location of an encrypted high resolution image resource. (see 6) |
| place | String | optional | Social Profile URL of a place linked to the photo |

#### Type “video”:
| Name | Type | Req/Opt | Description |
|---|---|---|---|
| message | String | optional | Text message |
| preview | String <br/> Object | required | Absolute URL pointing to a resource holding a preview image. Clients should at least support images in JPEG and PNG format. <br/> or <br/> JSON object holding decryption details and the location of an encrypted preview image resource. (see 6) |
| media | String <br/> Object | required | Absolute URL pointing to a resource holding a video media file. Clients should at least support MP4 containers with H.264 video and AAC audio codec. <br/> or <br/> JSON object holding decryption details and the location of an encrypted video media resource. (see 6) |
| place | String | optional | Social Profile URL of a place linked to the photo |

#### Type “profile”:
| Name | Type | Req/Opt | Description |
|---|---|---|---|
| message | String | optional | Text message |
| profile | String | required | Social Profile URL |

Example:
```json
{
    "data" : [
        {
            "timestampUTC" : "2018-09-17T14:04:27.373",
            "type" : "text",
            "message" : "Hello, world!"
        }, {
            "timestampUTC" : "2018-09-15T12:35:47.735",
            "type" : "web",
            "message" : "Interesting read...",
            "link" : "https://example.com"
        }, {
            "timestampUTC" : "2018-09-16T13:35:47.735",
            "type" : "photo",
            "message" : "Look at this",
            "full" : "https://example.com/full-image.jpeg",
            "small" : " https://example.com/small-image.jpeg "
        }, {
            "timestampUTC" : "2018-09-15T12:35:47.735",
            "type" : "profile",
            "message" : "Do you know Crypto Alice?",
            "profile" : " https://example.com/spxp/alice"
        }
    ],
    "more" : true
}
```
The “data” array contains a subset of posts known by the server. The number of returned items is chosen by the SPXP
server, but the client can attach a “max” query parameter to influence this number. The client can further attach
“before” and “after” query parameters to specify a date range of requested items. The server guarantees that there are
no additional items available between the oldest and the youngest items returned in the data array. The “more” member in
the response indicates if there are additional items available between the oldest item in the data array and the
requested date range.  
Supported query parameters:

| Name | Type | Description |
|---|---|---|
| max | Integer | Maximum number of items the client can handle in one response. The server can return fewer items than that, but must not return more items. |
| before | timestamp | Only include items with a timestamp before this date |
| after | timestamp | Only include items with a timestamp after this date |

Example:  
Let’s assume the posts endpoint is specified in the profile root document as  
```
https://spxp.example.com/posts?user=alice
```
The client runs this initial query
```
https://spxp.example.com/posts?user=alice&max=2
```
The server returns two post items
```json
{
    "data" : [
        {
            "timestampUTC" : "2018-09-17T14:04:27.373", ...
        }, {
            "timestampUTC" : "2018-09-15T12:35:47.735", ...
        }
    ],
    "more" : true
}
```
Since the server indicated that there are more posts available before the oldest item in the data array, the client runs
another query
```
https://spxp.example.com/posts?user=alice&max=2&before=2018-09-15T12:35:47.735
```
The server then returns another two items
```json
{
    "data" : [
        {
            "timestampUTC" : "2018-09-13T10:06:17.484", ...
        }, {
            "timestampUTC" : "2018-09-12T15:16:17.484", ...
        }
    ],
    "more" : true
}
```
The client could continue this until it reaches the oldest post stored on the server. But ideally, it only loads a
limited window of all available posts and continues to load items when the user has reached the last known posts, e.g.
in an infinite scroll view.  
After some time, the client checks if new posts have been published
```
https://spxp.example.com/posts?user=alice&max=2&after=2018-09-17T14:04:27.373
```
The server returns these items
```json
{
    "data" : [
        {
            "timestampUTC" : "2018-09-20T16:05:28.373", ...
        }, {
            "timestampUTC" : "2018-09-19T15:45:37.735", ...
        }
    ],
    "more" : true
}
```
The more parameter indicates that there are more items available between the oldest item in the data array and the
“after” query parameter. So the client tries to close this gap:
```
https://spxp.example.com/posts?user=alice&max=2&after=2018-09-17T14:04:27.373&before=2018-09-19T15:45:37.735
```
And the server responds with
```json
{
    "data" : [
        {
            "timestampUTC" : "2018-09-18T09:06:17.484", ...
        }
    ],
    "more" : false
}
```

## 9 Private data
Specific JSON objects in the SPXP standard, as listed in 9.1, can contain encrypted data according to [RFC 7516 “JSON
Web Encryption (JWE)”](https://tools.ietf.org/html/rfc7516). These objects comprise an additional member named “private”
containing an array of either a String containing a JWE object in Compact Serialization or an object containing a JWE
object in JSON Serialization. The decrypted plaintext contains again a JSON object in UTF-8 charset encoding. The
decrypted objects are then merged into the main object according to the object merging rules defined in 9.2. If
multiple JWE objects in the “private” array can be decrypted by the SPXP client, then the contained objects are merged
into the containing document in the order they appear in the “private” array.

### 9.1 Private data support
Private data is supported for:
- The social profile root document
- The friend endpoint object
- Individual post items. In this  case, the value of the “timestampUTC” member is used as additional authentication
data (AAD) so that it is integrity protected

### 9.2 Object merging rules
A source object `src` is merged into a target object `dst` as follows:  
For each member in the source object `src.m` do:
- If `src.m` is an array and the target object contains an array with the same name, append the elements of `src.m` to
the elements in the target object `dst.m`.
- If `src.m` is an object and the target object contains an object with the same name, apply these merging rules to the
nested objects (i.e. merge `src.m` into `dst.m`).
- Otherwise, set the member `src.m` in the target object (i.e. `dst.m` := `src.m`).

### 9.3 Full example of private data in profile root document
Example:
```json
{
    "ver" : "0.2",
    "name" : "Crypto Alice",
    "private" : [
        "eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIiwia2lkIjoiQUJDRC4xMjMifQ..8PhcCQkuOjnclrKb.ZUpNLu0Dc7u2ENdmKXR4ZcXA6NKTP08cVYkR8p4SBCq1.sFf31pw_pKT3vP8CiXkjiQ"
    ],
    "profilePhoto" : " https://images.example.com/alice.jpg",
    "friendsEndpoint" : "friends/alice",
    "postsEndpoint" : "posts/alice"
}
```
The “private” array contains one element in JWE Compact Serialization. The string is made up of 5 parts, encoded as
Base64Url and separated by dots.
The first part is
```
    eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIiwia2lkIjoiQUJDRC4xMjMifQ
```
It is Base64Url decoded to the JWE header:
```
    {"alg":"dir","enc":"A256GCM","kid":"ABCD.123"}
```
It specifies direct encryption with 256 bit AES in Galois/Counter Mode using the key “ABCD.123”. Since we do not use a
separate content encryption key (`"alg":"dir"`), the second part is empty. The third part `8PhcCQku…` contains the
Initialization Vector and the forth part `ZUpNLu0D…` the ciphertext. The last part `sFf31pw_…` contains the tag to
validate the message integrity. The ciphertext is then decrypted to
```
    {"website":"https://example.com"}
```
This object gets then merged into the profile root document:
```json
{
    "ver" : "0.2",
    "name" : "Crypto Alice",
    "profilePhoto" : " https://images.example.com/alice.jpg",
    "friendsEndpoint" : "friends/alice",
    "postsEndpoint" : "posts/alice",
    "website" : "https://example.com"
}
```

## 10 Key management
Private data can only be decrypted by readers who are able to obtain the necessary decryption key. Due to the possibly
large number of readers and/or large number of data items, we cannot simply encrypt every single data element for all
possible readers. Instead, readers are organized in a hierarchical structure of groups. Decryption keys are then
encrypted themselves along a path through this hierarchy (key wrapping). The _keys endpoint_ (10.2) allows clients to
discover groups relevant to them and to obtain keys required to decrypt dependant keys and data elements.

### 10.1 Key Groups
Readers are organised in _groups_. Groups that are used to control the visibility of data are referred to as _publishing
groups_; while groups that are only generated by the client internally to better organize readers are referred to as
_virtual groups_. Logically, there is no difference between both, other than that publishing groups are available to the
end user when defining the audience of a new item.  
Groups can have individual readers or other groups as members. A client could for example maintain the following
hierarchy of groups and readers:
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX insert image
Each group is uniquely identified by a _group id_. This id should be generated randomly. A stream of _round keys_ is
associated with each group. The key id of these round keys is generated by concatenating the group id with a dot and a
random round id. In the example above, the publishing group “Friends” could have the following stream of keys:
```
    grp-friends.key0, grp-friends.key1, grp-friends.key2 ...
```
The group id and the round id must only use characters used by the Base64Url encoding.  
If a group or reader is a member of another group, then it can decrypt the key stream of that group with its own key(s).
In the example above, the social profile could maintain the following keys:

| Key id | Can be decrypted with |
|---|---|
| grp-friends.key0 | grp-closefriends.key0, grp-virt0.key0, grp-family.key0 |
| grp-friends.key1 | grp-closefriends.key1, grp-virt0.key1, grp-family.key0 |
| grp-friends.key2 | grp-closefriends.key1, grp-virt0.key2, grp-family.key1 |
| grp-closefriends.key0 | key-bob |
| grp-closefriends.key1 | key-bob |
| grp-family.key0 | key-charlie |
| grp-family.key1 | key-charlie |
| grp-virt0.key0 | key-alice |
| grp-virt0.key1 | key-alice |
| grp-virt0.key2 | key-alice |
When a new post is created, the client of this profile would offer the groups “Friends”, “Close Friends” and “Family” as
possible audience. If the user selects “Friends” as audience, the client would pick the most recent key of this group,
“grp-friends.key2” in this example, and use it to encrypt the private data of this post.  
When Alice finds this post with the associated private data, she looks at the JWE header of the private data and learns
the required key id “grp-friends.key2”. If she does not have this key yet, she uses the keys endpoint to obtain the
encrypted keys “grp-friends.key2” and “grp-virt0.key2”, decrypts the key “grp-virt0.key2” with her private key
“key-alice”, uses this unwrapped key to further decrypt the key “grp-friends.key2” and finally uses this unwrapped key
to decrypt the private data.

## 10.2 Keys endpoint
If a social profile makes use of private data as defined in section 9, it has to announce a keys endpoint in the profile
root document (see section 5).  
This endpoint takes two request parameters:

| Name | Type | Description |
|---|---|---|
| connectionId | String | Mandatory key id of the of the reader’s personal key |
| request | String | Optional comma separated list of round key ids that the caller wants to decrypt |

The server responds with a 3 level JSON object. The names used for the members in each level are:  

| Level | Content |
|---|---|
| Outermost level | Group id or personal key id required to decrypt the keys given in this object |
| Middle level | Group id that can be decrypted |
| Inner level | Round key id |

For example, if “key-alice” can decrypt round keys of the two groups “groupA” and “groupB”, then the response will look
like this:
```json
{
    "key-alice" : {
        "groupA" : {
            "groupA.key0" : "...",
            "groupA.key1" : "...",
            "groupA.key2" : "..."
        },
        "groupB" : {
            "groupB.key0" : "...",
            "groupB.key1" : "...",
            "groupB.key2" : "..."
        }
    }
}
```
If “groupA” is a member of “groupX”, so that round keys of “groupA” can decrypt round keys of “groupX”, the response
could look like this:
```json
{
    "key-alice" : {
        "groupA" : {
            "groupA.key0" : "...",
            "groupA.key1" : "...",
            "groupA.key2" : "..."
        },
        "groupB" : { ... }
    }
    "groupA" : {
        "groupX" : {
            "groupX.key0" : "...",
            "groupX.key1" : "...",
            "groupX.key2" : "..."
        }
    }
}
```
The innermost member contains a JWE object in compact serialization that contains the wrapped round key as JWK object.
The JWE headers of the “groupX.key#” entries would define one of the “groupA.key#” keys as the valid decryption key.  
If the “request” parameter is given, the server returns all round keys given in this list as well as all intermediate
round keys required to decrypt these keys until the initial key given as “connectionId”.  
Example:
```
GET https://example.com/spxp/roger/keys?connectionId=key-alice&request=groupX.key2
200 OK
{
    "key-alice" : {
        "groupA" : {
            "groupA.key1" : "..."
        }
    }
    "groupA" : {
        "groupX" : {
            "groupX.key2" : "..."
        }
    }
}
```
In this example, the round key “groupX.key2” is encrypted with the round key “groupA.key1”. The key id required to
decrypt the JWE object is given in the JWE header.  
If the “request” parameter is missing, the server can chose the set of round keys that are returned. This set has to
include at least all round keys that are required to read all private data in the profile root document accessible to
the key given in the “connectionId”. It additionally should include enough round keys to decrypt a sensible amount of
most recent posts.

## 10.3 Supported encodings
There are currently no restrictions on the JWE encodings supported by SPXP and on the key wrapping encodings, other than
that these encodings need to be supported in JWE.  
Later versions of this protocol might set stronger requirements.

## 10.4 Reader keys
There are currently no restrictions on the reader’s keys and key ids. Cryptographic keys can be distributed by any side
channel among the audience and then used on the keys endpoint. There is also no requirement that readers have to
maintain a profile themselves.  
If the reader however maintains a profile, then profile key pair as specified in 1.1 of both profiles is used to derive
the encryption key as defined in 10.5. In this case, the “kid” of the profile public key has to be used as
“connectionId”.

## 10.5 Key derivation function for profiles
Before deriving the shared secret, the profile public key has to be sanity tested. Otherwise it is possible that a
maliciously crafted public key can lead to information leakage. After this test, the following steps are performed:
1.	Generate a random key id by Base64Encoding 8 random bytes
2.	Calculate the shared secret with the elliptic-curve Diffie-Hellman key agreement algorithm between the own private
key and the peer public key
3.	Concatenate the following bytes: The ASCII bytes of the text string “SPXP-KDF”, the shared secret, and the ASCII
bytes of the random key
4.	Calculate the  SHA256 message digest of the concatenated bytes
5.	Use the first n bits of the calculated digest, where n indicates the required key size
When reading the response of the keys endpoint, then the random key ID of step 1 is given as “kid” in the JWE header of
the encrypted key and the required key size depends on the used encryption defined in the “enc” member of the JWE
header.
