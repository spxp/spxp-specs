# SPXP - Profile Management Extension
Version 0.3

## 1 Base URI
A management server can manage profiles with various profile URIs, not just profiles on the same domain. To give the
implementors flexibility on the layout of the URI hierarchy on this domain, this spec defines the API relative to
a base URI.

## 2 Authentication
This API uses an OAuth like authentication scheme.

### 2.1 Device Registration
Each device is granted a long living device token. Each device can choose a random unique device ID.  
Endpoint: `<baseUri>/auth/device`  
Method: `POST`  
Content-Type: `application/json`  
Body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `profile_uri` | String | required | Social profile URI as specified by SPXP of the profile for which a device should be registered |
| `device_id` | String | required | A random ID uniquely identifying this device |
| `timestamp` | timestamp | required | Timestamp when the client created this request |

The device registration request must always be signed by the profile owner as specified by SPXP.

Example:
```json
{
    "profile_uri" : "https://example.com/spxp/alice",
    "device_id" : "my-fancy-phone",
    "timestamp" : "2020-01-15T10:39:15.437",
    "signature" : {
        "key" : "C8xSIBPKRTcXxFix",
        "sig" : "htQvu861MyGldtDEaDfOBydlijO_FaZYnpgXuvgcO0H5HNCIkQ_VIOWKZL9peZ19PjPjvpWtQbZEOvNh05o6Bw"
    }
}
```
Success response code: `200`  
Content-Type: `application/json`  
Success response body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `token_type` | String | required | Fixed text string `device_token` |
| `device_token` | String | required | The device token registered by the server |

Example:
```json
{
    "token_type" : "device_token",
    "device_token" : "MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3"
}
```
Failure response code: `403`  
This operation will invalidate all device tokens previously granted for the same `device_id`. A server can choose to
limit the lifetime of a device token or a user can choose to invalidate a device token (lost device). In either case,
clients must be prepared to re-register once their device token becomes invalid.  
The device token grants access to the profile and needs to be stored by clients in a secure location.

### 2.2 Access Tokens
Device Tokens need to be exchanged into short living access tokens before being used on API endpoints.  
Endpoint: `<baseUri>/auth/access_token`  
Method: `POST`  
Content-Type: `application/json`  
Body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `device_token` | String | required | Device token registered by the server |
| `timestamp` | timestamp | required | Timestamp when the client created this request |

The access token request must always be signed by the profile owner as specified by SPXP.

Example:
```json
{
    "device_token" : "MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3",
    "timestamp" : "2020-01-15T10:39:17.164",
    "signature" : {
        "key" : "C8xSIBPKRTcXxFix",
        "sig" : "sfdzUrSIjZA2ZTFAQSO8NHqDz1LlhAbRbcmiOlIcEpz9ezlNoxYAhYHIctCpa4eG3NGaVGruMVVzQE0Y6ez6BQ"
    }
}
```
Success response code: `200`  
Content-Type: `application/json`  
Success response body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `token_type` | String | required | Fixed text string `access_token` |
| `access_token` | String | required | The access token issued by the server |
| `expires_in` | Integer | required | Lifetime in seconds of the access token |

Example:
```json
{
    "token_type" : "access_token",
    "access_token" : "jT4k9ZgkRwHIctzlNoxYAhY4eG3NCpaVVzQEGaVGruM0Y6ez6BQ",
    "expires_in" : 3600
}
```
Failure response code: `403`

### 2.3 API Authentication
Every API request listed here, except for authentication requests, must be authorised by sending the `Authorization`
http request header field containing the Access Token using the `Bearer` authentication scheme:
```
Authorization: Bearer <access_token>
```
If the access token is invalid, the server responds with a `401` status code. Clients need to refresh the access token
in this case or re-register the device.

## 3 Service info
The server implementation is providing the backend behind the various endpoints listed in the profile root document.
Most likely, this implementation has some constraints on the endpoint URIs so that they cannot be freely chosen by the
profile owner. The service info API provides the information needed by the profile owner to craft a profile root
document. It further exposes information about the capabilities of this implementation.  
Endpoint: `<baseUri>/service/info`  
Method: `GET`  
Success response code: `200`  
Content-Type: `application/json`  
Success response body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `server` | Object | required | Information about the server as defined below |
| `server.vendor` | String | optional | Vendor of the server product |
| `server.product` | String | required | Product name of the server product |
| `server.version` | String | optional | Version string of the server product |
| `server.helpUri` | String | optional | Absolute URI of user facing help pages of the server product |
| `endpoints` | Object | required | Collection of endpoint information to be used for the profile root document and in the connection process as defined below |
| `endpoints.friendsEndpoint` | String | optional | `friendsEndpoint` to be used in the profile root document |
| `endpoints.postsEndpoint` | String | optional | `postsEndpoint` to be used in the profile root document |
| `endpoints.keysEndpoint` | String | optional | `keysEndpoint` to be used in the profile root document |
| `endpoints.connectEndpoint` | String | optional | `connectEndpoint` to be used in the profile root document |
| `endpoints.connectResponseEndpoint` | String | optional | `responseEndpoint` to be used in connection requests |
| `limits` | Object | required | Information about service limits as defined below |
| `limits.maxMediaSize` | Integer | optional | Maximum size in bytes of media objects accepted by this server |

Example:
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
        "connectResponseEndpoint" : "connectResponse/alice"
    },
    "limits" : {
        "maxMediaSize" : 10485760
    }
}
```
Failure response code: `4xx` or `5xx`

## 4 Service messages
There are situations where a server needs to communicate with the profile owner, for example to inform the owner about
a new service agreement that needs to be accepted or to relay connection requests received from other profiles.  
These  messages use a similar approach as posts in SPXP.

### 4.1 Reading messages
We use the same basic approach and paging mechanism as in SPXP.  
Endpoint: `<baseUri>/service/messages`  
Method: `GET`  
Success response code: `200`  
Content-Type: `application/json`  
Success response body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `data` | Array | required | List of service message objects |
| `more` | Boolean | required | True if and only if there are more service messages before the oldest post in the `data` array |

Each single service message in the data array is a JSON object with these members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `seqts` | timestamp | required | Sequence timestamp of service message generated by the server |
| `type` | String | required | Type of service message. One of `provider_message`, `connection_request` or `connection_package` |

Depending on the `type`, additional members are defined as follows:

#### Type `provider_message`:
| Name | Type | Mandatory | Description |
|---|---|---|---|
| `message` | String | required | Text message to be displayed to the user |
| `link` | String | optional | Absolute URI of a link to be displayed to the user |

#### Type `connection_request`
| Name | Type | Mandatory | Description |
|---|---|---|---|
| `received` | Timestamp | required | Timestamp when the server received the connection request |
| `ver` | String | required | `ver` field as received on the connect endpoint |
| `msg` | Object | required | `msg` field as received on the connect endpoint |

#### Type `connection_package`
| Name | Type | Mandatory | Description |
|---|---|---|---|
| `received` | Timestamp | required | Timestamp of the package exchange |
| `ver` | String | required | `ver` field as received on the package exchange endpoint |
| `package` | Object | required | `package` field as received on the package exchange endpoint |

Supported query parameters:

| Name | Type | Description |
|---|---|---|
| `max` | Integer | Maximum number of items the client can handle in one response. The server can return fewer items, but must not return more items. |
| `before` | timestamp | Only include items with a timestamp before this date |
| `after` | timestamp | Only include items with a timestamp after this date |

Example request: `<baseUri>/service/messages?max=5&after=2018-09-17T14:04:27.373&before=2018-09-19T15:45:37.735`  

Example:
```json
{
    "data" : [
        {
            "seqts" : "2018-09-17T14:04:27.373",
            "type" : "provider_message",
            "message" : "Hello, world!",
            "link" : "https://example.com"
        }, {
            "seqts" : "2018-09-15T12:35:47.735",
            "type" : "connection_request",
            "received" : "2018-09-15T12:35:46.123",
            "ver" : "0.3",
            "msg" : {
                "..." : "..."
            }
        }, {
            "seqts" : "2018-09-13T02:53:12.154",
            "type" : "connection_package",
            "received" : "2018-09-13T02:53:11.856",
            "ver" : "0.3",
            "establishId" : "",
            "package" : {
                "..." : "..."
            }
        }
    ],
    "more" : true
}
```
Failure response code: `4xx` or `5xx`  

### 4.2 Cleaning up messages
Messages are hold by the server until they are cleaned up. Clients can decide if they delete messages on the server
immediately after having received them, or only after having shown them to the user.  
Endpoint: `<baseUri>/service/messages/<seqts>`  
Parameter: `<seqts>` value of the `seqts` field in the service message object to delete  
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

## 5 Publishing profile objects
To publish new data for the profile root document or the “friends endpoint“, the client simply PUTs the JSON objects
to the server, including all private data. There is no possibility yet to selectively update individual elements in the
private array or the main object.  
The server does not need to validate any signatures or availability of decryption keys.  
Endpoint: `<baseUri>/profile/root`  
Endpoint: `<baseUri>/profile/friends`  
Method: `PUT`  
Content-Type: `application/json`  
Body: the entire JSON object as specified in the SPXP specification  
Success response: `201` or `204` without body  
Failure response code: `4xx` or `5xx`  

## 6 Posts
Clients have to use the normal “posts endpoint” in SPXP to enumerate all posts. This requires the profile owner to have
a reader key (or set of keys) that is able to read all posts.

### 6.1 Publishing
To pubish a new post, the client POSTs the entire post object to the server.  
The server does not need to validate any signatures or availability of decryption keys.  
Endpoint: `<baseUri>/posts`  
Method: `POST`  
Content-Type: `application/json`  
Body: the entire JSON object of one single post object as specified in the SPXP specification  
Example:
```json
{
    "createts" : "2018-09-16T12:23:18.751",
    "type" : "text",
    "message" : "Hello, world!",
    "signature" : {
        "key" : "C8xSIBPKRTcXxFix",
        "sig" : "bDOgcT4uxTKYMTuOJXDbAPc1UA2p-aGdxwplUWNStzyDRIRPu9UxaTU1IoZ1ELjBY5iRf4FEBPV09Uw9TOYuCA"
    }
}
```

Success response code: `200`  
Content-Type: `application/json`  
Success response body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `seqts` | String | required | The `seqts` assigned to this post by the server |

Example:
```json
{
    "seqts" : "2018-09-17T14:04:27.373"
}
```
The server will assign a unique seqts, store the post and return the assigned seqts.

Failure response code: `4xx` or `5xx`

### 6.2 Deleting
Post objects can be removed with a DELETE HTTP request using the unique seqts of the post object.  
Endpoint: `<baseUri>/posts/<seqts>`  
Parameter: `<seqts>` value of the `seqts` field in the post object to delete
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

## 7 Media
Profiles can include links to other media files, like images and videos, in their posts and the profile. These media
files can also be managed through this extension.  
Media files are handled as opaque binary files by this protocol, uniquely identified by a media ID.

### 7.1 Uploading media
Binary files can be POSTed directly to the server as `multipart/form-data`.  
Endpoint: `<baseUri>/media`  
Method: `POST`  
Content-Type: `multipart/form-data`  
Form Data:

| Field | Content |
|---|---|
| source | binary media as `application/octet-stream`, `images/jpeg` or `images/png` |

Success response code: `200`  
Content-Type: `application/json`  
Success response body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `mediaid` | String | required | The ID of this media object used to address this object with this API |
| `uri` | String | required | Absolute URI of this media object (public accessible) |

Example:
```json
{
    "mediaId" : "3d3af028-8c61-4cdc-8b96-6012a85faa99",
    "uri" : "https://cdn.example.com/3d3af028-8c61-4cdc-8b96-6012a85faa99"
}
```
Failure response code: `4xx` or `5xx`

### 7.2 Deleting media
Endpoint: `<baseUri>/media/<mediaId>`  
Parameter: `<mediaId>`  ID of media object to be deleted as returned by the upload media endpoint  
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

## 8 Key Management
Maintaining the tree of keys and ensuring that actors have access to all the content they are entitled for is the sole
responsibility of the profile owner. This management extension only provides endpoints to publish new keys to the server
and to delete existing keys. The server will maintain this set of keys and use it to compute the result set on the “keys
endpoint” as well as to select the elements exposed as `private` data in objects.

### 8.1 Publishing keys
The client transfers a set of keys to the server. Each key is processed independently, and the outcome of each operation
is reported individually. A ”Success” response code only means that the entire request has been processed, but it does
not indicate that any of these operations has been successful. The request is structured similar to the response on the
“keys endpoint” in SPXP.  
Endpoint: `<baseUri>/keys`  
Method: `POST`  
Content-Type: `application/json`  
Request body: The same 3 level JSON structure as specified for the keys endpoint in SPXP  
Example:
```json
{
    "audience1" : {
        "group1" : {
            "round1" : "<JWK1>",
            "round2" : "<JWK>",
            "round3" : "<JWK>"
        },
        "group2" : {
            "round1" : "<JWK>",
            "round2" : "<JWK>",
            "round3" : "<JWK>"
        }
    },
    "audience2" : {
    }
}
```
Success response code: `200`  
Content-Type: `application/json`  
Success body:
```json
{
    "audience1" : {
        "group1" : {
            "round1" : "<outcome>",
            "round2" : "<outcome>",
            "round3" : "<outcome>"
        },
        "group2" : {
            "round1" : "<outcome>",
            "round2" : "<outcome>",
            "round3" : "<outcome>"
        }
    },
    "audience2" : {
    }
}
```
With `<outcome>` one of `ok`, `err_exists`, `err_invalid_jwk`, `err_invalid_jwk: <description>`, `err_retry` or `error: <description>`    
Failure response code: `4xx` or `5xx`  

### 8.2 Removing keys
Keys can be deleted individually or in groups. The server does not perform any cascading delete. If a key can no longer
be decrypted, it is still hold on the server. It is possible that a client is going to publish new decryption keys in a
later operation.  
Endpoint: `<baseUri>/keys/<audience>/<group>/<round>`  
Endpoint: `<baseUri>/keys/<audience>/<group>`  
Endpoint: `<baseUri>/keys/<audience>`  
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

### 8.3 Managing keys in prepared packages
To add or remove keys that are associated with a prepared package, the client can add the `establishId` of the prepared
package as suffix to the audience, seperated by the `@` character.
See [9.3](#93-updating-keys associated-with-a-prepared-package) for details.

## 9 Connection packages
When a peer profile accepts a connection request, the server has to perform the package exchange on behalf of the user.
In this process, the server has to activate a set of keys so that the peer profile can start to read private
information, accept and store  the package provided by the peer profile and then hand out the connection package that
has been prepared for the peer. Please see the main SPXP specification for details on this process.  
The profile management extension defines the following operations to manage this process.

### 9.1 Preparing a package
The server needs to be prepared for the connection package exchange with other profiles.  
Endpoint: `<baseUri>/connect/packages`  
Method: `POST`  
Content-Type: `application/json`  
Body:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `establishId` | String | required | The establish ID as defined in the connection process in SPXP |
| `expires` | timestamp | required | Timestamp after which the server must no longer accept package exchanges |
| `package` | Object | required | The connection package to be exchanged on the connection response endpoint for this establish ID |
| `keys` | Object | required | set of keys to be activated on the package exchange. Same object structure as on the publish keys endpoint. |

Example:
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
        "audience2" : {
        }
    }
}
```
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

### 9.2 Revoke a prepared package
The client can revoke a prepared connection package based on the establishId.  
Endpoint: `<baseUri>/connect/packages/<establishId>`  
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`

### 9.3 Updating keys associated with a prepared package
It is possible that the client needs to extend or even replace cryptographic material associated with a connection
process. If either the reader key or certificate are effected, the client needs to revoke the entire package and issue
a new one with the same establishId.  
If the change is limited to the round keys that are associated with a connection, which is most likely, then the client
can use the normal [key management](#8-key-management) operations using this special audience:  
`<audince>'@'<establishId>`  
While a connection request ages, it is likely that new round keys need to be generated in the assigned group. In this
case, these new round keys need to be added to the prepared package.  
Example body on the keys endpoint:
```json
{
    "audience1@establish1" : {
        "group1" : {
            "round1" : "<JWK1>",
            "round2" : "<JWK>",
            "round3" : "<JWK>"
        }
    },
    "audience2" : {
    }
}
```