---
layout: page
title: Api
permalink: /api/
---
The endpoint for the API is: `https://api.cattlepi.com`.

All examples here use `curl` to show interactions with the API.

### Content Type

The API accepts JSON and produces JSON. 

### API Keys

All requests to the API must be accompanied by an API key. Requests without an API key will fail with a `400 Bad Request` response status.  

**The key must be passed in a header named 'X-Api-Key'**

The provided demo API key is `deadbeef`  
To request an API key, send an email:  

To: `hello@cattlepi.com`

Subject: `NEW API KEY REQUEST`

(we are currently working on automating this process)

Example request:
```bash
curl -H "Accept: application/json" \
     -H "Content-Type: application/json" \
     -H "X-Api-Key: deadbeef" \
     https://api.cattlepi.com/boot/testid/config
```

The corresponding response would be:
```json
{
  "initfs": {
    "url": "https://api.cattlepi.com/images/global/initramfs.tgz",
    "md5sum": "93a4eccacabdcce8eb5b8b68de6742cc"
  },
  "rootfs": {
    "url": "https://api.cattlepi.com/images/global/rootfs.sqsh",
    "md5sum": "c1d44c65d29af575b2f6685b6a91d2da"
  },
  "bootcode": "",
  "usercode": ""
}
```

### Supported API Operations

#### `GET https://api.cattlepi.com/boot/{deviceid}/config`

This call is used to retrieve the boot configuration associated with a device.  
As an example, use the same from the API Key section above.

**A note on isolation**: two users (i.e. different API keys) can have the same deviceid without experiencing a collision. The device ids live in the scope of the API key and there is no way of accessing the device configuration of another user if you don't know the API key.

#### `POST https://api.cattlepi.com/boot/{deviceid}/config`

THis call is used to update the boot configuration associated with a device.

Example request:
```bash
curl -H "Accept: application/json" \
     -H "Content-Type: application/json" \
     -H "X-Api-Key: deadbeef" \
     -X POST -d '{"rootfs":{"url":"https://api.cattlepi.com/images/global/rootfs.sqsh","md5sum":"c1d44c65d29af575b2f6685b6a91d2da"},"initfs":{"url":"https://api.cattlepi.com/images/global/initramfs.tgz","md5sum":"93a4eccacabdcce8eb5b8b68de6742cc"}}' \
    https://api.cattlepi.com/boot/otherdevice/config
```

A successful response would be:
```json
{"status":"ok"}
```
At this iteration the API will **ignore** any parameter that is not rootfs.url, rootfs.md5sum, initfs.url, initfs.md5sum, bootcode or usercode. If any of these is not specified it will be set to an empty string.

**A note on isolation**: Because the demo API key is a shared one, this operation is explicitely disabled (you don't want a random stranger to set the boot target images for you devices, do you?). This restriction does not apply for your own API keys.

#### `GET https://api.cattlepi.com/images/{space}/filename`

This call is used to download an image. 
The only valid values for the {space} path parameter are: `global` and the user's API key.

Example request:
```bash
curl -v -H "X-Api-Key: deadbeef" \
    https://api.cattlepi.com/images/global/initramfs.tgz
```

At this iteration this will most likely result in a redirection (302) and the location that you will be pointed to is a temporary AWS S3 download. You will need to follow the redirect and download the image before the link expires.

A sample response:
```bash
< HTTP/1.1 302 Found
< Date: Tue, 26 Jun 2018 05:20:55 GMT
< Content-Type: application/json
< Content-Length: 0
< Connection: keep-alive
< x-amzn-RequestId: b3ae5e43-7900-11e8-9b7b-4d5b77c8057d
< x-amz-apigw-id: JE0AsH3yvHcFo7w=
< Location: https://cattlepi-images.s3.amazonaws.com/global/initramfs.tgz?AWSAccessKeyId=ASIAIK4I7NAAVTVCQ6UA&Signature=a7u87tfMnC3N0h6h8rLJigSc3BM%3D&x-amz-security-token=FQoDYXdzEJ7%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaDMoDCHuiWoGyR2XCpCKNAimobryo74h6%2BjdDsKl4DsWOtXQsKkLJE%2F4aXHHrBGtd9UFfk%2FbdNj10MryFenYB%2BCWfKQGmIOC1ouEMR0GIlsZb2X3NjGNhagOO%2FIpFm4auqgect3P69fkQqNAOSPB40EWXldnJTjDcXoc9th4ZRhjn3rmOftd4w7VdoHtKU3AT2CxnykldrF3cAviMig8FX2DJU%2F7nF8tfM3h46%2BhG4z6iKr9W76WUGWAmF69rFpF7XfZZhqTcdnj5OTNZ4%2BjpTnokhi88X5%2BB8489%2FIORyRwcCIdSJIaHQ2xI%2Fa7GKZpSPzaYrdXP7gHCeQOVW0XxDTgqRqqi1sNNo42U2RKbMuwE2pShm6nUwmBpi0lAKJOax9kF&Expires=1529990485
< X-Amzn-Trace-Id: Root=1-5b31cd37-a90fd0188f6c12685705b278
```

### Limits

Ideally we would like the usage of this API to be free. We hope that, for the vast majority of users and use cases, this will be the case. For the exceptional cases we do have quotas on the number of API calls that you can make. This ensures that our running costs do not spiral out of control. 

The quotas are as follows:
 * `GET  https://api.cattlepi.com/boot/{deviceid}/config`: **maximum of 60 requests per hour**
 * `POST https://api.cattlepi.com/boot/{deviceid}/config`: **maximum of 10 requests per hour**
 * `GET https://api.cattlepi.com/images/{space}/filename`: **maximum of 10 requests per month**

Unused requests **do not accumulate**.

What this means is:
 * if you had a single device, and rebooted it each minute, you would not hit the API limits
 * if you had to alter or update your configuration, you could make perhaps ten updates per hour _at the most_. Again, with one device, this should not constrain you in any way 
 * the ten requests per month for downloading images may seem a bit draconic at first. The reasoning is as follows: the images themselves are large and the downloads will consume real bandwidth. If you have less than five devices, this quota should easily suit your needs (recall that the images are cached locally - this is the main reason why we want to cache them). If you need to get around this you could store the images somewhere your devices can access (intra- or internet), and point the config to those images. Alternatively, we are open to learning more about your use case (and perhaps helping you with the additional bandwidth)

We do not currently support uploading your custom images through our API. However, this is on our strategic roadmap. Again, we encourage you to reach out and tell us about your use case.
