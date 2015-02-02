---
layout: post
title: Labyrinth Plugin For 3ds Max
description: "A spline generating plug-in I wrote for Joker Martini"
modified: 2013-12-27
tags: [3ds Max, C++, Splines, MAXScript]
image: /assets/images/vinyl.jpg
comments: true
share: true
---

I was asked by [Joker Martini](http://jokermartini.com/) to convert a MAXScript geometry plugin that he wrote into a C++ plugin for performance and functionality reasons.
John Martini came up with the concept for the plugin and had a nearly complete working MAXScript implementation with only a few gotchas.
One of those gotchas was performance for a spline with many knot points.
The other gotcha was getting the generated spline to update properly when the scene node(s) it depended on were updated.
A C++ based plugin solved these issues and allowed for additional functionality as well. Check out these videos to see the plugin in action:

<iframe src="//player.vimeo.com/video/86831419?title=0&amp;byline=0&amp;portrait=0&amp;color=c9ff23" width="500" height="303" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<p/>

<iframe src="//player.vimeo.com/video/86815022?title=0&amp;byline=0&amp;portrait=0&amp;color=c9ff23" width="500" height="303" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

<p/>

You can purchase the plugin [here](http://jokermartini.com/2014/02/17/labyrinth/) if you are interested.

#### Development Details

My first step in prototyping this plugin was to create a skeleton geometry plugin based on *SimpleSpline*.
This class wraps up a lot of the details of a spline plugin including dealing with render time and viewport mesh generation.
Besides the usual boilerplate 3ds Max plugin code (of which there is quite a bit), you really only have to implement a single method to generate the spline geometry: *BuildShape(TimeValue t, BezierShape& ashape)*.

Once I had the plugin drawing a test spline, it was time to see how to get a list of referenced nodes and watch for changes to them.
Luckily, the built-in *ParamBlock2* parameter type *TYPE_NODELISTBOX* will auto-manage a list of nodes and references to those nodes allowing you to receive notifications when the reference changes:

{% highlight C++ %}
static ParamBlockDesc2 theLabyrinthObjectNodeListParmBlock
(
	labyrinth_nodelist_params,		// block id
	_T("LabyrinthNodes"),	     	// internal name string
	0,								// local name string
	&theLabyrinthObjectClassDesc,   // class description
	P_AUTO_CONSTRUCT + P_AUTO_UI,	// flags
	labyrinth_nodelist_params,	    // pblock num
	IDD_NODELIST_PANEL, IDS_NODELIST, 0, 0, NULL,

	//Parameter Specifications ----------------------
	labyrinth_nodelist_node_tab, _T("nodes"), TYPE_INODE_TAB, 0, P_VARIABLE_SIZE, IDS_NODES,
		p_ui, TYPE_NODELISTBOX, IDC_NODE_LIST, IDC_PICK_BUTTON, IDC_REPLACE_BUTTON, IDC_DELETE_BUTTON,
		p_end,
	p_end
);
{% endhighlight %}

Notice that there is auto-ui support for the pick button, replace button, and delete button! As long as you provide the proper dialog IDs, you get all that functionality for free!

Now that the plugin has a node list parameter (thanks to the magic of the *TYPE_NODELISTBOX* parameter type), it will start to receive *REFMSG_CHANGE* notifications in the *NotifyRefChanged()* method when any of the nodes in the list change.
Internally, the parameter block is taking a reference to the nodes that are added to the list and the parameter block itself re-broadcasts the change message downstream to the plugin object.
From the *NotifyRefChanged()*, if we receive *REFMSG_CHANGE* from the node list parameter block we can invalidate the geometry cache and force a spline re-build on demand.
This keeps the generated spline always up to date as nodes and objects change.

With the node list working, it was pretty straightforward to translate the provided reference MAXScript plugin into C++.
I was careful to make sure the array for the points is only reallocated when the point count changes and reused whenever possible to avoid slowdowns.

After the core plugin was implemented and working I wanted to add PFlow support.
Unfortunately, it wasn't super clear to me from the SDK docs how to identify and access a PFlow object's particles. There are a lot of classes with 'Particle' in the name and ParticleObject/SimpleParticleObject was not working for PFLow objects.
After a lot of digging into the header files, here is how I ended up doing it:

{% highlight C++ %}
ObjectState os = pNode->EvalWorldState(t);
Object *pObj = os.obj;
valid = valid & os.Validity(t);
if (pObj->IsParticleSystem())
{
    IParticleObjectExt* pExt = GetParticleObjectExtInterface(pObj);
    if (pExt != 0)
    {
        pExt->UpdateParticles(pNode, t); // need to call this or we get the last frame...
        int pcount = pExt->NumParticles();
        // now process the particles in some way
        for (int i=0; i < pcount; i++)
        {
            Point3* pPoint = pExt->GetParticlePositionByIndex(i);
            // do something with the positions
            ...
        }
    }
    // else this is probably a legacy particle system...
}
{% endhighlight %}

The key here is getting the IParticleObjectExt interface from the object using the macro GetParticleObjectExtInterface(). This object is not really described in the docs as being related to PFlow, but it works, go figure.

Another feature I wanted to add was the ability to generate random points for the spline on the surface of a triangle mesh.
I wanted to generate points evenly based on face area so I came up with this method:

- Compute the area for every triangle and store it in a cumulative distribution array called *cda* (i.e. \[face0's area, (face0's area + face1's area), ...\])
    - Consider each element to actually represent an interval. The value of the array at index *i* represents the interval: cda\[i-1\] -> cda\[i\]
    - So if cda[0] == .1 and cda[1] = 0.3, the index 0 represents the interval (0,0.1\] and index 1 represents the interval (0.1, 0.3\]
- For each point you want to generate:
    - Pick a random number *r* between 0 and 1
    - Generate the random 'area' value *av* to search for by multiplying *r* by the total face area
        - We will search for the interval index in *cda* that contains the value *av*
    - Guess a reasonable search starting index for *cda* by multiplying the random number *r* by the number of faces in the mesh
        - This is just a guess to help get close for the next step...
    - Do a binary search of *cda* looking for the index *i* where the *av* value we generated fits into the implied interval:
        - When cda\[i-1\] < *av* <= cda\[i\] we return *i* and break out of the binary search
    - The index *i* can now be used to index a triangle from the mesh which should now be randomly selected weighted by face area
    - Finally just generate a random but valid Barycentric coordinate to pick a random point on the triangle itself

With this method you can generate any number of random points on a mesh with the only additional overhead of 1 float per triangle/face and no sorting or data copying required.
This is probably not the absolute fastest way to generate random surface points, but it is fast enough, works well, and is easy to implement.













