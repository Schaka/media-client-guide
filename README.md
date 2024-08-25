# Media Client - A guide to streaming local media via Jellyfin on a budget

## Introduction

This guide will be focused on **working on a budget and around 1080p content**, but will have mentions of 4K HDR and DV. I think those are great technologies that have been fairly affordable - but not on a budget of a couple hundred dollars for a home theather setup.
If some of your users are on an older 4k device that can only do SDR, this is also for you. CoreELEC and Kodi do a great job tone mapping client side too.
It's also written under the assumption that you have a working Jellyfin server. Making this work for Plex won't be much different - but in the spirit of open source and a tight budget, the focus will not be on Plex.

We're going to try and achieve maximum compatbility for all sound and video codecs. There will be no coverage for playing full discs (although possible includin full menu in Kodi) or how to obtain any media. There are plenty of guides (including my own) for media server management.

## Reasoning
As more time goes on, I constantly see questions about "why does this not play on my Samsung" or "why does this not play on WebOS". There are questions about subtitles, transcoding, about passing through different sound codecs and what's the best player to get. In the spirit of ensuring direct playback compatibility for friends or family members, a device that can do everything without stressing your server is desperately needed.

Amazon is moving away from Android entirely, ads are getting more and more invasive on both Google's and Amazon's players - even Roku's been getting bad headlines lately. 

There's also almost always missing support for DTS and prices are out of hand. The latest FireTV 4K Max supports almost everything but has a still unfixed bug with DV P8 in ExoPlayer and can only pass through the DTS core. 
The $20 ONN Best Buy device is only available in the US - it doesn't do DTS:X or Atmos.

It seems that most official, branded streaming player out of the box have one flaw or another. Even the Zidoo Z9X Pro won't play P7 FEL and the Shield Pro 2019 can't do HDR10+ and suffers from a DV red push that hasn't been fixed in the last 5 years.
But to most people, including myself, having an Android based system is at the very least important for apps such as SmartTube, S0undTV, Spotify or similar apps for music, audiobooks, etc. 
So no matter which player we choose, it should cover all use cases to some extent.

Official streaming apps aren't being considered as a relevant factor.
If that's all you're interested in, get yourself a Chromecast 4K, APTV 4K and a cheap soundbar. You'll be okay. If you're interested in upgrading and enjoying movies and TV shows closer to how their creators intended, read on.

**This is for local media that you own only, regardless of how you acquired it. The focus is on compatibility and playability.**

## Choosing the player
To get full support and compatibility client side, it is best to go with Kodi as a client. With the exception of the Zidoo Z9X Pro, getting Dolby Vision AND all sound codecs to work on Android is nigh impossible. There wil always be some shortcomings and as long as you can run Android alongside an OS like CoreELEC, you won't give up any of the benefits.

If you care about 4K DV/HDR remuxes with P7 FEL, grab the Ugoos Am6b Plus. It has the S922X-J chip which was certified for Dolby Vision and supports HDR10+. Kodi takes care of Atmos, DTS:X and any other Dolby or DTS codec.

For the sake of this guide, where we'll focus on on S904-X4 based boxes. They are essentially all identical, but sold under a few different names.
The most popular are HK1 RBox X4 and Vontar X4. You can usually find them on AliExpress from $30-35 shipped worldwide.
Although we don't care for 1080p SDR TVs, it is fully capable for 4K HDR10+.

## Setting up the player
We will want to dual boot both Android and CoreELEC. There are a few ways to do this, but the main ones are:

1. keep the original Android 11 installation it came with and run CoreELEC from a fast USB .2 thumb drive
2. use HybridELEC, an older image allowing triple boot on eMMC - CoreELEC 21.0 and the official Android image
3. use the image I provide, with an up-to-date stable CoreELEC (21.1) and SlimBOXtv 

If you don't care for Android, just install CoreELEC following the official guide, then transfer it to eMMC.

Not running from eMMC can be potentially slow and running from an SD card or USB thumb drive can kill the flash storage if there are a lot of writes to it.
I've decided to build my own image using SlimBOXtv and CoreELEC, both on eMMC for stability and speed. This has been achieved using [hepacker](https://github.com/HybridELEC/hepacker), the same tool used to build HybridELEC. Special thanks to 7Ji for building and maintaining this great tool.

The decision is up to you and what you value. The cheapest option is definitely the HybridELEC one (no cost for extra drive/SD card). It's also the least well documented one. It also gets rid of the potentially sketchy Chinese Android version and replaces it with a custom ROM.
Keep in mind, this type of installation is not officially supported by CoreELEC. Do NOT contact them for support.

### Installing HybridELEC
To make sure HybridELEC works, make sure you buy the 4G (RAM) and 1 Gbit. There are 2GB and 100Mbit versions of these devices out there. While it's possible to make CoreELEC work on them, HybridELEC images are more constrained.

Go to the [release page](https://github.com/Schaka/media-client-guide/releases).

Download all the pieces of release files. They need to be combined into one and unpacked with 7zip.

Use 7zip to unzip so you have the `*.img` file available. Follow the instructions for Amlogic USB Burning Tool on the [CoreELEC Wiki](https://wiki.coreelec.org/coreelec:aml_usb_tool).

I found that what I had to do was:
- start software, select image and click start after it was validated
- use toothpick inside the audio plug to hold the reset button down
- (plug in power, then right after) plug USB-A from computer into the USB 2.0 (not blue) on the HK1 RBox X4/Vontar X4
- hold the reset button until the flashing progress reaches 10%+

It is possible that plugging in power won't be necessary for you - on one computer USB seemed to supply enough power for me.

## Setting up your new player
Upon starting it, you'll be seeing the logo of the image you flashed, no matter which player you owned before. So don't be scared.
Android should work just as it did before. Install your favorite apps like SmartTube, etc.

Next, download the Rebooter APKs from the android_apk directory in this repository. Put them on a USB drive, connect it to the player, use TotalComander to access the drive and install the apps. You can put the Rebooter on your home screen right away - it's a shortcut for the HybridELEC Rebooter and will open it so you can start CoreELEC.
Use this opportunity to install your favorite Android APKs like SmartTube, S0undTV, Audiobookshelf, etc.

You can now use it to load into CoreELEC. Be sure to have a keyboad ready, as your remote won't be recognized.
Enable SSH in the initial screen where it prompts you to. Get an SSH client like PuTTY and connect to the host.
The hostname should be coreelec, the user `root` and the password `coreelec` again. Refer to the [SSH page](https://wiki.coreelec.org/coreelec:ssh) if you need more help.

Download your `remote.conf` file [here](https://github.com/CoreELEC/remotes/tree/master/AmRemote).
I found the [HK1RBox file](https://github.com/CoreELEC/remotes/blob/master/AmRemote/HK1RBox/remote.conf) works best.
Copy all its contents. Now in PuTTY, use `nano /storage/.config/remote.conf`, paste its contents and hit Ctrl+X, then confirm via Y + Enter.
Upon rebooting, your remote should be recognized.

## Using your player
First, I recommend [following the instructions to install Jellyfin for Kodi](https://jellyfin.org/docs/general/clients/kodi/).
I found that using the JellyCon plugin would either flat out not work or just be more complicated. This player should become a dedicated streaming anyway, so there shouldn't be any mixing of local media and Jellyfin supplied files.

Set up your streaming settings in both Kodi and Jellyfin for Kodi as you please. There's no simple guide to follow - you need to adjust it to your needs.
If you have hardware that supports lossless audio, make it match the codecs. Same goes for your transcode settings.

While not required, I highly recommend going to Addons => Official Kodi Repository => Program Addons => Up Next.
It's a very useful addon for playing the next episode. 

### Settings

Settings => System => Audio

- GUI Sounds - None. I find it very annoying it clicks on each button press but ymmv
- Audio Output Device - Set to HDMI for typical setup
- Allow Passthrough - Set whatever passthrough audio codecs for whatever your TV/Receiver supports. If you have AVR that supports everything, DTS and TrueHD should be checked

Settings => System => Input => Addons

- Unknown sources - check
- Update official addons from - Any repo. Helps for a lot of skins

Settings => System => CoreElec

- Disable noise reduction - check

Settings => Services => Caching

- Buffer mode - cache settings to all methods: smb, local, remote, etc (default ignores local playback)
- Read factor - Set 'Adaptive' readfactor (default is 4x).
- Memory size - Set 64mb cache size (default is only 20mb). If unchanged from 20Mb, I found it gives the 'low-speed connection' warning when playing high-bitrate 4K. Set a bit higher to prevent warning.

Settings => Player => Videos

- Accelerate VC-1 - Exclude 24fps (small bug in amlogic hw acceleration. this should be done on software instead)
- HW acceleration set to always for other codecs - Default does not accelerate mpeg-4

Settings => CoreElec => Services

- eMMC Speed Mode - HS200, HS400. Enables blazing fast performance on eMMC.


### Skin

Next, we need to de-uglify Kodi so it's usable. Both for you or whatever family member or friend you're setting up the cheap player for.
There are a few skins worth considering:
- [Arctic Horizon 2](https://forum.kodi.tv/showthread.php?tid=367352)
- [Arctic Fuse](https://forum.kodi.tv/showthread.php?tid=373859)
- [Arctic Zephyr Reloaded](https://forum.kodi.tv/showthread.php?tid=337862)

Take a look at which one you like best, follow the instructions and install it.

If instructions are unclear, it usually goes like this:
- open Settings and File Manager
- add a new file source, this points towards a Kodi repository that you need to add
- go to Addons and Install from Zip, choose the repository as a location
- install a new addon from the now available repository

I personally recommend Arctic Fuse. It's a level above the rest. 

Afterwards, go to Settings => Interface => Skin and configure your widgets.
I recommend adding the addon "CoreELEC Configuration" and the Kodi Command "Reboot to Android" to your Options menu (the very menu that lets you select Settings).

## Enjoying your player
There are plenty of tweaks to be made. You can probably sink countless hours into it if you wanted to.
But this basic setup should really be good enough. Don't spend too much time tinkering before you haven't enjoyed it a little bit.

Jellyfin should have synced your entire library by now. Watch some of your favorite movies as they were intended be seen. Enjoy you've only ever experienced transcoded and down-mixed.

You've probably spent hours following this guide already - take a break and enjoy the fruits of your labor.

## Dolby Vision support
Keep an eye out on [this thread](https://discourse.coreelec.org/t/learning-about-dolby-vision-and-coreelec-development/50998/249). This CoreELEC user is working on making Dolby Vision work via TV LED on [non-licensed devices](https://discourse.coreelec.org/t/learning-about-dolby-vision-and-coreelec-development/50998/262).

If this ever goes anywhere and may even make it to upstream releases, it could make this player extremely valuable.

## Home theather setup
To be continued...

There will be a helpful guide on how to do a hometheater sound system on a budget, using used parts here.
