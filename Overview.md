# Social Profile Exchange Protocol (SPXP)

## Mission statement
Create the world’s first truly open social network.

## Motivation
Social media and social networks have just shown a tiny fraction of their true potential yet. They are controlled by
single entities, solely focusing on strengthening their control over the user base and monetizing their insight into
data owned by users.  
A social network compromised of multiple independent actors, just like the World Wide Web, can unleash new applications
nobody has even been dreaming of yet.

## Background
The first widely used computer networks in the late 1970s have been established around a central provider. Data, like
emails, has been organized and stored by this central entity. Interactions between users have then been dispatched via
this central hub.  
All of this changed with the rise of the Internet in the early 1990s. Instead of establishing centralized service
providers, engineers just agreed on protocols that define how client applications communicate with their server
counterparts. The POP3 and SMTP protocol family for example defines how email clients exchange messages with mail
servers. This enabled multiple companies and non-profit organizations to step up and provide a huge variety of
implementations. All actors are free to choose any available product on the market and conjointly establish a
communication network. There is no single entity that runs the email service or the World Wide Web.  
Social media networks as we know them today are again organized and controlled by a single central entity. This had
clear advantages over a de-centralized distributed network given the commodity technology of the mid-2000s. But the
hardware available to average users has evolved since. With the broad availability of high performance smartphones,
establishing a social media network of multiple independent actors now became feasible.  
However, no one has yet proposed a protocol for a de-centralized distributed social media network.

## Approach
The social profile exchange protocol (SPXP) defines how client applications can retrieve information from social profile
servers and contribute data to them. Under this protocol, each social profile is uniquely identified by a cryptographic
key pair and addressed by a URL.  
A consuming user runs a SPXP client application and configures it with the URLs of social profiles to follow. This
client application then polls the SPXP servers of these profile URLs for updates and presents them to the user. Since
all the filtering, curation and presentation of the social profiles is performed by the client application, the user has
full control over the view of the social network.  
Social profiles are published by SPXP servers. Since client applications can poll servers for updates at any time, it is
required that SPXP profile servers are always on. Users who wish to publish a social profile can decide to either run
such a server themselves or to use a service provider who runs the server infrastructure for them.  
Cryptographic keys are only stored in the SPXP clients. Updates to social profiles are prepared in the client
application, encrypted and digitally signed, and then sent to the server that hosts the profile. Full end-to-end
encryption of non-public information guarantees that not even the profile server can access private information.  
SPXP is based on existing technology, so that simple text files on a web server already constitute a valid social
profile. All cryptographic mechanisms are based on well standardized protocols and well understood algorithms.  
Two social profiles can connect to each other via a cryptographic key exchange. Connected profiles give each other
access to private information and grant mutual contribution permissions (e.g. commenting).  

## Basic protocol details
SPXP is based on a web API, transferring data as JSON objects and using JOSE for encryption.  
Each social profile is uniquely identified by a _profile URL_. There are no additional restrictions or requirements
imposed on this URL. The profile server then resolves this URL against the profile root document.  
```
GET https://example.com/spxp/alice
200 OK
{
    "ver" : "0.1",
    "name" : "Crypto Alice",
    "about" : "I love cryptography.",
    "gender" : "female",
    "website" : "https://en.wikipedia.org/wiki/Alice_and_Bob",
    "email" : "cryptoalice@example.com",
    "birthDayAndMonth" : "18-04",
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
This profile root document contains basic information about this profile, like the display name, an about line or the
email address. The members hometown and location contain profile URLs themselves and are an example of how social
profiles can be linked together.  
In addition to the basic profile information, the profile root document also specifies optional endpoints that deliver
additional information. The members `profilePhoto`, `friendsEndpoint` or `postsEndpoint` contain an absolute or a
relative URL of additional endpoints.  
The relative URL “friends/alice” in this example would be resolved relative to the profile URL to
“https://example.com/spxp/friends/alice”.

## Time series data
The posts endpoint of a social profile returns the most recently published posts of this profile.  
Example:  
```
GET https://example.com/spxp/posts?profile=alice
200 OK
{
    "data" : [
        {
            "timestampUTC" : "2018-09-17T14:04:27.373",
            "type" : "text",
            "message" : "Hello, world!"
        }, {
            "timestampUTC" : "2018-09-16T13:35:47.735",
            "type" : "photo",
            "message" : "Look at this",
            "full" : "https://example.com/full-image.jpeg",
            "small" : " https://example.com/small-image.jpeg "
        }
    ],
    "more" : true
}
```
This endpoint supports additional query parameters that can be used to page through the result set or to limit the
response to a specific time window.

## Limited audience
Social profiles can decide to publish selected information only to a limited audience. This is achieved by encrypting
data according to the JSON Web Encryption (JWE) standard combined with an effective key management.  
For example, Alice could provide her social profile like this:  
```
{
    "ver" : "0.1",
    "name" : "Crypto Alice",
    "private" : [
        "eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0...",
        "eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0...",
        {
            "ciphertext": "M-TQPSbJBB...",
            "protected": "eyJlbmMiOiJBMjU2R0NNIn0",
            "aad": "MjAxOS0wNy0wNlQwNzo1MzozMi45NDQ",
            "recipients": [{
                "encrypted_key": "MhgFV...",
                "header": {
                    "kid": "grp...",
                    "tag": "VOHdxWwA1TZNk1KVUGED-g",
                    "iv": "_AyUcYoAf_gVKygs"
                }
            }],
            "unprotected": {"alg": "A256GCMKW"},
            "tag": "wu7aqATu0XpqfnDGyu-OgQ",
            "iv": "4ldURKoUnRiZiIRS"
        }
    ],
    "profilePhoto" : " https://images.example.com/alice.jpg",
    "friendsEndpoint" : "friends/alice",
    "postsEndpoint" : "posts?profile=alice"
}
```
The `private` array can contain encrypted content in either JWE Compact or JWE JSON serialization. The latter requires
more space but can address multiple recipients at once.  
All actors who can obtain the secret key with the given key id are able to decrypt the ciphertext. The decrypted
plaintext then contains again a JSON object:  
```
{
    "about" : "I love cryptography.",
    "gender" : "female",
    "website" : "https://en.wikipedia.org/wiki/Alice_and_Bob",
    "email" : "cryptoalice@example.com"
}
```
SPXP clients need to merge the decrypted objects into the profile root document before interpreting its content.

## Key management
Multiple protocols have been published for the secure transmission of messages between individuals or within groups,
like Off-the-Record Messaging (OTR), Silent Circle Instant Messaging Protocol (SCIMP) or the Signal Protocol which
introduced the Double Ratchet Algorithm. All these protocols have been designed to have specific cryptographic
properties, like forward secrecy or plausible deniability, to serve the requirements of instant messaging.  
For a network of social profiles however, we have completely different conditions in which this protocol has to operate
as well as different requirements. Some of these requirements are even the exact opposite of those desirable for instant
messaging.  
With social profiles, we need to be able to address a massive audience of possibly millions of recipients. When adding a
new recipient, we need to give this new recipient immediate access to all information that has been published in the
past – which is in contrast to forward secrecy. As soon as a recipient is removed, we need to guarantee that no
information published from now on can be decrypted by this recipient. We cannot guarantee that this recipient does no
longer have access to data published in the past, simply because the SPXP client could have already received, decrypted
and stored this information locally. But at least, we need to prevent that this recipient is able to get additional data
from the server. Further, social profiles should have the possibility to specify the audience for each piece of
information. All of these requirements must be met while preventing even the SPXP servers from learning anything about
the profile, its peers or the published data.  
With SPXP, a profile can define multiple publishing groups. Individual recipients can then be assigned to any number of
these publishing groups, and publishing groups can also be contained in each other. Every piece of private information
published via SPXP can address one or multiple of these publishing groups.  
A social profile could for example define the three groups “Friends”, “Close Friends” and “Family”. The groups “Close
Friends” and “Family” are both contained in the group “Friends”. Now let Alice be in the group “Friends”, Bob in the
group “Close Friends” and Charlie in the group “Family”. Any post published for the audience “Friends” can be seen by
all three, while posts for the audience “Close Friends” can only be seen by Bob. Posts that target the audience “Close
Friends” and “Family” can then be seen by Bob and Charlie.  
Cryptographic keys assigned to these groups are replaced regularly, forming a stream of round keys. Whenever a member
leaves a group, a key rollover is required so that the former recipient is no longer able to decrypt newly published
data. To reduce the amount of cryptographic keys that need to be published on key-rollover events, the social profile
can chose to partition the entire audience of one publishing group into smaller sub-groups forming a well-balanced
tree.  
SPXP is flexible enough to let the client chose any structure of these groups while still giving hints for an optimal
group balance. 

## Information authenticity and public key infrastructure
Each profile is uniquely identified by an asymmetric cryptographic key pair. All data that is digitally signed by the
profile’s private key is considered authentic, no matter where or how it got published. Profiles can act as a
certificate authority in a public key infrastructure and authorize other key pairs to publish data on this profile. The
scope of this grant can be limited to a specific audience or limit the kind of information that can be published.  
This can be used to authorize devices, applications or other persons to publish information on behalf of and in the name
of the profile owner. It can also be used to authorize other profiles to publish information in their name on this
profile, for example to publish comments and reactions to the profile’s posts.

## Connections
Two profiles can be mutually connected. When Alice wants to initiate a connection with Bob, she instructs her SPXP
client to generate a connection request and send it to Bob’s SPXP server. Bob’s SPXP client checks his own SPXP server
regularly and presents incoming connection requests to Bob. When Bob accepts the request, his client forms a connection
response and sends it back to Alice’s server. In this process, both also select a group to which the other party
belongs. This can be different on both sides. While Alice assigns Bob to the group “Friends” in her profile, Bob could
assign Alice to the group “Work colleagues” on his profile.  
SPXP uses a key agreement scheme that guarantees that no sensitive information is hold anywhere outside of the clients
(full end to end encryption).  
Profile connections essentially combine the limited audience scheme with the publishing grant scheme. Alice does not
just give Bob access to information she has published to her “Friends” audience, she also grants him the right to
publish comments on posts she published to this audience.

## Validated profiles
SPXP intentionally does not define any kind of authority which could verify profiles. So anybody could setup a new
profile, set the name to “John Doe” and publish it somewhere.  
Trust that a specific profile is actually controlled by a particular real life person, organization, corporation or
other entity is established via one of these methods:
##### Trusted URL:
A profile published by a URL can be bound to the domain name used in this URL via a TXT entry in the DNS. This
essentially delegates trust to the DNS.  
This method is useful for all entities that host their own website and want to associate profiles with it.
##### Cross signing:
Social profiles can digitally sign the public profile key of other profiles with their own private key and publish this
certificate as part of their own profile. This should only be done after verifying the authenticity of this profile
outside of SPXP, e.g. by a personal interaction with mobile devices in real life or in a video chat.  
Let’s assume Alice wants to connect with her real life friend Dave and she has discovered a profile at
https://example.com/spxp/Dave. She has already established connections to her other real life friends Bob and Charlie
and personally verified these in real life. She can now trust her friends who certify that the profile behind this URL
is actually their real life friend Dave.  
This trust scheme is based on the fact that two real life friends typically have overlapping groups of social contacts.

## Neighbourhood Assistance
These overlapping social contacts can also be used for a digital version of Neighbourhood Assistance. A profile can ask
connected friends to store and / or publish a small piece of information for them. This mechanism can be used for
various use cases, like
##### Profile relocation
Since social profiles are uniquely identified by their cryptographic key pair, they can be relocated to other URLs. But
references to the old URL can still exist, especially offline, for example printed as QR code. If at least one profile
of your social contacts is connected to this profile, you can get the new profile URL via your connected friend.
##### Profile key revocation and replacement
There can be situations where a profile key gets compromised. Shared contacts can help to announce this key revocation
and also to re-establish trust in a new profile key.

## Regain control over a profile after key loss
A common problem with end to end encrypted communication schemes is the secure backup and restauration of cryptographic
material. In the case of a loss of subscriber keys, the user has to start from scratch and re-establish all connections
with communication partners. To avoid this, some applications require users to perform regular backups. But this
requires trust in the provider of the backup location.  
Other applications bind identities to secondary communication channels like email or phone numbers. But this enables
attackers to take over the identity if they are able to get hold of these communication channels. In some communication
schemes, perfect forward secrecy (PFS) protects at least the past communication from being disclosed in this case.  
With SPXP, it is possible to use the “social fingerprint” to regain control over the own social profile after the full
loss of cryptographic material. To prepare this recovery mechanism, the SPXP client deposits small data packets at
connected profiles. In the case of a disaster, it is possible to setup a new SPXP client from scratch and to perform a
personal authentication with a predefined number of your friends. During these 1-to-1 handshakes in real life,
cryptographic data is exchanged that enables the SPXP client to re-gain control over the lost profile key pair.  
This method bares the risk that the required quorum of your connected profiles team up to conjointly take over your
profile. So it requires a thoughtful selection of trusted friends and quorum.

## Quid pro Quo
The publishing mechanisms in SPXP are explicitly designed to expose as little information as possible. A reader who
belongs to one of the publishing groups of a profile is only able to decrypt information published to this audience. But
it is not possible to gain any information about other readers, the group structure or even the existence of other
information beyond this publishing group. And a cryptographic key pair that got granted publishing rights does not get
any information about the audience it is publishing to.  
All of this is the result of design decisions that intend to give profiles fine grained control over published
information and to limit exposed information exactly to what the profile intends to publish.  
But there are situations, especially with social networks, where you want to share information on a quid pro quo basis.
For example, if you publish a comment on a profiles post, then you should be aware of the audience which is able to see
this comment.  
NOTE:  
This aspect of SPXP is currently up to debate and it is not specified yet how it materializes in the protocol.

## Protocol versions and status
While the vision of SPXP evolves, new versions of this protocol are specified. These are then used to test this protocol
in real applications and to put it under performance tests on real devices.  
During this feedback cycle, it is possible that there is no protocol specification that fully covers the current vision
and goals of SPXP.

| Version | Objective |
|---|---|
| [0.1](./SpxpProfileSpec-V01.md) | Investigates how data of social profiles can be efficiently encoded in JSON |
| [0.2](./SpxpProfileSpec-V02.md) | Adds JWE based encryption and investigates efficient key distribution and management |
| [0.3](./SpxpProfileSpec-V03.md) | Changes cryptographic algorithms, adds signing, specifies profile connections (in progress) |
| 0.4 | Adds Neighbourhood Assistance, handling of profile relocation, handling of key loss, guaranteed quid pro quo (planned) |

See SPXP protocol specification for a list of changes.
