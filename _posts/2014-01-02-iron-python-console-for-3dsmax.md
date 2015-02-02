---
layout: post
title: Iron Python Console for 3ds Max
description: "This is an interactive IronPython console written in C# connected to 3ds Max's .NET API"
modified: 2013-01-02
tags: [IronPython, Python, 3ds Max, C#, WPF, .NET]
image: /assets/images/fog.jpg
comments: true
share: true
---

This is quick video showing a WPF IronPython console I wrote for 3ds Max a while back:

<iframe src="//player.vimeo.com/video/83206128?title=0&amp;byline=0&amp;portrait=0&amp;color=c9ff23" width="500" height="302" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<p/>

The purpose of this project was to be able to be interactively probe 3ds Max's .NET API as well as other .NET based tools inside 3ds Max. Technically you can do most of that directly from MaxScript, but I prefer IronPython since you get to use the Python language and access the CLR types in a more straight forward manner.

This was also just a good excuse to play with the IronPython C# embedding API as well mess with WPF some more. With 3ds Max 2014 now having direct support for CPython, this is a little less useful, but I thought I would still share it anyways.