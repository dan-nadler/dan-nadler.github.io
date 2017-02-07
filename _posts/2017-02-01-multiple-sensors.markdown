---
layout: post
title:  "Multiple Sensors"
date:   2017-02-01 18:00:00 -0500
categories: thermopi hardware
comments: true
---
<!--more-->

# Multiple Sensors - Hardware

I wanted to go back and write a little about how I am using multiple sensors for this project. I learned early on that the DS18B20 thermal sensors I am using can be chained together very easily, as illustrated here:

![schematic]({{site.url}}/assets/2017-02-01-multiple-sensors/schematic.png){:class="img-responsive"}

The result is that one GPIO pin can receive data from a number of sensors (not sure of any limit). The sensors each appear in `/sys/bus/w1/devices/` as folders named by their serial numbers.

I decided to solder the resistor to the sensor, rather than hook up everything through a breadboard. This probably isn’t a great long-term solution, but it allows me to easily move sensors around.

The sensors aren’t labeled, as far as I could tell, so I had to figure out the serial numbers and keep track of everything myself. 

My process to organize the sensors was:

1. Set up a single sensor
1. Solder a resistor between the power and data lines
1. Plug it into the breadboard
1. Check the `/sys/bus/w1/devices` folder 
1. Record the serial number of the sensor, and attach a label to the sensor
1. Update my ‘sensor’ table in the database I set up with this information
1. Repeat for each sensor

Once I had everything organized, I ran some wire from my desk (where my Pi is) to various parts of my house that were easily accessible. I attached the sensors to the ends of the wires, and recorded their locations in the ‘sensor’ table.

![sensor_table]({{site.url}}/assets/2017-02-01-multiple-sensors/sensor_table.png){:class="img-responsive"}

At this point, I was collecting data from 4 sensors distributed between two rooms, and a 5th sensor outside my house. I wanted to collect data from other rooms, but they were too far away to discretely run wire. 

To get around this, I ended up buying another Raspberry Pi (I looked into cheaper boards, but decided to stick with what I know for now). I updated my sensor table to include a ‘unit’ column that identifies the Raspberry Pi that the sensor is attached to. Then, I cloned my thermoPi repo to the new Pi, added a sensor, and put it in my bedroom.

![pi2]({{site.url}}/assets/2017-02-01-multiple-sensors/pi2.png){:class="img-responsive"}

I realize now that rather than have the unit column in the database, I could have the Pi check for any attached sensors, and look them up in the database by serial number. While this might be a little less efficient as far as the query goes, this check really only needs to be done once every 5 minutes or so. At the moment, I only check once when the script is started.

I'll go back to my code soon and make these changes:

1. Check for sensors attached to the Pi at a set interval, and query database if a new one is detected
1. Remove the 'unit' column

-Dan
