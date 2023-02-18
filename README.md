# Social Profile Exchange Protocol

A protocol for a decentralised social network, focusing on privacy, security and individual sovereignty.

Designed after the principles of the world wide web, adding strong end-to-end cryptography to push
power from the network into endpoint devices.

If you want to play with it and see it in action ASAP, take a look at our [Quickstart](./Quickstart.md).

## How it works

Similar to the world wide web, a social profile is described by a simple text file in JSON format:

`https://spxp.org/spxp`
```json
{
    "ver": "0.3",
    "name": "SPXP.org",
    "shortInfo": "Social Profile Exchange Protocol",
    "about": "A protocol for a decentralised social network, focusing on privacy, security and individual sovereignty.",
    "website": "https://spxp.org",
    "profilePhoto": "spxp-profile-logo.png",
    "postsEndpoint": "spxp-posts"
}
```

To participate, use a client like the [HeyFolks app](https://heyfolks.app) to display these files and navigate the social graph:

![Our Profile](./assets/SpxpProfileApp.png)

As a **publisher**, you can eliminate the network between you and the reader. Nobody inbetween will alter your content or rate its importance.
Even more important, your content resides on your server under your control. You do not need to grant anybody a license for your content.

As a **reader**, you can participate immediately without setting up a profile - just like browsing the world wide web. You have fulll control
*what* you see, and *how* you want to consume it.

To guarantee **authenticity and sovereignty**, a publisher can optionally *sign* all information. The profile is then bound to the *public key*
rather than the URI, allowing the publisher to move to a different server or provider without friction.

To guarantee **privacy**, individual information can be *encrypted* allowing a profile to restrict individual bits of information to a limited audience. 

This cryptographic process is described in detail in [this video](https://www.youtube.com/watch?v=C0S0Oa4G1M4).

## Protocol Family

Latest version: [0.3](https://github.com/spxp/spxp-specs/releases/tag/v0.3)

[Social Profile Exchange Protocol - SPXP](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md)  ([wip](./SPXP-Spec.md))
The core protocol defining how clients can retrieve information from protocol servers, validate the information and
participate in the connection handshake.

[SPXP Profile Management Extension - SPXP-PME](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-PME-Spec.md)  ([wip](./SPXP-PME-Spec.md))
Protocol extension defining how clients can manage a protocol server which is hosting their own profile.

[SPXP Service Provider Extension - SPXP-SPE](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-SPE-Spec.md)  ([wip](./SPXP-SPE-Spec.md))
Extension defining a secure setup process for new profiles between a client and a service provider.

## SDK / Reference implementation
There are already numerous libraries out there for HTTP communication and JSON handling - the two main foundation
blocks of SPXP. We think providing an SDK that would depend on one of these would interfere too much with most
projects or developer preferences.
The situation however is different for the cryptographic operations in SPXP. The [spxp-crypto](https://github.com/spxp/spxp-crypto)
library provides a reference implementation in Java as well as a standard library that can be used directly in your
projects.

## Commandline tooling
For most operations, `curl` and `jq` are sufficient. For cryptographic operations, we provide the
[SpxpCryptoTool](https://github.com/spxp/spxp-crypto/blob/master/spxp-crypto-tools/README.md)
which can be used on the command line or from scripts.
With this tool, you can [explore the testbed profiles](https://github.com/spxp/spxp-crypto/blob/master/spxp-crypto-tools/ExploreTestbedProfiles.md)
from the command line.
