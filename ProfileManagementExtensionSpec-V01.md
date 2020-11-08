# SPXP - Profile Management Extension
Version 0.1

## Base URI
A management server can manage profiles with various profile URIs, not just profiles on the same domain. To give the
implementors flexibility on the layout of the URI hierarchy on this domain, this spec defines the API relative to
a base URI.

## Authentication
This API uses an oauth like authentication scheme for now.

### Device Registration
Each device is granted a long living device token. Each device can choose a random unique device ID.  
Endpoint: `<baseUri>/auth/device`  
Method: `POST`  
Content-Type: `application/json`  
Body:
```json
{
    "profile_uri" : "",
    "device_id" : "",
    "timestamp" : "",
    "signature" : {
        "key" : "",
        "sig" : ""
    }
}
```
Success response code: `200`  
Content-Type: `application/json`  
Success body:
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

### Access Tokens
Device Tokens need to be exchanged into short living Access Tokens before being used on API endpoints.  
Endpoint: `<baseUri>/auth/access_token`  
Method: `POST`  
Content-Type: `application/json`  
Body:
```json
{
    "device_token" : "",
    "timestamp" : "",
    "signature" : {
        "key" : "",
        "sig" : ""
    }
}
```
Success response code: `200`  
Content-Type: `application/json`  
Success body:
```json
{
  "token_type":"access_token",
  "access_token":"MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3",
  "expires_in":3600
}
```
Failure response code: `403`

### API Authentication
Every API request listed here, except for authentication requests, must be authorised by sending the `Authorization`
http request header field containing the Access Token using the `Bearer` authentication scheme:
```
Authorization: Bearer <access_token>
```
If the access token is invalid, the server responds with a `401` status code. Clients need to refresh the access token
in this case or re-register the device.

## Service info
The server implementation is providing the backend behind the various endpoints listed in the profile root document.
Most likely, this implementation has some constraints on the endpoint URIs so that they cannot be freely chosen by the
profile owner. The service info API provides the information needed by the profile owner to craft a profile root
document. It further exposes information about the capabilities of this implementation.  
Endpoint: `<baseUri>/service/info`  
Method: `GET`  
Success response code: `200`  
Content-Type: `application/json`  
Success body:
```json
{
  "server" : {
    "vendor" : "",
    "product" : "",
    "version" : "",
    "helpUri" : ""
  },
  "endpoints" : {
    "friendsEndpoint" : "",
    "postsEndpoint" : "",
    "keysEndpoint" : "",
    "publishEndpoint" : "",
    "connectEndpoint" : "",
    "connectResponseEndpoint" : ""
  },
  "limits" : {
    "maxMediaSize" : 10485760
  }
}
```
Failure response code: `4xx` or `5xx`

## Service messages
There are situations where a server needs to communicate with the profile owner, for example to inform the owner about
a new service agreement that needs to be accepted or to relay connection requests received from other profiles.  
These  messages use a similar approach as posts in SPXP.

### Reading messages
We use the same basic approach and paging mechanism as in SPXP.  
Endpoint: `<baseUri>/service/messages?max=###&before=<timestamp>&after=<timestamp>`  
Method: `GET`  
Success response code: `200`  
Content-Type: `application/json`  
Success body:
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
              ...
            }
        }, {
            "seqts" : "2018-09-13T02:53:12.154",
            "type" : "connection_package",
            "received" : "2018-09-13T02:53:11.856",
            "ver" : "0.3",
            "establishId" : "",
            "package" : {
              ...
            }
        }
    ],
    "more" : true
}
```
Failure response code: `4xx` or `5xx`  

Possible message types and additional fields:  

#### Type `provider_message`
`message` : required text message to be displayed  
`link` : optional link  

#### Type `connection_request`
`received` : timestamp when the server received the cr  
`ver` : `ver` field as received on the connect endpoint  
`msg` : `msg` field as received on the connect endpoint  

#### Type `connection_package`
`received` : timestamp of the package exchange  
`ver` : `ver` field as received on the package exchange endpoint  
`package` : `package` field as received on the package exchange endpoint  

### Cleaning up messages
Messages are hold by the server until they are cleaned up. Clients can decide if they delete messages on the server
immediately after having received them, or only after having shown them to the user.  
Endpoint: `<baseUri>/service/messages/<seqts>`  
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

## Publishing profile objects
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

## Posts
Clients have to use the normal “posts endpoint” in SPXP to enumerate all posts. This requires the profile owner to have
a reader key (or set of keys) that is able to read all posts.

### Publishing
To pubish a new post, the client POSTs the entire post object to the server.  
The server does not need to validate any signatures or availability of decryption keys.  
Endpoint: `<baseUri>/posts`  
Method: `POST`  
Content-Type: `application/json`  
Body: the entire JSON object of one single post object as specified in the SPXP specification  

Success response code: `200`  
Content-Type: `application/json`  
Body:
```json
{
  "seqts" : ""
}
```
The server will assign a unique seqts, store the post and return the assigned seqts.

Failure response code: `4xx` or `5xx`

### Deleting
Post objects can be removed with a DELETE HTTP request using the unique seqts of the post object.  
Endpoint: `<baseUri>/posts/<seqts>`  
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

### Missing
Possibility to associate posts with media files, so that server can provide a cascading delete

## Media
Profiles can include links to other media files, like images and videos, in their posts and the profile. These media
files can also be managed through this extension.  
Media files are handled as opaque binary files by this protocol, uniquely identified by a media ID.

### Uploading media
Binary files can be POSTed directly to the server as multipart/form-data.  
Endpoint: `<baseUri>/media`  
Method: `POST`  
Content-Type: `multipart/form-data`  
Form Data:

| Field | Content |
|---|---|
| source | binary media as `application/octet-stream` |

Success response code: `200`  
Content-Type: `application/json`  
Body:
```json
{
  "mediaId" : "",
  "uri" : ""
}
```
Failure response code: `4xx` or `5xx`

### Deleting media
Endpoint: `<baseUri>/media/<mediaId>`  
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

### Missing
Listing all media files, searching for media file based on URI, possibility to connect media files to posts.

## Key Management
Maintaining the tree of keys and ensuring that actors have access to all the content they are entitled for is the sole
responsibility of the profile owner. This management extension only provides endpoints to publish new keys to the server
and to delete existing keys. The server will maintain this set of keys and use it compute the result set on the “keys
endpoint” and to select the elements exposed as `private` data in objects.

### Publishing keys
The client transfers a set of keys to the server. Each key is processed independently, and the outcome of each operation
is reported individually. A ”Success” response code only means that the entire request has been processed, but it does
not indicate that any of these operations has been successful. The request is structured similar to the response on the
“keys endpoint” in SPXP.  
Endpoint: `<baseUri>/keys`  
Method: `POST`  
Content-Type: `application/json`  
Body:
```json
{
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

### Removing keys
Keys can be deleted individually or in groups. The server does not perform any cascading delete. If a key can no longer
be decrypted, it is still hold on the server. It is possible that a client is going to publish new decryption keys in a
later operation.  
Endpoint: `<baseUri>/keys/<audience>/<group>/<round>`  
Endpoint: `<baseUri>/keys/<audience>/<group>`  
Endpoint: `<baseUri>/keys/<audience>`  
Method: `DELETE`  
Success response code: `204`  
Failure response code: `4xx` or `5xx`  

## Connection packages
The server needs to be prepared for the connection package exchange with other profiles.  
Endpoint: `<baseUri>/connect/packages`  
Method: `POST`  
Content-Type: `application/json`  
Body:
```json
{
  "establishId" : "...",
  "expires" : "...",
  "package" : {
    ...
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
