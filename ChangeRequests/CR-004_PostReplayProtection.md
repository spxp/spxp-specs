# CR-004 - Post Replay Protection
Draft - Target SPXP 0.4

Version 0.3 has introduced [signing certificates](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#82-authorized-signing-keys)
and Version 0.4 will then use these to allow contributions from connected peers (
[CR-002](https://github.com/spxp/spxp-specs/blob/master/ChangeRequests/CR-002_PublishToConnectedProfile.md) and
[CR-003](https://github.com/spxp/spxp-specs/blob/master/ChangeRequests/CR-003_CommentsAndReactions.md)).

In the current draft, these contributed posts are not bound to the profile to which they are contributed, allowing
for a post replay attack as follows:
An attacker can observe timelines of profiles for contributions from peers with interesting content, for example
expressing consent, contradiction or support. The attacker can then create a new certificate allowing the contributing
author to publish on the attackers timeline. This is possible as the public key is known and there is no consent
required in certificate creation. Since the certificate chain is part of the signature object, which is not authenticity
protected, it is possible to replace this certificate and publish this post on the attackers timeline. For readers,
it looks like the contributing user expressed consent or contradiction towards the attacker rather than the initially
targeted profile.

## Proposed changes

### In section "10.1 Post object"
Add the following member to the "Each single post..." table:

| Name | Type | Mandatory | Description |
|---|---|---|---|
| `target` | Object | optional | [Social profile reference](#6-profile-reference-object) of the profile on which this post is published, if this post has been created by a different profile and then published on this profile. <br/> If present, it must match the profile on which this post is published. Required if ` author` is present. |

Add the following Note:
To prevent that contributed posts are published on a profile other than the author has intended to, clients are required
to compare the `target` profile reference against the profile on which the post is published in case an `author` has
been specified.
