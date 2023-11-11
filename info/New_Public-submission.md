# New_ Public Submission
## What is the Digital Space? Please describe in detail.
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


## WELCOME
### Signal 1: Invite everyone to participate
What it is: *The ability and opportunity for all of us to take part in digital society, regardless of background, especially for people who are typically disadvantaged. Also known as “social inclusion.”*

* SPXP has a very low entrance barrier (see 4a). Participating in the SPXP network and community is easy. It’s safe to assume that freemium profile hosters and client apps are available (e.g. [HeyFolks App](https://heyfolks.app/), [spxp.space](https://spxp.space/)). Thus, the only things required to participate are for example a smartphone, a client app (at no cost) and an internet connection.
* SPXP is inclusive by design. Everybody can consume content without the need to sign terms of services (see 5a 5b) or to create their own account or profile first (see 4a). SPXP can be compared to the WWW which has shown that it’s inclusive (the same low entry barriers)
* Easy profile and content discovery. Like with the WWW, search engines facilitate searchability and findability of SPXP profiles and public content. Additionally, users can browse the network by jumping along the connections between profiles.
* SPXP is a protocol, not a product. The potential amount of apps and solutions that implement the protocol and offer their services is unlimited. 
* Given SPXP finds broader interest, we can assume a multitude of client and server solutions a user can pick from.
* For nerds: In its easiest form, an SPXP profile is just a text file (json) on a web server. It’s very easy to get started. Furthermore, there is an open source implementation available for running a simple SPXP server as well as other useful tools for developers (see https://github.com/spxp)

### Signal 2: Ensure people’s safety
What it is: *The protection of digital participants from harm or danger ranging from malware, identity theft, and harassment to sexual victimization and exposure to violent material.*

* People feel safest when communication is secure. See section 2 and 3.
* SPXP users decide at any time which content they want to consume. For example, a user could choose to view all content of a profile in chronological order.
* There is no intermediate actor between consumer and creator who could impact the content (see 5l, 5n). For example, there is no central content pre-selection or “algorithm”.
* Content could be filtered / pre-selected at the client, but the user would have full control over that either by adjusting settings or choosing a different client app.
* Users can unfollow or disconnect profiles at any time (see 3f).
* Users can post anything they’re allowed to post in the WWW (see 5i)
* Users should reach out to prosecution authorities about (potentially) illegal content. (see 5k)
* Users don’t depend on a single, (private) platform (see 1a)
* Users don’t depend on a single, private, company-owned governance board (see 5j)

### Signal 3: Encourage the humanisation of others
What it is: *The recognition and affirmation of the humanity of other people.*

SPXP focuses on modeling relationships between real humans in the physical world. This modeling is based on the following observations:
1. In essence, relationships between humans can be viewed as based on trust and affection. Our relationships could be grouped into concentric circles with increasing radii. The individual is at the center and their trust and affection for people in a smaller circle (“close to them”) is higher than for people in a bigger circle (“farther away”). The circles could be labeled “Family”, “Close Friends”, “Friends”, “Co-workers”, “Community members”, etc. Last but not least the public is outside the biggest circle.
2. Our trust and affection for others are highly individual. Alice might consider Bob as a close friend. Bob’s view might differ and he sees Alice as one of many co-workers.
3. Humans are only capable of maintaining a limited number of relationships with other individuals.
4. Our trust and affection for others is in a constant state of reevaluation and change.

SPXP’s properties described in section 3 “Communication” implement all of the above. The SPXP network is not big enough yet to draw from world experiences and analysis. However, the idea is that:
* SPXP allows for and thus encourages a user to address small audiences of mutually connected profiles who want to share private content with each other in a secure way. Humans by nature want to share with their loved ones. Members of these audiences know each other and treat each other like humans.
* In general, SPXP prevents commenting on a public post without being connected to the author of the post. Remember that a connection needs to be mutually agreed upon.   
  Today’s social media platforms often allow members to comment on a public post. The comments themselves often become a thread of their own that easily and often rather quickly diverts from the topic of the original post. Even worse, the thread may contain insults or defamation. Furthermore, as a friend of someone participating in a commenting thread, the platform may show my friends' comment in my timeline, thereby increasing the visibility of the potential toxic thread. All this is not possible with SPXP.

Human relationships can also be commercial in nature. For example you might want to consume premium content from your favorite artist.
* SPXP supports commercial relationships by the exchange of reader keys (paywall). This allows for example an artist to publish premium content to a limited and defined set of profile owners who don’t need to grant access to any private content to the artist. 

### Signal 4: Keep people’s information secure
What it is: *The preservation of the integrity and confidentiality of people’s private information.*

* Integrity and confidentiality are a main design criteria of SPXP (see section 2 and 3)
* SPXP implements real e2e authenticity and encryption (see section 2)

Other aspects to SPXPs safety are:
* SPXP focuses on modeling relationships between real humans in the physical world
* Authenticity: It’s likely to have connections with real people (see signal 3)
* The degree of authenticity of a profile increases with the amount of overlapping connections with that profile (e.g. it’s a friend of a friend / 2nd degree friend)
* A client app could show the number of connected profiles which are connected with a third profile as well (e.g. to validate a connection request)


## CONNECT
### Signal 5: Cultivate belonging
What it is: *The feeling of connection to others.*

* SPXP focuses on modeling relationships between real humans in the physical world
* SPXP fosters trustful connections between individuals (see signal 3)
* Audiences are designed to feel like a safe space by creating an environment that:
  * provides the profile owner full control over shared content and membership
  * ensures privacy and integrity (see section 2) 
  * makes it likely to connect with real humans, businesses or institutions (see signal 3)

### Signal 6: Build bridges between groups
What it is: The formation of social connections that allow information, resources, and opportunities to travel between groups that might not ordinarily connect. 

* SPXP currently doesn’t support retweeting or boosting a third party’s post. The authors of SPXP consciously want to avoid scenarios where wrong or even harmful content can be amplified by network participants. We’re aware that this comes with the cost of not being able to amplify true and “good” content as well.
* SPXP allows audience members to see comments of other members even if both members are not connected with each other. This fosters building bridges between groups.
* Connected profiles can see each other’s other connections. This allows for browsing the network and having a look at the profile of or even connecting with the friend of a friend.
* The SPXP network is completely transparent and open. It’s very likely that there will be search engines (as a matter of fact, the SPXP authors already have a search engine up and running)
* Search engines facilitate searchability and findability of SPXP profiles and public content. 
* Because all public content is searchable, SPXP search engines could easily gather statistics and list occurrences of hashtags.

### Signal 7: Strengthen local ties
What it is: *The connections that people have with the physical places and communities in which they live.*

* The protocol is easy to start with and has low entrance barriers (see signal 1)
* Similar to the WWW, SPXP allows for every local shop to own their own SPXP profile
* Local communities or shop owners don’t need to pay money to a platform to increase their visibility. As a matter of fact, that’s not even possible with SPXP. 
* SPXP’s design allows business relationships between profiles (see signal 3).
* SPXP profiles allow for defining their location. A local shop owner could provide its location in their SPXP profile to get exposed via a “nearby” feature a client App might implement.
* A user could discover new opportunities via their social graph, like a new restaurant or bar recently visited by their connected friends.
* A shop owner could post the offers of the day on their profile and connect its URL with an NFC tag in the shop. This way customers could easily retrieve the offers on their client app.

### Signal 8: Make power accessible
What it is: *The degree to which the public is heard by those in power, whether in government, business, or other institutions.* 

* Like the WWW: SPXP profiles and public content are visible for everyone can be searched and found by search engines
* Like the WWW: Search engines could aggregate hashtags and generate trends and zeitgeist (think: MeToo movement)
* In contrast to closed or federated networks, these search engines will be independent and focus on the users needs rather than optimizing search results for their specific business  model


## UNDERSTAND
### Signal 9: Elevate shared concerns
What it is: *The extent to which issues that are important can be elevated for consideration by society at large, whether by the news media, legislators, interest groups, or other actors.*

* SPXP currently doesn’t support retweeting or boosting a third party’s post (see signal 6)
* SPXP profiles and public content are visible for everyone and can be searched by everyone, including media, legislators, interest groups, or other actors.
* Moreover, media, legislators, interest groups, and other actors can have their own SPXP profile to publish updates and statements.
* The SPXP network is completely transparent and open. Academic institutions will have access to all public messages for political, sociological, and other research.
* Search engines allow for aggregating hashtags and trends (see signal 6)

### Signal 10: Show reliable information
What it is: *The amount of information shown that is verified based on the best available evidence, and for which the production process is transparently disclosed.*

It’s hard for a social network to be designed in a way that fosters posting verified or at least traceable messages. That being said, we’d like to list a view points that might be relevant:
* SPXP supports message authenticity and client profiles can highlight authentic messages (by signature) (see 2e)
* Assuming that SPXP fosters connections between real humans (see signal 3) and thus assuming that a profile owner knows the person behind a connected profile, they can likely already assess the credibility of this person.
* Assuming real connections between humans that know each other, it should be comparably easy to reach out and double check the truthfulness of a message.
* Posts from unconnected profiles are harder to assess. 
* Like the WWW: Search engines could weight a piece of information by the amount of links that reference it (think: Page rank at Google). The idea is that the majority of people will more likely reference truthful and valuable information than false or misleading information.
* Authors publishing illegal information can and should be prosecuted (see signal 2)

### Signal 11: Build civic competence
What it is: *The awareness of how to perform one’s particular roles in a democracy, such as the roles of a citizen or a voter.*

At this point, we don’t know how SPXP can contribute to this signal.

### Signal 12: Promote thoughtful conversation
What it is: *The interactions among people with differing views that shed light on why people believe what they do, and involve the consideration of others’ perspectives.*

While SPXP cannot foster a certain type of content, its design can help to promote thoughtful conversation, mainly by preventing unthoughtful conversation.
* SPXP does generally not support to comment on public posts of unconnected profiles (see signal 6)
* Comments are always signed and associated with a profile. It’s not possible to comment anonymously.
* SPXP fosters communication between real humans that typically know each other (see signal 3 and 6).
* It’s also possible to comment on a public post of an unconnected profile if this profile grants a one time or time limited authorization to comment. However, commenting this way requires a confirmation on the commenter’s client app which enforces a conscious decision and helps reduce emotional and displacement behavior.  
   Such a comment will still be signed and associated with the commenter’s profile.


## ACT
### Signal 13: Boost community resilience
What it is: *The ability of a community to recover from significant stress or adversity, such as natural disasters, public health emergencies, or violence.*

The positive effects of the WWW are well  researched and understood. Since SPXP attempts to be a “social network designed like the WWW”, we hope to add a social aspect. For example, information can travel along profile connections and reach a targeted audience.  
In addition,  the decentralized and distributed nature makes this protocol much more resilient than a centralized or federated service.

### Signal 14: Support Civic Action
What it is: *Action to address issues of public concern — from volunteering to attending community meetings to co-creating new spaces and institutions.*

Please see signal 13.


## HOW CAN COMMUNITY BY DESIGN BEST SUPPORT YOU?

Increase SPXP’s visibility in general but specifically amongst public institutions that currently need to make use of the big private US platforms due to a lack of alternatives that allow them to have 100% control over their content.
