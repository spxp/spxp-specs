# CR-001 - Profile Relocation
Draft - Target SPXP 0.4

It has always been an early goal of this protocol to avoid user lock-in on specific applications or providers. The
protocol intentionally gives the profile key precedence over the URI [(see 1.3)](../SPXP-Spec.md#13-unique-profile-identification)
to bind a profile to the cryptographic key rather than the URI. This degrades the profile provider to a mere publishing
service which can be swapped for a different provider.

However, the current version 0.3 of SPXP does not provide a guided migration process or give enough hints for
implementors how to discover the new profile URI of a relocated profile.

## Proposed changes

### In section "5 Social profile root document"
Add the following members:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `timestamp` | timestamp | optional | Timestamp helping clients identifying the most recent version of a profile. Required if `profileLocation` is present. |
| `profileLocation` | String | optional | _Absolute URI_ as defined in [RFC 3986 Section 4.3](https://tools.ietf.org/html/rfc3986#section-4.3) constituting the new profileURI if the profile has been relocated as specified in [chapter 15](#15-profile-relocation) |

Add the following notice:
Clients should track the `timestamp` of a profile and discard information older than the state recorded by the client.

### Insert new section before "Profile Extensions"

NEW PROFILE LOCATION MUST ANNOUNCE ITS OWN URI

#### 15 Profile Relocation
A profile can be transferred to a new profile URI while maintaining its profile key pair. Clients can validate this
transfer by checking the profile's signature on the new profile URI and update their internal state. If the previous
service provider is cooperative, it can be used to announce the new profile location by updating the profile root
document with the new `profileLocation` as specified in [15.1](#151-profile-location-reference).  
Connected peer profiles which discover such a profile relocation must update all references to this profile published
via the "friends endpoint" ([9](../SPXP-Spec.md#9-friends-endpoint)) using the new profile URI and must only use this
new profile URI for any references used in posts or anywhere else in this protocol from now on.  
If a profile is no longer accessible, or the client has reason to believe that a profile URI delivers stale information,
it should use profile references published by other profiles, e.g. via their friends endpoint, to discover the new
profile URI.
It is possible that a malicious actor is copying the profile root document to a different URI and trying to trick
clients into switching over to this URI. This can be used to prevent clients from receiving updates from the original
profile. To prevent this, it is important that clients 
The timestamp in the profile root document helps clients to identify the most recent profile location in
case a profile has been relocated multiple times.  
If a client discovers a signed post by a profile on a peer profile with a different URI in the profile reference and a
more recent creation date as the latest profile timestamp on record, then this is a good reason to believe that the
known profile URI delivers stale information.

##### 15.1 Profile Relocation Reference
If a profile has been relocated to a different profile URI, it provides a new `profileLocation` as part of the profile
root document. If this field is present, a `timestamp` on the profile root document is required. If this timestamp is
more recent than the profile state stored in the client, the client has to update the profile URI and start using this
new URI from now on.