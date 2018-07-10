---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---
[cattlepi.com](https://cattlepi.com) is built on the open-source project with the same name hosted here: [CattlePi](https://github.com/cattlepi/cattlepi).  
The [README](https://github.com/cattlepi/cattlepi/blob/master/README.md) in the project root gives the vision behind the cattlepi project.  
We recommended you go through the README and also the FAQ to build a better understanding. 

In a nutshell, there are three major pieces:
 * a Rasperry Pi device 
 * a backend API that stores the configuration for all Raspberry Pi devices you want to control
 * image files that are used, both in the boot process, and as the root filesystem for the managed Raspberry Pi

When leveraging cattlepi, one can: 
 * choose to build and serve the images along with implementing the API
 * build and serve the images while leveraging a prebuilt API
 * leverage prebuilt images and a prebuilt API

 `api.cattlepi.com` provides the back-end and is also available to serve the standard image files. You can use the API to configure your own devices. You can even point the boot process to images that you 

 On this website you will find:
  * [Flow](flow.md): understand the boot flow and how the loader interacts with the API 
  * [Images](images.md): a list of prebuilt images that you can use, out of the box, on your Rapsberry Pis
  * [Api](api.md): a description of the API, and how to interact with it outside of the boot process
  * [Legal](legal.md): privacy and legal concerns if you decide to use the API (basically: this is provided as-is without any warranties + we respect your privacy)

You are encouraged to use `api.cattlepi.com` in the beginning. At least, until you grasp what cattlepi is.  

Our hope is that you'll find it useful and cattlepi will unlock scenarios that were impossible or hard to manage with a standalone Raspberry Pi.  

**Your feedback is greatly appreciated.**
