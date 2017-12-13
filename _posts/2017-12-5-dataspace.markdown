---
layout: post
title:  "DataSpace"
date:   2017-12-5 10:00:00 -0500
categories: virtual-reality data-visualization
comments: true
---
<iframe width="560" height="315" src="https://www.youtube.com/embed/IUu1pJK793I" frameborder="0" gesture="media" allow="encrypted-media" allowfullscreen></iframe>

When I first heard about the Oculus Rift, I was excited about the opportunity VR would bring to data visualization. We would no longer be bound to keyboard and mouse, or to the 2D plane of a monitor. We would occupy the same space as our creations and truly interact with anything we could imagine.

I can plot 3D charts on a monitor with no problem, but keeping track of the patterns I see can be challenging sometimes, especially when incorporating higher dimensions via attributes like color or size. With all of the VR development resources, I figured that it would be relatively simple to implement a useful data visualization application. I waited patiently for the first app to appear, but alas, it never came. Most data visualizations were no more than a 2D chart floating in a virtual room, or a line-chart turned into a roller-coaster. Fun to look at, sure, but far from useful. No one was really thinking about these applications as tools, but as “experiences” or entertainment. The one application that seemed to be intended for scientific use was very buggy and difficult to use.

That’s why I built DataSpace. It’s the only hardcore data visualization app for modern virtual reality headsets that I am aware of (though I haven’t looked too hard since I started building DataSpace). Bear in mind that this was my first real foray into Unity/C# and game development in general. There are a lot of rough edges. For example, the UI/UX should really be reworked, but it’s also the part that I am least interested in working on.

At the moment, my app only allows for the creation of a 3-dimensional scatter plot. The user can map data from a CSV file to the three spatial dimensions, one color dimension (a red-green gradient), and a time dimension.

In this blog post, I use an n-body simulation ([github repo](https://github.com/dan-nadler/n-body)) as an example. I ran a simulation with 500 bodies of equal mass and radius at psuedo-random points along the outside of a sphere. The bodies interact using Newtonian gravity, and merge if the bodies touch. This isn’t a 100% physically accurate simulation, just something I did for fun that turned out to be useful as an example.

The simulation outputs a CSV file with columns for the position, mass, time, and body ID. Each row represents the mass and location of a specific body at a specific time. This output file is about 12.5 MB (about 192,000 rows). I initially ran into issues drawing this much data, but I was able to use some video game optimization techniques to make it run quite smoothly. At this point, the only real bottleneck is the load time when I have to redraw the entire plot (for example, when I reassign axes or initialize the scatter plot room).

Here’s the loader menu (you’ll notice that I’m running this in the Unity editor, but I have an alpha version on Oculus Home as well):

![file_select]({{site.url}}/assets/2017-12-5-dataspace/file-select.jpg){:class="img-responsive"}

![csv_loader]({{site.url}}/assets/2017-12-5-dataspace/csv-loader.jpg){:class="img-responsive small-img"}

The menu provides a default mapping, but you can specify the initial mapping however you want. You can also change this on-the-fly once you’re in the scatter plot room.

![axis_select]({{site.url}}/assets/2017-12-5-dataspace/axis-select.jpg){:class="img-responsive"}

Click "Load Scatter Room," and voila:

![sphere_loaded]({{site.url}}/assets/2017-12-5-dataspace/sphere-loaded.jpg){:class="img-responsive small-img"}

Once you’re in the scatter plot room, you can access an in-game menu that appears over the left controller. You can manipulate the menu with the left joystick, toggle the menu visibility with “Y”, and redraw the data with “A”. Holding down the left trigger will let you access the Scale and Opacity variables:

![left_hand_menu]({{site.url}}/assets/2017-12-5-dataspace/left-hand-menu.jpg){:class="img-responsive small-img"}

A smaller version of the axes appears over the right controller for reference (toggle with "B"). They rotate with the main axes, but do not scale or stretch.:

![right_hand_axis]({{site.url}}/assets/2017-12-5-dataspace/right-hand-axis.jpg){:class="img-responsive small-img"}

You can scroll through the time variable with the right joystick (squeeze the right trigger to go faster):

<iframe width="560" height="315" src="https://www.youtube.com/embed/rRlsQmTmpVw" frameborder="0" allowfullscreen></iframe>

The current time variable is displayed above the right hand:

![right_hand_time_index]({{site.url}}/assets/2017-12-5-dataspace/right-hand-time-index.jpg){:class="img-responsive"}

There are a couple other major features that I demonstrate in the video at the top of this post, including:
-	Moving and resizing the plot by grabbing it
-	Identifying data items by pointing at them (click the joystick to enable the laser pointer, then use your index finger to point at the data)

Some things I’d like to add/improve in the future are:
-	Better UI & UX, including more intuitive menus
-	Basic statistical tools
    -	Regression
    -	Clustering
-	Annotations
    -	Draw, highlight, and label
    -	Take screenshots while wearing the headset
-	Save and load

Do you have a use case for something like this? Let me know; I’m really interested to see if anyone else would find this useful. I’ve mostly used it for fun visualizations, but I did find it fairly helpful for quickly exploring a dataset from the Two Sigma Kaggle competition.
