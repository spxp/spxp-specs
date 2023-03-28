# Get started quickly

## How it works

A social profile is described by a simple text file in JSON format. You can easily inspect them with your web broweser, like  our official profile:   
[https://spxp.org/spxp](https://spxp.org/spxp)

```json
{
    "ver": "0.3",
    "name": "SPXP.org",
    "shortInfo": "Social Profile Exchange Protocol",
    "about": "A protocol for a decentralised social network, focusing on privacy, security and individual sovereignty.",
    "website": "https://spxp.org",
    "profilePhoto": "https://spxp.org/spxp-profile-logo.png",
    "postsEndpoint": "https://spxp.org/spxp-posts"
}
```

This file then [references additional resources](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#5-social-profile-root-document), like our profile picture. It also declares [additional endpoints](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#10-posts-endpoint), like the `postsEndpoint` which delivers a time series of updates, known as posts. You can inspect them in your browser as well:  
[https://spxp.org/spxp-posts](https://spxp.org/spxp-posts)

```json
{
    "data": [
        {
            "seqts": "2021-12-11T12:00:00.000",
            "type": "text",
            "message": "Running some last tests with the new protocol features. You will soon see PRs..."
        },
        {
            "seqts": "2021-12-02T12:00:00.000",
            "type": "text",
            "message": "Now that we have a mechanism in place to contribute posts to peer profiles, we can build ..."
        }
    ],
    "more": true
}
```

## Bridge to other protocols

With it's simplicity, it is quite easy to convert information from other social network protocols into SPXP.

At [bridge.spxp.org](https://bridge.spxp.org), we provide such a bridge currently supporting:
* ActivityPub
* AT Protocol
* Nostr

For example, to see the SPXP profile JSON of a Mastodon account like [Spaceflight](https://techhub.social/@spaceflight) (`@spaceflight@techhub.social`), just use the following URI schema:

[https://bridge.spxp.org/ap/@spaceflight@techhub.social](https://bridge.spxp.org/ap/@spaceflight@techhub.social)  

Next, let's check out whom the Spaceflight Mastodon accont is following by having a look at the SPXP profile's [friend endpoint](https://github.com/spxp/spxp-specs/blob/master/SPXP-Spec.md#9-friends-endpoint):

[https://bridge.spxp.org/ap/@spaceflight@techhub.social/friends](https://bridge.spxp.org/ap/@spaceflight@techhub.social/friends)  

The bridge response with a list of SPXP profile references to other bridged accounts the Spaceflight account is "Following". When using
Firefox, you should be able to directy click on the profile links to hop to their profile document.  
We can also take a look at the posts of this account in the SPXP format on the [posts endpont](https://github.com/spxp/spxp-specs/blob/v0.3/SPXP-Spec.md#10-posts-endpoint):

[https://bridge.spxp.org/ap/@spaceflight@techhub.social/friends](https://bridge.spxp.org/ap/@spaceflight@techhub.social/posts)  

The same is possible with accounts on Bluesky via the AT protocol, for example the "Vibes" account (`@vibes.bsky.social`) you see on all the published screenshots:

[https://bridge.spxp.org/at/@vibes.bsky.social](https://bridge.spxp.org/at/@vibes.bsky.social)

I leave it to you as an exercise to explore the posts and followed profiles by yourself. :-)

With a Nostr public key, like the one from [Uncle Bob Martin](https://twitter.com/unclebobmartin/status/1479070661435871234), you can also explore Nostr:

[https://bridge.spxp.org/nostr/2ef93f01cd2493e04235a6b87b10d3c4a74e2a7eb7c3caf168268f6af73314b5](https://bridge.spxp.org/nostr/2ef93f01cd2493e04235a6b87b10d3c4a74e2a7eb7c3caf168268f6af73314b5)  

Feel free to continue from here on your own. By now, you should have a good impression of the simplicity of this basic protocol level.


## Client App

Opening SPXP profiles in a web browser and hopping through the social graph via copy pasting profile URIs is possible. But it's definitely not the most conventient way to explore the network. 

A more user friendly approach is to install a mobile app, like the [HeyFolks App](https://heyfolks.app). As a starting point, the app suggests a couple of SPXP profiles as well as bridged
Mastodon accounts. Follow some of the suggestions and after finishing the setup dialogue, have a look at the profiles you're following by clicking on the middle tab:

![AppProfilesFollowing][AppProfilesFollowing]

Just click on a profile to see their posts. You can also see profiles they're following by clicking on the middle tab again. This way you can browse through the network and get a feeling for which other profiles you might like and want to follow.

Please note that following profiles is managed by the HeyFolks App and as a consquence is invisible to profiles you follow. They won't know that you're following them.



## Publishing a manually built profile on a web server

All  you need to do is to craft a simple JSON document and make it available through HTTP(S), e.g. by placing it on your web server. You can very simply already start with:

```json
{
    "ver": "0.3",
    "name": "Jane Doe"
}
```

Once published, follow it with the HeyFolks App with three simple steps:
1. Click on the middle icon  
   ![middle-tab icon][middle-tab]
2. Click on the round plus icon  
   ![round-plus][round-plus]  
3. Enter the profile URL from the JSON file on your web server

We recommend turning on the *Developer mode* in the settings of the HeyFolks App. This makes a *Refresh* button appear in your profile so you can enforce a reload after making changes to your profile.

Take a look at the [Spec](https://github.com/spxp/spxp-specs/blob/master/SPXP-Spec.md) or [this video](https://www.youtube.com/watch?v=C0S0Oa4G1M4) to learn more about the format of the JSON file.

## Publishing your own profile with a service provider

Writing a profile's JSON file and putting it on your own web server is a nice start, but not the most convenient option for average users. Apps like the HeyFolks App additionally speak other [auxiliary protocols](https://github.com/spxp/spxp-specs/blob/master/SPXP-SPE-Spec.md) to allow you to manage your profile directly in the app.

To setup your own profile in the HeyFolks App, click on the right tab:

![right-tab][right-tab]

The app will ask you to chose a service provider that hosts your profile for you, like [SPXP.space](https://spxp.space). Don't worry. You won't be locked in to a particular provider. It is always possible to migrate to a different provider seamlessly and without any friction as you can see in [this video](https://www.youtube.com/watch?v=kDv0rW8uEwA).

The HeyFolks app also automatically creates a public/private key pair for your profile. It [publishes the public key in your profile](https://github.com/spxp/spxp-specs/blob/master/SPXP-Spec.md#6-profile-reference-object) and signs your posts from this point on. The signature proves that it's your post (and only your post) and indicates wether it was changed during transmission ([data authenticity](https://github.com/spxp/spxp-specs/blob/master/SPXP-Spec.md#8-data-authenticity)).



[AppProfilesFollowing]:./assets/AppProfilesFollowingSmall.png
[middle-tab]:./assets/middle-tab.jpg?s=50
[round-plus]:./assets/round-plus.jpg?s=50
[right-tab]:./assets/right-tab.jpg?s=50