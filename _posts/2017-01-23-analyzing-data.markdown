---
layout: post
title:  "Analyzing the Data"
date:   2017-01-24 19:30:00 -0500
categories: thermopi analysis
---

Finally, we're on to the good stuff!

I left the Pi to collect data for a little while, and this is what I 
had when I came back the next day.

{% highlight python %}
df['Living Room (North Wall)'].head(10)
{% endhighlight %}

{% highlight text %}
record_time
2017-01-21 18:16:22    65.8616
2017-01-21 18:16:33    65.8616
2017-01-21 18:16:45    65.8616
2017-01-21 18:59:37    66.2000
2017-01-21 18:59:49    66.2000
2017-01-21 19:00:01    66.2000
2017-01-21 19:00:13    66.2000
2017-01-21 19:00:24    66.2000
2017-01-21 19:00:36    66.2000
2017-01-21 19:00:48    66.2000
Name: Living Room (North Wall), dtype: float64
{% endhighlight %}

__WOOHOO!__

Plotting the data from the CSV file gives me some promising preliminary data.

![temp_over_time]({{site.url}}/assets/2017-01-23-analyzing-data/temp_over_time.png){:class="img-responsive"}

You might have noticed a break in the pattern at the beginning of the chart, here:

![interpolated_data]({{site.url}}/assets/2017-01-23-analyzing-data/interpolated_data.png){:class="img-responsive"}

During that period of time, I had turned off the Pi to either debug something, move the unit, or rewire the sensors. Given 
that it would be a good idea to be able to handle missing data, I'm using a linear interpolation to fill in the gap. This
is obviously not an optimal solution, so I'll look into a better way to handle this in the future.

# Subjective analysis

It's worth spending a little time to really understand what we're seeing in this series, and to that end it's important to know 
a bit about my condo's heating system. 
* I live on the bottom floor of an old, three story building. 
* My walls are horse-hair plaster
* The windows are original (and quite leaky)
* My condo is heated via force hot air, and the furnace was recently replaced

Here's a floor plan of my condo that I drew up a while back. The furnature is somewhat accurate, but the important thing to note is the
locations of the thermostat (red), vents (green), and intake (blue).

![floor_plan]({{site.url}}/assets/2017-01-23-analyzing-data/floor_plan.png){:class="img-responsive"}

The `Living Room (North Wall) sensor is located in the top right corner of the floor plan, next to the sofa.

With all of that out of the way, what are we looking at? Well, I set my thermostat on a schedule. It's generally 68 degrees when I'm home, 
and 62 at night or when I'm at work.

![temp_over_time]({{site.url}}/assets/2017-01-23-analyzing-data/temp_over_time.png){:class="img-responsive"}

What I found immediately interesting was that the condo can make it pretty much completely through the night without needing the heat once.

Additionally, the actual shape of the decline is interesting. The rate of loss of heat appears to decelerate in the period immediately following
a period of heating. The is especially apparent after the last heating cycle around 11pm - 11:30pm before I went to bed.

Then, at 6:30, my heating kicks back on to hit the morning target of, if I recall correctly, 65 degrees. It's interesting that during this time, 
the living room overshoots by a large margin, and takes some time (upwards of 15-30 minutes) to arrive at a stable mean. The same thing happens when the 
heat bumps up after that. I believe that this was taken over a Saturday, which explains the higher daytime temperature.


Next time, I'll get into some of the multi-sensor data, and see what kind of relationships exist between outdoor and indoor temperature.

-Dan
