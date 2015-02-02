---
layout: post
title: Hierarchical Render Pass Manager for 3d Max
description: "A Hierarchical Render Pass Manager for 3ds Max built on the 3ds Max .NET API"
modified: 2014-01-02
tags: [3ds Max, C#, WPF, .NET, Pass Manager, MAXScript]
image: /assets/images/mist.jpg
comments: true
share: true
---

Here is a video demonstrating a Hierarchical Render Pass Manager for 3ds Max that I created:

<iframe src="//player.vimeo.com/video/83860989?title=0&amp;byline=0&amp;portrait=0&amp;color=c9ff23" width="500" height="302" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<p/>

I created Hierarchical Pass Manager for 3ds Max to ensure that anyone in the pipeline could pick up a scene file and be able to submit a correct render to the farm without having to know the details of how the scene needed to be setup to generate correct passes for compositing. Our previous batch system helped move us toward this goal, but relied on Scene States which are both buggy and hard to manage/update as the scene evolves. I was also not happy with any other existing pass manager solutions so I wrote my own.
It was created in C# and WPF using the .NET API for 3ds Max (2012+) with some MAXScript shimming to get it integrated deeply.

#### Key Features:
- Hierarchical pass system where nested passes inherit customizations from their parent passes.
- Plugin based customization system where small customization can be combined to define a pass setup and new customizations can be created quickly and easily.
- Object Sets system to define a higher-level abstraction for grouping geometry to apply customizations to (like object properties or V-Ray properties).
- Auto generated and auto versioned viewport previews and render outputs, no need to ever enter a render output path.
- V-Ray support with customizations for V-Ray frame buffer outputs, render elements, render settings, V-Ray properties, etc.
- Custom viewport preview integration with overlays for frame, camera, pass, scene file
- Pre-submit checks with both C# and MAXScript support to ensure bad renders don't get submitted.
- Batch farm submission with a single shared scene copy for multiple passes
- PDPlayer integration for viewing the last render or viewport preview
- Standalone editing and submission of passes without needing to launch 3ds Max at all!