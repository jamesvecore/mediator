---
layout: post
title: Krakatoa For Softimage Open Sourced
description: "An open source integration of the Krakatoa SR api into Softimage as render engine."
modified: 2013-12-27
tags: [Krakatoa, Softimage, XSI, Rendering, Particles, ICE, C++]
image: /assets/images/blocks.jpg
comments: true
share: true
---

I have open sourced my Krakatoa for Softimage Plugin. The full source code is now available at [github](https://github.com/jamesvecore/KrakatoaForSoftimage).

<iframe src="//player.vimeo.com/video/62377510?title=0&amp;byline=0&amp;portrait=0&amp;color=c9ff23" width="500" height="291" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<p/>

<iframe src="//player.vimeo.com/video/82989087?title=0&amp;byline=0&amp;portrait=0&amp;color=c9ff23" width="500" height="302" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

<p/>

Here are the details from the readme:

This a simple plugin for Softimage that exposes the Krakatoa SR particle renderer from [Thinkbox Software](http://www.thinkboxsoftware.com/) as a renderer in Softimage.
It is a very basic implementation at this point.
This plugin has NOT been tested in production is just intended as a test/example/starting point.

You will need a valid Krakatoa "Render" license from [Thinkbox](http://www.thinkboxsoftware.com/) for this plugin to work.

To build you will also need the Krakatoa SR C++ SDK which can be downloaded from the [Thinkbox website](http://www.thinkboxsoftware.com/krakatoa-sr-downloads/)

Pull requests welcome.

#### Features:

- Renders ICE point clouds with Krakatoa from within Softimage as a native C++ renderer plug-in
- Custom ICE channels mapped to Krakatoa channels (see below)
- Region Render tool support
- Light Groups can be used to control with lights are used by Krakatoa
- Occlusion mesh support
- Multi-channel EXR output support

#### The Following ICE Channels are mapped for Krakatoa if they exist and are not being [optimized away by ICE](http://softimage.wiki.softimage.com/index.php?title=Optimization_of_ICE_Data):

- PointPosition
- Color
- Density
- Lighting
- MBlurTime
- Absorption
- Emission
- PointNormal
- Tangent
- PointVelocity
- Eccentricity
- PhaseEccentricity
- SpecularPower
- SpecularLevel
- DiffuseLevel
- GlintGlossiness
- GlintLevel
- GlintSize
- Specular2Glossiness
- Specular2Level
- Specular2Shift
- SpecularGlossiness
- SpecularShift

