---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---
[cattlepi.com](https://cattlepi.com) is built on the open-source project with the same name hosted here: [CattlePi](https://github.com/cattlepi/cattlepi).  
The [README](https://github.com/cattlepi/cattlepi/blob/master/README.md) in the project root  goes over the details of what the cattlepi vision is.  
It's recommended you go through the README and also the FAQ to build a better understanding. 

In a nutshell, there are 3 major pieces:
 * a Rasperry Pi device 
 * a backend API that stores the configuration for all Pi devices you want to control
 * image files that are used both in the boot process and as root filesystem for the managed Pi

When leveraging cattlepi, one can: 
 * decide to build and serve the images along with implementing the API.
 * build and serve the images while leveraging a prebuild API
 * leverage prebuild images and a prebuilt API

 `api.cattlepi.com` provides the backend and is also available to standard serve the image files. You can use the api to configure your own devices and even point the boot process to images that you 

 On this website you will find:
  * [Flow]({% link flow.md %}): understand the boot flow and how the loader interacts with the Api
  * [Images]({% link images.md %}): a list of prebuilt images that you can use, out of the box, on your Rapsberry Pis
  * [Api]({% link api.md %}): a description of the api and how to interact with it outside of the boot process
  * [Legal]({% link legal.md %}): privacy and legal concerns if you decide to use the API (basically this is provided as-is without any warranties + we respect your privacy)

You are encouraged to use `api.cattlepi.com` in the beggining, at least until you grasp what cattlepi is.  

Our hope is that you'll find it useful and cattlpi will unlock scenarios that were impossible or hard to manage with a standalone Raspberry Pi.  
**Your feedback is highly appreciated.**
