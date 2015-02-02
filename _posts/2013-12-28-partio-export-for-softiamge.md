---
layout: post
title: Partio Export For Softimage
description: "An open source project that uses the Partio library to export particles from Softimage."
modified: 2013-12-28
tags: [Krakatoa, Softimage, XSI, Rendering, Particles, ICE, Partio, C++]
image: /assets/images/feed.jpg
comments: true
share: true
---

I have open sourced my Partio Exporter for Softimage. The full source code is now available at [github](https://github.com/jamesvecore/PartioExportForSoftimage).

<iframe src="//player.vimeo.com/video/82982792?title=0&amp;byline=0&amp;portrait=0&amp;color=c9ff23" width="500" height="302" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<p/>

Here are the details from the readme:

This is a particle export plugin using the [Partio](http://www.disneyanimation.com/technology/partio.html) library for [Autodesk Softimage](http://www.autodesk.com/products/autodesk-softimage)

#### To Build:

Use [cmake](http://www.cmake.org/cmake/resources/software.html) to generate a VS solution file and build.

#### To Use:

- Load the plugin from the plugin manager ("File" -> "Plugin Manager").
- Open the cache manager ("View" -> "Animation" -> "Cache Manager").
- Select the "Write" tab.
- Set the format to "CUSTOM" and put in one of the supported file extensions in.
- Make sure to modify the file name template to something like "[object]_[version]_[frame #4]" to get the padding on frame numbers.
- Add your point cloud object to the objects list.
- Set your scene range and channels to export in the "Additional Options" tab.
- Hit the "Write Cache" button.

#### IMPORTANT note about missing channels:

If your channels are not showing up in your particle files you need to be aware of how ICE optimizes data. Even if you write to an attribute explicitly with Set Data, if it is not used, it will be optimized away automatically by ICE and will not be exported.
See [here](http://softimage.wiki.softimage.com/index.php?title=Optimization_of_ICE_Data) for details and workarounds.

#### Supported Formats:

Only exporting is supported and not all Partio formats supported. The following formats are supported:

- BGEO
- BIN[^1]
- GEO
- PDA
- PDB
- PDC
- PRT

I tried to do some default channel name mapping for known channel types so that data works in the resulting format as expected.
See the code for mapping details. The specifics might need adjusting depending on your format choice and end software choice.

-------------
[^1]: if you plan on using the .BIN format, please see [my pull request](https://github.com/wdas/partio/pull/36) for the partio library which fixes the .BIN exporter. The current version in partio/master does not work properly and will crash most programs that try to read .bin files written from partio. My pull request has been ignored so you will have to merge and re-build partio manually or just clone [my fork of partio](https://github.com/jamesvecore/partio) directly.
