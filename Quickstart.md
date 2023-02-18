# Get started quickly

## How it works

A social profile is described by a simple text file in JSON format. You can easily inspect them with your web broweser, like  out official profile:   
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

## Existing profiles

This protocol is intentionally so simple,
