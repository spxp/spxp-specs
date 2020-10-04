# SPXP - Service Provider Extension
Version 0.1

## Background
Social Profiles are uniquely identified by their profile key pair, and are intentionally only loosely coupled to their
profile URI. This enables profiles to switch the provider used to publish them while keeping the profile and its
connections intact.  
However, a profile still needs at least one service provider to get published. This service provider also relays
messages between profiles on behalf of the profile owner.  
This specification defines a setup process that can be driven by an SPXP client to support users in easily setting up
a relationship with a service provider.
This specification has been defined to foster a culture of end-to-end encryption. A complex and cumbersome setup process
could encourage users to delegate the maintainance of encryption keys to the service provider.

## Service discovery
Every domain supporting the SPXP Service Provider Extension has to provide a service discovery document at a well-known
location. The server has to respond to the absolute path `/.well-known/spxp/spe-discovery` with a JSON object with these
members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `start` | String | required | _Absolute URI_ as defined in [RFC 3986 Section 4.3](https://tools.ietf.org/html/rfc3986#section-4.3) pointing to the initial web page |
| `bind` | String | required | _Absolute URI_ as defined in [RFC 3986 Section 4.3](https://tools.ietf.org/html/rfc3986#section-4.3) pointing to the endpoint used to bind the newly registered profile URI to a profile key |
| `managementEndpoint` | String | optional | _Absolute URI_ as defined in [RFC 3986 Section 4.3](https://tools.ietf.org/html/rfc3986#section-4.3) pointing to the Base URI of the Profile Management Extension |

Example:
```json
{
    "start" : "https://profiles.example.com/.spxp-spe/register",
    "bind" : "https://profiles.example.com/.spxp-spe/bind"
}
```

## Profile registration web flow
Once the user has chosen to register a new profile URI with this provider, the client needs attach a `return_scheme`
query parameter to the URI given as `start` member in the [service discovery](#service-discovery) response and open this
page in a new browser window. The client can chose to use any valid unique value as `return_scheme`.  
The service provider will then walk the user through a multi step process, in which it will choose a profile URI,
optionally validate the users identity, agree on terms of service and perform other operations of its choice. Once the
service provider is agreeing to publish the users profile, it navigates the browser to a URI constructed from the
initially given `return_scheme` and using a random one-time token as path.

## Profile Key Binding
After the user has registered with a service provider, the client needs to bind the public profile key to this account
and retrioeve the profile URI.  
The client sends a HTTP POST request to the URI given as `bind` member in the [service discovery](#service-discovery)
response. The HTTP body of this POST request contains a JSON object with these members:
          
| Name | Type | Mandatory | Description |
|---|---|---|---|
| `token` | String | required | The one-time token returned during profile registration |
| `publicKey` | Object | required | JSON object describing the public key of the profile's key pair as JWK defined in [RFC 7517 “JSON Web Key (JWK)”](https://tools.ietf.org/html/rfc7517) using the Ed25519 curve specifier as defined in [RFC 8037 Section 3.1](https://tools.ietf.org/html/rfc8037#section-3.1). <br/> Must have a unique, random key id (“kid”) |

Example:
```json
{
    "token" : "ksrguvarhgivnaowih5914z59fn",
    "publicKey" : {
        "kid" : "C8xSIBPKRTcXxFix",
        "kty" : "OKP",
        "crv" : "Ed25519",
        "x" : "skpRppgAopeYo9MWRdExl26rGA_z701tMoiuJ-jIjU8"
    }
}
```

If the operation succeeds, the server responds with a 200 status code, and a JSON object as body with these members:
          
| Name | Type | Mandatory | Description |
|---|---|---|---|
| `profileUri` | String | required | Registered profile URI as _absolute URI_ as defined in [RFC 3986 Section 4.3](https://tools.ietf.org/html/rfc3986#section-4.3) |

Example:
```json
{
    "profileUri" : "https://example.com/spxp/alice"
}
```