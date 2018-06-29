---
layout: page
title: Flow
permalink: /flow/
---

## Steps in the boot process
### Step 1: Ping the api
Done to verify that the api is up and to learn the current time (because the rapsberry pi does not have a realtime clock, it needs to somehow learn the time if it want to use https in the boot process)
`GET https://api.cattlepi.com/ping`

### Step 2: Get the configuration associated with the device
Each raspberry pi device should have a configuration associated with it. The device will try to retrieve this configuration, first using it's own identified and if that fails using the identifier `default`  
By default the indentifier for a device is its mac address. This can be overriden and set to whatever you want to, although ideally you would still want different ids for different devices.

`GET https://api.cattlepi.com/boot/{deviceId}/config`  
and if this fails  
`GET https://api.cattlepi.com/boot/default/config`  

An example configuration for a device would be:  
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
The reasoning behing falling to the default device id is that you can use that to detect new devices that you've never seen before and register then and/or generate their own configs on the fly.
### Step 3: Download the images specified in the config
From the config, download and store locally the images specified in `initfs.url` and `rootfs.url`. The md5sum for each of these can be used to verify the images - by default this check is off.

### Step 4: Update initfs
If the contents of the boot partition are different than the contents of specified via the configuration, the downloaded initfs will be unpacked on the boot partition (ie overwritting the contents of the files already there - but not removing additional files) and the device will be rebooted. This ensures that the device always runs with the specified boot partition content. 

### Step 5: Mount the root filesystem and switch to it
The last step of the boot process consists in building the root filesystem. The root is an union filesystem that has 2 layers. A bottom read-only layer that is whatever was specified in rootfs.url and a top read/write layer that is a tmpfs filesystem.

## Implications of this boot flow
Some of these may be obvious, some not, so explicitely documenting them here:
 * the raspberry pi needs to be able to acquire a network address through DHCP and needs to be able to do internet traffic. the boot process will fail if both 2 are not met
 * all operations that happen during boot will be retried with exponential backoff in case of failure (ie failure of one api call does not mean much and in an usual case it will just add a few additional seconds to the boot - the boot is resilient to transient failures)
 * because of the way this is wired, only one partition is needed on the SD card: the boot partition. As an optimization the downloaded images are stored on this partition. The size of the partition should be enough to accomodate both the boot files themselves and the images. We recommend that the boot partition is at least 1GB in size.
 * also because the way this is wired, the SD card is potentially written to only at boot time. Once the Pi has booted and you have the root filesystem, for all intents and purposes the card is only read from (for the lower layer of the union root filesystem).
 * if the images are not already cached, the process to download them may introduce a significant boot delay when the download need to happen (eg if a image is 300M and the internet download speed is 5M/s, the download will take 60seconds)
 * because the way the root filesystem is built, any changes done to the filesystem will live in the upper layer of the union and will be lost on reboot. This is good because it means that all the state you have on the device is expendable and you can probably rebuilt it after a reboot. If you rely on the state of the device you will have to figure out a state to persist it outside of cattlepi.

## More... 
Find more about the boot flow details w/ links to the code here: [BOOT.md](https://github.com/cattlepi/cattlepi/blob/master/doc/BOOT.md)

