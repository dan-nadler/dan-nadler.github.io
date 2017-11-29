---
layout: post
title:  "DataSpace"
date:   2017-12-23 20:00:00 -0500
categories: virtual-reality data-visualization
comments: true
---
<iframe width="560" height="315" src="https://www.youtube.com/embed/IUu1pJK793I" frameborder="0" gesture="media" allow="encrypted-media" allowfullscreen></iframe>

When I first heard about the Oculus Rift, I was very excited about what VR would enable us to do. We wouldn't be bound to the keyboard and mouse, or to the 2D plane of a monitor. We would be able to occupy the same space as our creations and truly interact with anything we could imagine.<!--more-->

One thing in particular that I was looking forward to was a true 3-dimensional data visualization tool. I can plot 3D charts on a monitor with no problem, but keeping track of the patterns I see (especially when incorporating higher dimensions via attributes like color or size) can be challenging sometimes. With all of the VR development resources, I figured that it would be relatively simple to implement a data visualization application. I waited patiently for the first app to appear, but it never came. Most data visualizations were no more than a 2D chart floating in a virtual room. Or a line-chart turned into a roller-coaster. Fun to look at, sure, but far from useful. No one was really thinking about these applications as tools, but as "experiences" or entertainment. The one applciation that seemed to be intended for scientific use was very buggy, and difficult to use.

That’s why I built DataSpace. It's the only hard-core data visualization app for modern virtual reality headsets that I am aware of. Bear in mind that this was my first real foray into Unity/C# (and game development in general), so there are a lot of rough edges (the UI/UX should really be reworked, but it's also the part that I am least interested in working on).

At the moment, my app only allows for the creation of a 3-dimensional scatter plot. The user can map data from a CSV file to the three spacial dimensions, one color dimension (a red-green gradient), and a time dimension. 

In this blog post, I use an n-body simulation ([github repo](https://github.com/dan-nadler/n-body)) as an example. I ran a the simulation with 500 bodies of equal mass and radius at psuedo-random points along the outside of a sphere. The bodies interact using Newtonian gravity, and merge if the bodies touch. This isn't a 100% physically accurate simulation, just something I did for fun that turned out to be useful as an example.

The simulation outputs a CSV file with columns for the position, mass, time, and body id. Each row represents the mass and location of a specific body at a specific time. This output file is about 12.5 MB (about 192,000 rows). I initially ran into issues drawing this much data, but was able to use some video game optimization techniques to make it run quite smoothly. At this point, the only real bottleneck is the load time when I have to redraw the entire plot (for example, when I reassign axes or initialize the scatter plot room).

Here's the loader menu (you'll notice that I'm running this in the Unity editor, but I have an alpha version on Oculus Home as well):

![file_select]({{site.url}}/assets/2017-12-30-dataspace/file-select.jpg){:class="img-responsive"}

![csv_loader]({{site.url}}/assets/2017-12-30-dataspace/csv-loader.jpg){:class="img-responsive small-img"}

The menu provides a default mapping, but you can specify the inital mapping however you want. You can also change this on-the-fly once you're in the scatter plot room.

![axis_select]({{site.url}}/assets/2017-12-30-dataspace/axis-select.jpg){:class="img-responsive"}

Click "Load Scatter Room," and voila:

![sphere_loaded]({{site.url}}/assets/2017-12-30-dataspace/sphere-loaded.jpg){:class="img-responsive small-img"}

Once you're in the scatter plot room, you can access an in-game menu that appears over the left controller. You can manipulate the menu with the left joystick, toggle the menu visibility with "Y", and redraw the data with "A". Holding down the left trigger will let you access the Scale and Opacity variables:

![left_hand_menu]({{site.url}}/assets/2017-12-30-dataspace/left-hand-menu.jpg){:class="img-responsive small-img"}

A smaller version of the axes appears over the right controller for reference (toggle with "B"):

![right_hand_axis]({{site.url}}/assets/2017-12-30-dataspace/right-hand-axis.jpg){:class="img-responsive small-img"}

You can scroll through the time variable with the right joystick (squeeze the right trigger go faster):

<iframe width="560" height="315" src="https://www.youtube.com/embed/rRlsQmTmpVw" frameborder="0" allowfullscreen></iframe>

The current time variable is displayed above the right hand:

![right_hand_time_index]({{site.url}}/assets/2017-12-30-dataspace/right-hand-time-index.jpg){:class="img-responsive"}

There are a couple other major features that I demonstrate in the video at the top of this post, including:
- Move and resize the plot by grabbing it
- You can also identify data items by pointing at them (click the joystick to enable the laser pointer, then use your index finger to point at the data)

Some things I'd like to add/improve are:
- Better UI & UX, including more intuitive menus
- Statistical tools
    - Regression
    - Clustering
- Annotations
    - Draw, highlight, and label
    - Take screenshots while wearing the headset
- Save, and load

Do you have a use case for something like this? Let me know, I'm really interested to see if anyone else would find this useful. I've mostly used it for fun visualizations, but I did find it fairly helpful for quickly exploring a dataset from the Two Sigma Kaggle competition.

If you're interested in trying it out, please feel free to shoot me an email. The app only supports the Rift at the moment, and you will need Oculus Home to download the application.
