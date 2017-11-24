---
layout: post
title:  "DataSpace"
date:   2099-11-23 20:00:00 -0500
categories: vr data-visualization
comments: true
---
![scatter_overview]({{site.url}}/assets/2017-12-30-dataspace/scatter-overview.jpg){:class="img-responsive"}

When I first heard about the Oculus Rift, I was very excited about the new doors this technology would open up. We wouldn't be bound to the keyboard and mouse, or to the 2D plane of a monitor. We would be able to truly interact with anything we could imagine. This could be a huge step up for entertainment, but it also allows for new professional tools.

One thing that I was really looking forward to was a true 3-dimensional data visualization tool. With all of the VR resources, I figured that it would be relatively simple to implement. I waited patiently for the first app to appear, but that app never came. Most data visualizations were no more than a 2-D chart floating in a virtual room. Or a line-chart turned into a roller-coaster. Fun to look at, sure, but far from useful. The one applciation that claimed to be able to plot data in 3-D as I imagined was very buggy, and difficult to use.

That’s why I built DataSpace. This, from what I have found so far, is the first hard-core data visualization app for modern virtual reality HMDs. Bear in mind that this was my first real foray into Unity/C# (and game development in general), so there are a lot of rough edges.

<!--more-->

First, I began with a simple 3-D scatter plot. A simple three axis plot is simple, and i was able to add color and time dimensions as well. I can load up a CSV, choose variables to map to the grid, and then litterally walk around your data.

In this example, I use an n-body simulation as an example. Each data point represents a body. The CSV file has columns for the position, mass, time, and body id. The file I load here is about 12.5 MB (about 190,000 rows). I initially ran into issues drawing this much data, but was able to use some common video game optimization concepts that I learned from some great tutorials that I found.

Here's the loader menu: 

![file_select]({{site.url}}/assets/2017-12-30-dataspace/file-select.jpg){:class="img-responsive"}

![csv_loader]({{site.url}}/assets/2017-12-30-dataspace/csv-loader.jpg){:class="img-responsive"}

It gives you a default mapping, but you can specify the inital mapping however you want. You can also change this on-the-fly once the data is plotted.

![axis_select]({{site.url}}/assets/2017-12-30-dataspace/axis-select.jpg){:class="img-responsive"}

![sphere_loaded]({{site.url}}/assets/2017-12-30-dataspace/sphere-loaded.jpg){:class="img-responsive"}

Once you're in the scatter plot room, you can access an in-game menu:

![left_hand_menu]({{site.url}}/assets/2017-12-30-dataspace/left-hand-menu.jpg){:class="img-responsive"}

Move and resize the plot by grabbing it:


And even scroll through time:


The current time variable is displayed above the right hand:

![right_hand_time_index]({{site.url}}/assets/2017-12-30-dataspace/right-hand-time-index.jpg){:class="img-responsive"}


Do you have a use case for something like this? Let me know, I'm really interested to see if anyone else would find something like this useful.

Also, if you're interested in trying it out, please feel free to reach out. The app only supports the Rift at the moment, and you will need Oculus Home to download the application.