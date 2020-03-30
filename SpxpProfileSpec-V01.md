# Social Profile Exchange Protocol Specification
Version 0.1

## 1 Profile URL
Every social profile is addressed by a URL as defined in [RFC 1738 “Uniform Resource Locators
(URL)”](https://tools.ietf.org/html/rfc1738). Every protocol client must support at least the scheme “https” and can
choose to additionally support the scheme “http” and others.

## 2 Communication protocols
Data is exchanged between participating clients and servers via HTTP, preferably over TLS (i.e. HTTPS). Clients and
servers are encouraged to use the latest versions of these protocols, e.g. HTTP/2 and HTTP/3 and to prefer IPv6.

## 3 Transport encoding of structured data
Data is encoded as JSON according to [RFC 7519 “The JavaScript Object Notation (JSON) Data Interchange
Format”](https://tools.ietf.org/html/rfc7159). Please note that this standard defines the HTTP “ContentType” to be
“application/json” with no “charset” parameter. Clients must always use UTF-8 character encoding, irrespective of any
“charset” incorrectly sent by the server.

## 4 Protocol versioning
This protocol uses [Semantic Versioning](https://semver.org/). This document specifies protocol version is “0.1”.

## 5 Social profile root document
Protocol servers respond to requests for the social profile URL with the social profile root document. This JSON object
contains the following members:

| Name | Type | Req/Opt | Description |
|---|---|---|---|
| ver | String | required | Version of the SPXProtocol exposed by this URL. <br/> This specification is defining version “0.1” |
| name | String | required | The display name of this profile |
| about | String | optional | Additional description of this profile |
| gender | String | optional | Free text string specifying the gender of this profile. Clients should recognize the english text strings “female” and “male” and display localized text or icons. All other content can be displayed as-is. |
| website | String | optional | URL of the profile’s website |
| email | String | optional | Email address of this profile |
| birthDayAndMonth | String | optional | String of the format “dd-mm” with “dd” being a numeric value 1-31 and “mm“ being a numeric value 1-12 specifying the day and month of birth in the Gregorian calendar |
| birthYear | String | optional | String containing a positive numeric integer specifying the birth  year of this profile in the Gregorian calendar |
| hometown | String | optional | Social profile URL of the profiles hometown |
| location | String | optional | Social profile URL of the profiles current location |
| coordinates | Object | optional | Object containing two members with numeric values “latitude” and “longitude” specifying the profiles current position in signed degrees format. <br/> Latitude ranges from -90 to +90 and longitude ranges from -180 to +180. |
| profilePhoto | String | optional | Relative or absolute URL pointing to a resource holding a profile photo. Clients should at least support images in JPEG and PNG format. |
| friendsEndpoint | String | optional | Relative or absolute URL pointing to the “friends endpoint” as specified in chapter 6 |
| postsEndpoint | String | optional | Relative or absolute URL pointing to the “posts endpoint” as specified in chapter 7 |

Example:
```json
{
    "ver" : "0.1",
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
    "postsEndpoint" : "posts?profile=alice"
}
```

## 6 Friends endpoint
Social profiles can expose a list of other social profiles as “friends”. If the profile root object declares a
“friendsEndpoint”, then it exposes a JSON object as follows:

| Name | Type | Req/Opt | Description |
|---|---|---|---|
| data | Array | required | List of profile URLs as Strings |

Example:
```json
{
    "data": [
        "https://example.com/spxp/alice",
        "https://example.com/spxp/bob"
    ]
}
```
	
## 7 Posts endpoint
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
| small | String | required | Absolute URL pointing to a resource holding a preview image. Clients should at least support images in JPEG and PNG format. |
| full | String | optional | Absolute URL pointing to a resource holding a high resolution image. Clients should at least support images in JPEG and PNG format. |
| place | String | optional | Social Profile URL of a place linked to the photo |

#### Type “video”:
| Name | Type | Req/Opt | Description |
|---|---|---|---|
| message | String | optional | Text message |
| preview | String | required | Absolute URL pointing to a resource holding a preview image. Clients should at least support images in JPEG and PNG format. |
| media | String | required | Absolute URL pointing to a resource holding a video media file. Clients should at least support MP4 containers with H.264 video and AAC audio codec. |
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
            "timestampUTC" : "2018-09-17T14:04:27.373", "type": "..."
        }, {
            "timestampUTC" : "2018-09-15T12:35:47.735", "type": "..."
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
            "timestampUTC" : "2018-09-13T10:06:17.484", "type": "..."
        }, {
            "timestampUTC" : "2018-09-12T15:16:17.484", "type": "..."
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
            "timestampUTC" : "2018-09-20T16:05:28.373", "type": "..."
        }, {
            "timestampUTC" : "2018-09-19T15:45:37.735", "type": "..."
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
            "timestampUTC" : "2018-09-18T09:06:17.484", "type": "..."
        }
    ],
    "more" : false
}
```
