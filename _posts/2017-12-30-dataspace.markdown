---
layout: post
title:  "DataSpace"
date:   2099-11-23 20:00:00 -0500
categories: virtual-reality data-visualization
comments: true
---
![scatter_overview]({{site.url}}/assets/2017-12-30-dataspace/scatter-overview.jpg){:class="img-responsive small-img"}

When I first heard about the Oculus Rift, I was very excited about what that technology would enable us to do. We wouldn't be bound to the keyboard and mouse, or to the 2D plane of a monitor. We would be able to occupy the same space as our creations and truly interact with anything we could imagine.<!--more-->

One thing that I was really looking forward to was a true 3-dimensional data visualization tool. You can plot 3D charts on a monitor with no problem, but keeping track of the patterns you see (especially when incorporating higher dimensions via things like color or size) can sometimes become quite challenging. With all of the VR resources, I figured that it would be relatively simple to implement a data visualization application. I waited patiently for the first app to appear, but it never came. Most data visualizations were no more than a 2D chart floating in a virtual room. Or a line-chart turned into a roller-coaster. Fun to look at, sure, but far from useful. No one was really thinking about these applications as tools, but as "experiences" or entertainment. The one applciation that seemed to be intended for scientific use was very buggy, and difficult to use.

That’s why I built DataSpace. This, from what I have found so far, is the first hard-core data visualization app for modern virtual reality HMDs. Bear in mind that this was my first real foray into Unity/C# (and game development in general), so there are a lot of rough edges (the UI/UX should really be reworked, but it's also the part that I am least interested in working on).

At the moment, my app only allows for the creation of a 3-dimensional scatter plot. The user can map data from a CSV file to the three spacial dimensions, one color dimension (a red-green gradient), and a time dimension. You can load up a CSV file, choose variables to map to the various dimensions, and then litterally walk around my data.

In this blog post, I use an n-body simulation as an example. In this simulation, I initialize 500 bodies of equal mass and radius at psuedo-random points along the outside of a sphere. The bodies interact using Newtonian gravity, and merge if the bodies in any way. This isn't a 100% physically accurate simulation, just something else that I did for fun. 

The CSV file has columns for the position, mass, time, and body id. Each row represents the mass and location of a specific body at a specific time. The file I load here is about 12.5 MB (about 192,000 rows). I initially ran into issues drawing this much data, but was able to use some video game optimization techniques to make it run quite smoothly. At this point, the only real bottleneck is the load time when I have to redraw the entire plot (when I reassign axes or initialize the scatter plot room, for example).

Here's the loader menu (you'll notice that I'm running this in the Unity editor):

![file_select]({{site.url}}/assets/2017-12-30-dataspace/file-select.jpg){:class="img-responsive"}

![csv_loader]({{site.url}}/assets/2017-12-30-dataspace/csv-loader.jpg){:class="img-responsive small-img"}

It gives you a default mapping, but you can specify the inital mapping however you want. You can also change this on-the-fly once the data is plotted.

![axis_select]({{site.url}}/assets/2017-12-30-dataspace/axis-select.jpg){:class="img-responsive"}

Click "Load Scatter Room," and voila:

![sphere_loaded]({{site.url}}/assets/2017-12-30-dataspace/sphere-loaded.jpg){:class="img-responsive small-img"}

Once you're in the scatter plot room, you can access an in-game menu that appears over the left controller. You can manipulate the menu with the left joystick, toggle the menu visibility with "Y", and redraw the data with "A". Holding down the left trigger will let you access the Scale and Opacity variables:

![left_hand_menu]({{site.url}}/assets/2017-12-30-dataspace/left-hand-menu.jpg){:class="img-responsive small-img"}

Move and resize the plot by grabbing it:

**TODO: Transfrom and Rotate**

A smaller version of the axes appears over the right controller for reference (toggle with "B"):

![right_hand_axis]({{site.url}}/assets/2017-12-30-dataspace/right-hand-axis.jpg){:class="img-responsive small-img"}

You can scroll through the time variable with the right joystick (squeeze the right trigger go faster):

<iframe width="560" height="315" src="https://www.youtube.com/embed/rRlsQmTmpVw" frameborder="0" allowfullscreen></iframe>

The current time variable is displayed above the right hand:

![right_hand_time_index]({{site.url}}/assets/2017-12-30-dataspace/right-hand-time-index.jpg){:class="img-responsive"}

You can also identify data items by pointing at them (click the joystick to enable the laser pointer, then use your index finger to point at the data):

**TODO: Data Popup**

Do you have a use case for something like this? Let me know, I'm really interested to see if anyone else would find something like this useful. I've mostly used it for fun visualizations, but I did find it fairly useful for quickly exploring a dataset from the Two Sigma Kaggle competition.

Also, if you're interested in trying it out, please feel free to shoot me an email. The app only supports the Rift at the moment, and you will need Oculus Home to download the application.
