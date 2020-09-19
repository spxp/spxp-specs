## 1 Create profile profile key pair

Create a new key pair for your profile and store it in a file. In this case `mwiesen_keypair.json`:

```shell
$ java -jar ./target/spxp-crypto-tools-0.3-SNAPSHOT.jar genprofilekeypair > mwiesen_keypair.json
$ cat mwiesen_keypair.json
```

Output:

```json
{
    "kty": "OKP",
    "d": "...",
    "crv": "Ed25519",
    "kid": "nsgPGGSTKrnjoUWZ",
    "x": "NptsYvJ9GByZEt7E5CqD2qS5W7c7hlOLEVN8dLfDUUU"
}
```

Make a copy without the private key:

```shell
$ java -jar ./target/spxp-crypto-tools-0.3-SNAPSHOT.jar extractprofilepublic mwiesen_keypair.json > mwiesen_publickey.json
$ cat mwiesen_publickey.json
```

Output:

```json
{
    "kty": "OKP",
    "crv": "Ed25519",
    "kid": "nsgPGGSTKrnjoUWZ",
    "x": "NptsYvJ9GByZEt7E5CqD2qS5W7c7hlOLEVN8dLfDUUU"
}
```

## 2 Register new profile

Use the public key as the value for the `publickey` key in the following JSON template:

```json
{
    "slug" : "mwiesen",
    "publicKey" :
}
```

Post the resulting JSON object to the profile registration endpoint, `profiles/v01/register` in our case:

```shell
$ curl -i -H 'Content-Type: application/json' \
-d '{
    "slug" : "mwiesen",
    "publicKey" : {
        "kid": "nsgPGGSTKrnjoUWZ",
        "kty": "OKP",
        "crv": "Ed25519",
        "x": "NptsYvJ9GByZEt7E5CqD2qS5W7c7hlOLEVN8dLfDUUU"
    }
}' \
http://profiles.xaldon.com/profiles/v01/register
```

Response:

```json
{"message":"Profile 'mwiesen' created","status":"200"}%   
```

**NOTES:**
- Shouldn't the profile registration return the exact URI under which the profile can now be reached? Like `http://profiles.xaldon.com/mwiesen`. This could also be used in the device registration step for `profile_uri`.

## 3 Register device

Generate a random `device_id`. In our case, we're going to use `A0HDYhmmWYDhI9QQ2zljUpzqJLRl2_65`.
Take the following template, insert the `profile_uri`, `device_id` and `timestamp` and store it in a file, e.g. `register_device.json`.

```shell
$ echo '{
    "profile_uri" : "http://profiles.xaldon.com/mwiesen",
    "device_id" : "A0HDYhmmWYDhI9QQ2zljUpzqJLRl2_65",
    "timestamp" : "2020-09-19T21:17:54.514+00:00",
}' \
> register_device.json
```

**NOTES:**
- It would be nice to know a bit more about potential `device_id` constraints, e.g. what's the recommended / maximum length? 32 characters/bytes? Are there any forbidden characters?
- Why not add a method to the cryptotools that generates a suitable random `device_id`? I would have used that right away...
- It would be nice to know what the timestamp is used for, e.g. so the server can "expire" device tokens after a certain period of time. But I wasn't sure if e.g. the device registration wouldn't work anymore after a timeout of, IDK 10 minutes or something.


Sign the device registration JSON object using the key pair:

```shell
$ java -jar ./target/spxp-crypto-tools-0.3-SNAPSHOT.jar sign register_device.json mwiesen_keypair.json
```

Output:

```json
{
    "device_id": "A0HDYhmmWYDhI9QQ2zljUpzqJLRl2_65",
    "signature": {
        "sig": "wFVVXwpfT49KyxDh6MPeXCosCU-tnBNZoTknYpIc2HM1sUU66ze_cl0t9j0mYgg_gRlIxnzfIP03fvdBszsSBQ",
        "key": "nsgPGGSTKrnjoUWZ"
    },
    "profile_uri": "http://profiles.xaldon.com/mwiesen",
    "timestamp": "2020-09-19T21:17:54.514+00:00"
}%     
```

POST this JSON object to the `manage/v01/auth/device` endpoint:

```shell
$ curl -i -H 'Content-Type: application/json' \
-d '{
    "device_id": "A0HDYhmmWYDhI9QQ2zljUpzqJLRl2_65",
    "signature": {
        "sig": "wFVVXwpfT49KyxDh6MPeXCosCU-tnBNZoTknYpIc2HM1sUU66ze_cl0t9j0mYgg_gRlIxnzfIP03fvdBszsSBQ",
        "key": "nsgPGGSTKrnjoUWZ"
    },
    "profile_uri": "http://profiles.xaldon.com/mwiesen",
    "timestamp": "2020-09-19T21:17:54.514+00:00"
}' \
http://profiles.xaldon.com/manage/v01/auth/device
```

Output:

```json
{"timestamp":"2020-09-19T21:18:00.648+00:00","status":500,"error":"Internal Server Error","message":"Incorrect result size: expected 1, actual 0","path":"/manage/v01/auth/device"}%
```

**NOTES:**
- Tried `http://profiles.xaldon.com/mwiesen`, `http://profiles.xaldon.com/profiles/mwiesen` and `http://profiles.xaldon.com/profiles/v01/mwiesen` URIs with no success
- Again, not sure if the timestamp plays a role, but I don't think so. Didn't have a look at the source code yet.
