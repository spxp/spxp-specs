# SPXP - Feature Idea Collection
The SPXP is developed in an agile manner, where we define new protocol versions, put them under test, and then evolve
based on the findings in simulations and real-world tests.  
Not all aspects of the overall vision are already realised with this version of the protocol. In particular, we are
aware of the following shortcomings and problems that we want to address in the upcoming versions of this
protocol.

##### Publishing posts, including encrypted posts
Currently, only the profile owner can publish posts via the Profile Management Extension. We also want other profiles to
be able to publish posts. This has already been prepared in this version by introducing signing certificates. But we are
still missing endpoints to publish posts. Also publishing encrypted posts makes this even more challenging.
 
##### Comments
We are aware that comments are a fundamental building block of social networks. Although there have been some
experiments with this feature, we decided to defer it to the next version.

##### Re-Publishing posts of other profiles in own profile
Some social networks allow users to "share" or "re-post" a post in the own profile that has initially been published by
a different profile. We are looking at this feature and will decide if and how we are going to realise this
functionality as part of SPXP.

##### Validity period of certificates
Certificates signed by a profile in this version of the protocol do not have any validity period. Permissions granted by
a profile never expire. An SPXP server needs to maintain an ever growing CRL (certificate revocation list) to be able to
reject posts from profile that are no longer allowed to post to this profile.

##### Profile signing key rollover
It is currently unclear if we need to define a process to rotate the profile key pair. In the case of a  key loss, the
profile needs to go through the process of key revocation anyway and establish trust in the new signing key. We do not
know yet if we need a secondary mechanism that performs a key rollover while the key has not been compromised.  
This also includes the question if the profile key needs to have a validity period.

##### Profile relocation
We only define that the profile signing key takes precedence over the profile URI, but we do not give any procedures or
examples how a profile can actually seamlessly move from one URI to another.

##### Approved friendship announcements
In the current version, a profile simply publishes a list of profile URIs it considers "friends". But there is no
guarantee that the linked profiles are actually connected with this profile or even know about this.  
It would be possible to mutually sign some sort of "connection certificate" during the connection process, so that a
profile can expose these "connection certificates" as proof that it is actually connected with the other profile.  
It is currently unclear if this is actually required or wanted.

##### Generalise concept of hometown and location
The `hometown` and `location` links in a profile could be generalised. A profile might also want to link to its current
or former employer, institute of education or other entities of interest.  
We could generalise this concept and just expose an arbitrary list of "profile links", each uniquely identified by a
key. Depending on the above decision, this could also cover the list of friends.

##### Profile root document cannot be signed by delegate
By design, the profile root document cannot be signed by a delegate. This is required as it contains the profile root
key itself. There might be use cases where a profile wants to delegate the publishing of the `about` or `shortInfo`
fields to another entity. This is currently not possible without exposing the profile root key.

##### Profile key revocation / reestablish trust in new key
The vision of SPXP describes a process where a profile can replace a lost or compromised signing key and re-establish
trust in the new key via neighborhood assistance through its connected profiles.
