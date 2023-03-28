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

* **Social Profile Exchange Protocol - SPXP**  
  The core protocol defining how clients can retrieve information from protocol servers, validate the information and participate in the connection handshake.  
  Released version: [0.3](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md), working draft: [0.4](./SPXP-Spec.md)

* **SPXP Profile Management Extension - SPXP-PME**  
  Protocol extension defining how clients can manage a protocol server which is hosting their own profile.  
  Released version: [0.3](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-PME-Spec.md), working draft: [0.4](./SPXP-PME-Spec.md)

* **SPXP Service Provider Extension - SPXP-SPE**  
  Extension defining a secure setup process for new profiles between a client and a service provider.  
  Released version: [0.3](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-SPE-Spec.md), working draft: [0.4](./SPXP-SPE-Spec.md)

## Implementations
Server:
* [Wordpress Plugin](https://wordpress.org/plugins/hfa-spxp-support/) to expose your blog via SPXP (GPL license)
* A [Simple PHP Server](https://github.com/spxp/simple-php-server) supporting the entire protocol family (Apache license)
* and you can always create a profile manually and throw it on any web server

Clients:
* [HeyFolks app](https://heyfolks.app) a mobile client for iOS and Android (commercial license)
* [spxp-cli](https://github.com/spxp-space/spxp-cli) implemented as a single plain bash script to manage profiles (supports the [Service Provider](https://github.com/spxp/spxp-specs/blob/master/SPXP-SPE-Spec.md) and the [Profile Management Extension](https://github.com/spxp/spxp-specs/blob/master/SPXP-PME-Spec.md))
* and you can always use `curl` and `jq` manually

Service provider:
* [spxp.space](https://spxp.space) a commercial SPXP hosting provider supporting the entire protocol family
* and you can always set up your own hosting service using the [Simple PHP Server](https://github.com/spxp/simple-php-server) mentioned above

## Get your hands dirty and play with this protocol
If you want to see it in action and learn more about this protocol, here are some suggestions for next steps:

1. Take a look at our [Quickstart](./Quickstart.md) guide walking you through the protocol essentials and some of the steps below
2. Visit the [SPXP Bridge](https://bridge.spxp.org) and see how ActivityPub, AT Protocol and Nostr are translated to SPXP  
   (Best explored with Firefox as it renders json nicely)
3. If you want to see more sophisticated profiles using encryption and signing, take a look at some [testbed profiles](http://testbed.spxp.org/0.3/)  
   For example this [profile](http://testbed.spxp.org/0.3/heavyfrog799), it's [posts](http://testbed.spxp.org/0.3/posts/_read-posts.php?profile=heavyfrog799) and [friends](http://testbed.spxp.org/0.3/friends/heavyfrog799)
4. Install the [HeyFolks app](https://heyfolks.app) and explore some bridge or testbed profiles to see the end user experience
5. Manually create a simple profile with a plain text editor, put it on your web server and open it in the HeyFolks app
6. Set up your own profile in the HeyFolks app on spxp.space
7. Take a deeper look at the cryptographic operations by [exploring and decrypting some testbed profiles](https://github.com/spxp/spxp-crypto/blob/master/spxp-crypto-tools/ExploreTestbedProfiles.md)
8. You can then further [manually sign and encrypt](https://github.com/spxp/spxp-crypto/blob/master/spxp-crypto-tools/ManualProfileCreation.md) your hand crafted profile
8. Deploy the [Simple PHP Server](https://github.com/spxp/simple-php-server) and run your own spxp hosting service
9. Use the [spxp-cli](https://github.com/spxp-space/spxp-cli) to create test profile(s) on your own hosting service or spxp.space
10. Send a connection request with the spxp-cli to your profile in the HeyFolks app, accept it and see how you can unlock additional content additional content

## Why don't you just use...
...ActivityPub, Nostr, Desosprotocol, AT protocol, Diaspora, ...

A common and very relevant question.

We have prepared a [comparsion with other protocols](./info/Comparison.md) investigating the differences and discussing how these do or do not fulfil our design goals.

## Development resources
In addition to the protocol spec in this repository, you might find these additional resources helpful.

#### Testbed profiles
We provide sets of artificially generated profiles on [testbed.spxp.org](http://testbed.spxp.org) for all protocol versions. These can be used to
develop new client applications and validate your implementation against different test sets. The [generator](https://github.com/spxp/spxp-testbed-generator)
for these profiles is available as well under Apache license.

#### SDK / Reference implementation
There are already numerous libraries out there for HTTP communication and JSON handling - the two main foundation
blocks of SPXP. We think providing an SDK that would depend on one of these would interfere too much with developer preferences.
The situation however is different for the cryptographic operations in SPXP. The [spxp-crypto](https://github.com/spxp/spxp-crypto)
library provides a reference implementation in Java as well as a standard library that can be used directly in your
projects.

#### Commandline tooling
For most operations, `curl` and `jq` are sufficient. For cryptographic operations, we provide the
[SpxpCryptoTool](https://github.com/spxp/spxp-crypto/blob/master/spxp-crypto-tools/README.md)
which can be used on the command line or from scripts.
With this tool, you can [explore the testbed profiles](https://github.com/spxp/spxp-crypto/blob/master/spxp-crypto-tools/ExploreTestbedProfiles.md)
from the command line.