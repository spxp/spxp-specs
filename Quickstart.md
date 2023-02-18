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
This file then references additional resources, like our profile picture. It also declares additional endpoints, like the `postsEndpoint` which delivers
a time series of updates, known as posts. You can inspect them in your browser as well:  
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

This protocol is intentionally kept as simple as possible, to allow easy adoption.

You can even find a bridge between other social network protocols and SPXP at [bridge.spxp.org](https://bridge.spxp.org). As of today, it only supports
ActivityPub. But there are already plans to extend the bridge for protocols like Nostr or Diaspora.

To follow a Mastodon Profile like the [Austronomy Picture of the Day](https://botsin.space/@APoD) (@APoD@botsin.space), simply use this profile URI:

[https://bridge.spxp.org/ap/@APoD@botsin.space](https://bridge.spxp.org/ap/@APoD@botsin.space)  

You can again inspect this profile simply with your web browser. You can take a look at the "friends" of this profile:

[https://bridge.spxp.org/ap/@APoD@botsin.space/friends](https://bridge.spxp.org/ap/@APoD@botsin.space/friends)  

to discover new interesting profiles.

## Client App

Opening SPXP profiles in a web browser and hopping through the social graph via copy pasting profile URIs is possible. But it's definitely not the most conventient way to explore the network. 

A more user friendly approach is to install a mobile app, like the [HeyFolks App](https://heyfolks.app). As a starting point, the app suggests a couple of SPXP profiles as well as bridged Mastodon profiles. You can then see the profiles they're following by clickin on the middle tab:

![middle-tab icon][middle-tab]

This way you can browse through and get a feeling for the network.  Please note that following profiles is managed by the HeyFolks App and invisible to the profiles you follow. 



## Building your own profile (manually)

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

We recommend turning on the "Developer mode" in the settings of the HeyFolks App. This makes a "Refresh" button appear in your profile so you can enforce a reload after you made a change to your profile JSON file.

Take a look at the [Spec](https://github.com/spxp/spxp-specs/blob/master/SPXP-Spec.md) or [this video](https://www.youtube.com/watch?v=C0S0Oa4G1M4) to learn more about the format of the JSON file.

## Setting up your own profile on a service provider

Writing your own JSON file and putting it on your own web server is a nice start, but not the most convenient option for average users. Apps like the HeyFolks App additionally speak other [auxiliary protocols](https://github.com/spxp/spxp-specs/blob/master/SPXP-SPE-Spec.md) to allow you to manage a profile directly in the app.

To setup your own profile in the HeyFolks App, click on the right tab:

![right-tab][right-tab]

The app will ask you to chose a service provider that hosts your profile for you, like [SPXP.space](https://spxp.space). Don't worry. You won't be locked in to a particular provider. It is always possible to migrate to a different provider seamlessly and without any friction as you can see in [this video](https://www.youtube.com/watch?v=kDv0rW8uEwA). 



 
[middle-tab]:./assets/middle-tab.jpg?s=50
[round-plus]:./assets/round-plus.jpg?s=50
[right-tab]:./assets/right-tab.jpg?s=50