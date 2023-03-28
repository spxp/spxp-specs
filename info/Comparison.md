# SPXP compared to other protocols
Multiple other protocols have been proposed for decentralised social networks. It is worth comparing SPXP against a selection
of popular alternatives, investigate the differences and discuss why these do not fulfil the design goals we are looking for
with SPXP:
* privacy
* security
* individual sovereignty
* simplicity

### ActivityPub (gnuscial, Mastodon, Pleroma, Pixelfed, etc)
[ActivityPub](https://www.w3.org/TR/2018/REC-activitypub-20180123/) uses a federated architecture where users need to create an account
on one of multiple available instances. Data, like profile information or individual posts, is replicated as needed between instances.
* Your ID is bound to the instances domain
* You don't have full control over your own profile  
  Instance owners can close and delete your profile at any time at their sole discretion - and sometimes even close the entire instance.
  All your data and connections will be lost, at least until your last manual backup. Profile migrations are an afterthought and only possible
  if the instance owner collaborates.  
  This is a violation of the *individual sovereignty* we are interested in.
* You don't have full control over your own timeline.   
  Instance owners block content from other instances at their sole discretion. Accounts on blocked instances will not be able to interact
  with you and you won't even see their content. This content moderation has some positive impacts, e.g. for blocking spam and fighting harassment.  
  However, since you don't have control over this content moderation, it is a violation of the *individual sovereignty* we are interested in.
* The authenticity of your data is not guaranteed  
  Without real end-to-end digital signatures, it is possible to tamper with existing messages or completely forge them.  
  This is a violation of the *security* we are interested in.
* Your data is fully accessible by instance owners  
  This includes absolutely everything and is a severe violation of the *privacy* we are interested in.
* You cannot control how your content is used  
  As a federated protocol, it requires that your data is replicated between different instances, and you have no control over this mechanism. Other
  instances in the Fediverse will display your content on their website, under their own terms and under their exclusive control.  
  This is a violation of the *individual sovereignty* we are interested in.
* You cannot limit the visibility of your content  
  There is some content you are happy to share with the whole world. But there might be other content you only want to share with your
  friends or your family. This is not supported in ActivityPub. Everything is publicly visible (except DNs).  
  This does not provide the level of *privacy* we are interested in.

### AT Protocol (Bluesky)
The [AT Protocol](https://atproto.com/docs) is very similar to ActivityPub and again using a federated architecture. Although there are
technical differences, the same findings as above apply here as well:
* The handle is bound to a DNS domain
* There is no end-to-end message encryption
* Personal Data Servers (PDS) owners have access to all data and can block interactions
The AT protocol is pretty new and is [currently seeing a lot of changes](https://github.com/bluesky-social/atproto/commits/main). It is
possible that the above analysis is no longer accurate. We will try to keep up with the recent developments and update this section accordingly.

### Nostr
[Nostr (Notes and Other Stuff Transmitted by Relays)](https://github.com/nostr-protocol/nostr) is an interesting candidate. It solves many
of our requirements in a quite similar manner. However, it only provides public posts and does not go as far as SPXP in terms for privacy
and sovereignty matters:
* Profiles are bound to a cryptographic keypair rather than a domain, making them portable
* Events are end-to-end signed, guaranteeing message authenticity
* All messages are public, except for direct messages.
