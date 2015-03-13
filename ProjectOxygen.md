

# Project: Oxygen #

Since the Google acquisition of SageTV, many have been wondering how will we replace our extender devices when they inevitably die?

The focus of the community, myself included, has been to find a device and somehow make it look, feel, and/or act like a SageTV HDx00 extender device.  These efforts have come with some degree of success, such as the [Boxee](http://code.google.com/p/sagetv-boxee/) and [Plex](http://code.google.com/p/sagetv-for-plexmediacenter/) projects.

But frustrations tend to quickly arise when you attempt to take a device meant for one purpose and try to make it do exactly what the Sage extenders do... it's simply not fun and becomes tiresome really fast.  It wouldn't be too bad if the public APIs of devices like the Boxee were _wide_ open, mature, and feature rich.  At least in my opinion, these devices are far from such openness and API maturity.

So here's my latest take on the problem, currently code named "Project: Oxygen".

# Extender Replacement Wish List #

After really studying **_my_** SageTV usage patterns over the last few months, I've come to learn a few things about how the people in my house use SageTV and what's important to **_us_** when the time comes to replace my HDx00 extenders.

  1. Most importantly I just want a simple device that attaches to my tv via HDMI and is capable of playing back all of my recorded content smoothly.  I don't want to fuss with PCs at each tv.
  1. Watch status synchronization.  I knew this was the key to any extender replacement from the start.  This **_is_** the deal breaker for me.  Any device I use to watch Sage TV recordings must update the Sage server with a watched status when I delete videos from the device.  Without this capability Sage will record shows that have already been recorded when it shouldn't.  This item and the one above, as it turns out, are the **_only_** requirements that truly matter to me when looking at an extender replacement.
  1. Comskip support.  If a device didn't process edl files then it's not a deal breaker (for me), but this is definitely something I would much rather have than not have.
  1. I'd love full SageTV management capabilities on this device, but I have yet to see a device with an API capable of duplicating most, if any, of the SageTV management features (EPG, settings, favs manager, and so on) easily.  The Boxee effort shows a solid starting point of what's possible, but to say getting it to that stage was a bit frustrating would be an understatement.  To actually duplicate the SageTV UI and all its capabilities on Boxee - never going to happen, imho.
    * And it turns out, after some studying of my habits, that I don't really manage my SageTV server from extenders too often.  I mean, I do sometimes because I can, but most of the time I manage my settings and favourites from the web UI, especially since recent web UI updates added plugin manger support and other features meaning I can do more from the web and need the extenders to do less.  I've learned that I'm ok with this and if I lost all my extenders I could easily setup favourites from the web and as long as I can browse and watch my recordings from some third party device then I'm more than happy to manage the server from the web, or if necessary via a Placeshifter connection from my laptop, etc.
  1. I do not watch live tv, ever.  I just don't.  No one in this house does.  So supporting live tv/channel surfing just isn't something I'm worried about.  The only time I do is sports, but even then I timeshift it by 60-90 minutes.
  1. Turns out I don't care about fanart.  If the device supported it and showed it I'd be ok with it, but if it didn't I really wouldn't care either.
  1. Metadata: The jury's still out on this one.  I can't say for sure I don't **_need_** it because it's just always there.  I think it's probably the same as comskip.  I'd much rather have the metadata for my recordings as I browse through them, but if I could only browse through a file system of recordings and pick something to watch then I could probably live with it.
    * Season/episode number, episode title and description are the ones I'd probably like to have and everything else is a bonus.

# My Solution: Let's Teach an Old Dog New Tricks #

So here's my plan: Instead of taking the latest and greatest consumer device for the tv and trying to modify it or write an app for it that makes it act like an extender - a task that, imho, is just too much effort for usually less than desirable results - let's make Sage provide some kind of mechanism that allows most, if not all, media streaming devices to attach and provide **_minimal_** Sage client support.  This way I can attach any device I want and use it to do the bare necessity of SageTV tasks: watch my tv recordings.  And it turns out that for **_me_**, this is actually all I really want an extender to do.

# So What's the Plan? #

Great question! Turns out it's rather simple.  Most any media player (standalone devices, not DLNA capable TVs) can load and play back a playlist.  And it's rather trivial to get SageTV to dump it's list of recordings into a series of playlist files that these devices are usually more than happy to playback.  And there you have it, I'm simply going to dynamically create standard playlist files that most devices will happily playback.  Simple, eh?

# Why Playlist Files? #

Another great question!  Two big problems I identified with just sharing my recording directories on the LAN and pointing these devices at them:

  1. Sage records to a single directory, so a typical user would possibly have to browse through dozens and dozens, if not hundreds, of files to find things of interest.
  1. Sage recording files aren't necessarily named all that well and they certainly aren't organized all that well.
  1. Recordings can be stored on multiple drives, meaning a user has to know which drive to look on in order to find a recording of interest - yucky!

So instead I'm going to make Sage maintain a mirror of playlist files organized with folders making it much more appealing to browse and find content of interest.  Even better, this hierarchy of playlist files is going to be built by using the metadata available to make a logical structure to browse through.  Here's a sample of what I'm creating:

```
\\server\playback\
   30 Rock\
      Season 06\
         30 Rock-S06E03-Idiots Are People Three!-11881907.m3u
         30 Rock-S06E04-The Ballad of Kenneth Parcell-11881909.m3u
   House of Lies\
      Season 01\
         House of Lies-S01E03-Microphallus-11855765.m3u
   The Colbert Report\
      Season 08\
         The Colbert Report-S08E50-Terry Gross-11872712.m3u
```

Then simply share the `\\server\playback\` folder on your LAN and point your media playback device at this share.  Each playlist file simply points to the actual Sage recording file - just make sure all of those directories are also shared on the LAN.  Then just browse this conveniently organized structure of playlists and playback whatever you want.

# And How Are You Synchronizing Watch Status? #

Here's the magic... Sage will do it for you!  The plugin I've written will monitor your mirror of playlist files and anytime a playlist is deleted, Sage will then immediately go and mark the corresponding media file as watched in `wiz.bin`.  Optionally, the plugin can also immediately delete the underlying recording from Sage as well.  Since Sage is doing all the heavy lifting, there's really no need to write any specialized apps/plugins/etc. for the media devices - assuming any such device can load and playback an `m3u` playlist file - and most can.

# Comskip Support? #

Well, I've been testing with XBMC and Boxee (PC) and since the playlists just point to the media files on the LAN, if there's an `edl` sitting along side the recording then these packages will process and use them.  Basically, if the device you're using will process `edl` files then they should be supported.  If your device doesn't then you're out of luck.

# Fanart/Metadata Support? #

Well, this will be device dependent.  And here's what I know:

  * **XBMC (10.1):** When you add the share of playlists as source in XBMC and tell it it's a tv source then XBMC goes and scrapes the _files listed inside the playlist files_ and does **not** scrape the playlist file name.  This is unfortunate.  If XBMC actually scraped the playlist file name then we'd be set.  Good news: Someone could write a patch for XBMC to have it scrape playlist names instead of the contents of the playlists.  But XBMC does scrape the folder structure and at least added fanart for all of my shows at the folder level.
  * **Boxee:** Boxee reacted a little different.  It didn't scrape anything from anywhere.  But when I browse to a folder containing playlist files and click on one and told it what show it was it then went and scraped metadata for all the episodes in the folder and added the show to the TV Shows area in Boxee.  So if you tell Boxee what to scrape then it actually does it, but it has to be told manually, which I thought was weird.  But once you did, you got full episode metadata in Boxee.

# Other Notes #

  * **Multi-segment recordings:** Fully supported because I simply add each segment to the playlist file I create for the recording.
  * **Live TV:** Not supported.  XBMC and Boxee both only play whatever content is available when you start playback.  If the devices/software packages recognized an active recording then it would be supported (to some extent), but I have yet to see a device that actually handles these active recordings properly.  Because of this, the Sage plugin doesn't create a playlist for a recording until the recording has ended.

# Device Testing #

I'm still developing the plugin and so have limited testing to PC software only, namely XBMC and Boxee.  However, my target devices are the Boxee device and the WDTV Live HD.  I'm especially interested in testing this on the WDTV device because it can be had for about half the cost of the Boxee box.  Once the plugin is a little more stable I'm going to go out and get my hands on these devices for some testing.  I expect the Boxee to perform about the same as the PC version and am curious to see how the WDTV device performs.

# Project Goals #

So a quick summary of what I'm trying to solve here:

  * Provide convenient playback of SageTV recordings to as many devices as possible through the use of standard playlist files
  * Maintain watch status synchronization between these third party devices and the SageTV server
  * Where devices support it, provide the ability to access and process `edl` or other comskip type files
  * Where devices support it, make it as easy as possible for devices to scrape the recordings for metadata.

# Project Anti-Goals #

Along with its goals, this project has a few clearly stated "anti-goals" or things that I don't expect (or even want) this project to achieve.

  * This is **NOT** a Sage extender replacement; I'm not trying to replicate **any** of the functionality of a SageTV extender.
  * I do not plan on ever supporting live tv; it's just not a feature I use or care about.  Providing access to active recording files is definitely possible, but most (all?) devices simply cannot handle media files that are actively recording.
  * I am not providing any management functionality (EPG, favs, server settings, plugin management, etc.).  That's what the web UI is for.  I'm only concerned about being able to playback my content easily when my extenders die.

If any of the features I talk about in the anti-goals are important to you then this project is probably of little, if any, use to you.  But if you just want to be able to (relatively) easily browse your recordings and play them back on a small, inexpensive media device on your tv then this project should be able to help you.

# Hey, make no mistake, extenders are king! #

Hey, I **love** my extenders and plan on using them to the bitter end of my SageTV deployment.  This project is my answer to playback concerns should they die.  In reality, I'm hoping I never have to deploy this solution, but if all my extenders do die then I feel better knowing I have a plan that works for **_me_**.  I realize many people aren't even going to be bothered with this plugin because it doesn't provide some feature that extenders do.  I get it, I understand it and trust me I don't really want to ever have to deploy this, but after months of monitoring how **_I_** use SageTV, this is my solution going forward in case I need to replace extenders.  If this solution fits into your usage patterns for SageTV then consider this a plausible alternative when your time comes to deploy extender replacements.

# What's up with the project code name? #

Call it an attempt at creativity. :)  You may notice that all of my Sage plugins have boring names so I went creative this time.  Why 'Oxygen'?  Well, the goal of this project is to provide the most minimal Sage client possible, providing just the bare necessities required to playback Sage recordings on the LAN.  Well, a human's most basic need is oxygen.  Ahh... I thought it was fitting. ;)

You could also say that this project is an attempt to breathe new life into the SageTV platform by providing a minimal functioning client.