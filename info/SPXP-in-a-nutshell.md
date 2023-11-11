# SPXP in a nutshell
The following is a simplified introduction to the Social Profile Exchange Protocol (SPXP). Its purpose is neither to explain every aspect in detail nor to go too deep into the technical depths of the protocol (for this purpose it’s better to read the [protocol specification](https://github.com/spxp/spxp-specs) or watch the [YouTube videos](https://www.youtube.com/@SocialProfileExchangeProtocol)).  
Instead it’s meant to provide a quick overview over the protocol and its capabilities and features for everybody.

### 1 Introduction
* a) SPXP is an open protocol for a decentralized social network
* b) SPXP strives for modeling relationships between humans in the physical world as well as learning from and improving on existing social networks
* c) It’s designed like the WWW and based on a client server model. (i.e. no peer-2-peer, federated, or relay network; no blockchain)
* d) Social profiles are hosted on servers and viewed by client apps
* e) Additionally, SPXP uses strong end-to-end cryptography to ensure authenticity, confidentiality and sovereignty aspects of the protocol.

### 2 Cryptography
* a) SPXP profiles can sign and encrypt messages (e.g. posts) and profile information (e.g. the year of birth)
* b) SPXP supports public and private information, e.g. posts.
* c) Public posts can be signed, but are not encrypted. Everybody can view them.
* d) Private posts are always signed and encrypted. Not everybody can view them.
* e) Signatures ensure information authenticity. Taking posts as an example, this guarantees:
  * i) A post’s authoring profile. A third party cannot pretend to be the author of a post.
  * ii) A post’s integrity. A third party cannot alter a post.
* f) End-2-end encryption ensures confidentiality and information privacy. Only the sending and receiving client apps are able to view encrypted information.

### 3 Communication
* a) A profile owner chooses if a post is public or private
* b) Private posts address one or more audiences, e.g. Alice might have “Friends”, “Close Friends” and “Family”.
* c) To view private posts of each other, two profiles need to mutually connect
* d) A profile owner may allow connected profiles to contribute to their own profile, e.g. by posting comments or reactions.
* e) A profile owner decides to which audience a connected profile belongs to
* f) A profile owner can terminate a connection at any time. Going forward, the two disconnected profiles cannot see private posts of each other anymore.

### 4 Privacy
* a) Unlike many social network platforms, SPXP doesn’t require creating an account or setting up a profile to browse the social network. Like a web browser can view websites in the WWW, an SPXP client app can view SPXP profiles.
* b) An SPXP server cannot see the content of private information it hosts, like private posts, comments or reactions (see 2f)
* c) The connection process is designed to use profile servers as a relay, so that both clients do not need to be active at the same time. It is end-to-end encrypted to prevent the server from gaining any knowledge beyond the bare minimum.
* d) Once two profiles established a connection between each other, a server doesn’t know which profile is commenting or reacting to a post
* e) As opposed to current major social network platforms, SPXP’s client server model prevents a single server from gaining absolute knowledge of the whole social network.

### 5 Sovereignty
1. a) A user doesn’t need to agree to a platform’s Terms of Service (ToS) to view an SPXP profile. An SPXP client and the profile URL are sufficient.
2. b) Creating an SPXP profile does not force a profile owner to agree to a platform’s ToS. Instead, they could decide to host their profile on a simple web server or choose from a variety of different hosting solutions and their individual offerings and terms of service.
3. c) A profile owner can choose to run their own SPXP server to host their profile
4. d) A profile owner can choose to relocate their profile to a different SPXP server, including their own.
5. e) A profile owner has full control over their content (see 5a, 5b, 5c, 5d)
6. f) A profile owner has full control over defining the recipients of each post (see 3)
7. g) In general, only connected profiles can comment on each other’s posts or react to them with an emoji (see 3d).   
  That is because a user cannot comment on or react to a post unless their profile is either connected with the author’s profile, or otherwise authorized.   
  Again in other words: In general, a user cannot comment on or react to an unknown profile’s post.
8. h) A profile owner has full control over comments on and reactions to their posts (see 5g)
9. i) A profile owner is able to publish all information on their SPXP profile that they are legally allowed to publish on the WWW.  
   Existing legislation applies. Countries have developed legislative frameworks for content published on the WWW; The same frameworks apply for content published via SPXP.
10. j) There is no single, private, company owned, governance board (see 1c, 4e, 5i)
11. k) Users can and should make use of existing judicial systems; They are able to take action against published information with the same mechanisms as against information published on the WWW.
12. l) SPXP transfers control of content creation and consumption to the endpoints (e.g. client apps)
13. m) SPXP consciously minimizes the role a server plays with regards to information publishing and profile and content management. This happens to an extent that an SPXP server is hosting encrypted information but does neither know the content nor its author (see 4c, 4d)
14. n) An SPXP server hosts profiles and their content but cannot modify either on its own as the network endpoints have exclusive control over that (see 5l)
