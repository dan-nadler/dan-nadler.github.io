---
layout: post
title:  "Setting up the Pi"
date:   2017-01-21 18:00:00 -0500
categories: thermopi setup hardware
comments: true
---
<!--more-->


# Parts

I ordered my first set of parts from Amazon, here's what I got:

1. [Raspberry Pi 3 Model B Motherboard](https://www.amazon.com/gp/product/B01CD5VC92) ($40)
1. [5pcs DS18B20 Temperature Sensors](https://www.amazon.com/gp/product/B01JKVRVNI) ($12)
1. [5V 2.5A Power Supply](https://www.amazon.com/gp/product/B00MARDJZ4) ($10)
1. [Electronics Starter Kit with Breadboard](https://www.amazon.com/gp/product/B01ERP6WL4) ($13)
1. [GPIO T-Cobbler with Ribbon Cable](https://www.amazon.com/gp/product/B01CNKXM54) ($10)
1. A 16GB MicroSD card that I had lying around

To be fair, I didn't do a whole lot of research before buying these parts, and probably could have found some of them cheaper elsewhere. The parts that came with the Elegoo kit were okay, but I am mostly just using the cables and breadboard. The breadboard is fairly small, which made working with the cobbler a little difficult. I ended up connecting the Pi directly to the breadboard without the cobbler, and have ordered a large breadboard as well.

# Getting the Pi up and running

1. [I downloaded Raspian Jesse Lite](https://www.raspberrypi.org/downloads/raspbian/)
1. I followed [these instructions](https://www.raspberrypi.org/documentation/installation/installing-images/windows.md) to write the image to my SD card
1. I plugged the Pi into my TV via HDMI, and my keyboard and mouse into the Pi's USB ports (my computer monitors don't have HDMI ports)
1. I ran [raspi-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) to setup the keyboard layout and timezone. I also was able to enable SSH from this menu. (Go to that link and Ctrl-F for more detailed instructions.)
1. I followed [these instructions](https://thepihut.com/blogs/raspberry-pi-tutorials/83502916-how-to-setup-wifi-on-raspbian-jessie-lite) to set up the WiFi connection.
1. Finally, I moved my Pi and all the electronics stuff I bought over to my desk.

# Setting up for development

I used PuTTY to connect to the Pi from my PC (The default username/password is pi/raspberry).

![putty]({{site.url}}/assets/2017-01-21-setting-up-the-pi/putty.png){:class="img-responsive"}

And, voila!

![connected]({{site.url}}/assets/2017-01-21-setting-up-the-pi/connected.png){:class="img-responsive"}

# Schematics

Now that the Pi was configured, I was ready to set up the temperature sensors.

I found [this site](http://www.cl.cam.ac.uk/projects/raspberrypi/tutorials/temperature/#step-two) very useful for getting started with the sensor, and learning how to read it from the Pi. The schematic they provided was simple, and I followed the instructions from step 2 and 3 to get it working at first.

![schematic]({{site.url}}/assets/2017-01-21-setting-up-the-pi/schematic.png){:class="img-responsive"}

I was able to plug the sensor into the breadboard without any modification to the sensor's wires, but I found that they fell out of the breadboard pretty easily. I figured that I was going to go through a lot of trial and error (I was right), so I decided to splice the ends of the sensor with the cables from the electronics kit to make the connections a little stronger.

The schematics call for a 4.7k Ohm resistor. The closest resistor in the kit I bought was a 5k Ohm resistor. I did a little googling, and it seemed like it should still work. So, I plugged everything in, according to the schematic above. This is the configuration that I got working, after a little trial and error:

![Pi with sensor]({{site.url}}/assets/2017-01-21-setting-up-the-pi/pi-with-sensor.jpg){:class="img-responsive"}

![Breadboard]({{site.url}}/assets/2017-01-21-setting-up-the-pi/breadboard.jpg){:class="img-responsive"}

# Reading the Temperature

Following the instructions from the schematics site above, I typed these commands into the terminal:

{% highlight bash %}
sudo modprobe w1-gpio
sudo modprobe w1-therm
cd /sys/bus/w1/devices/
{% endhighlight %}

At first, I saw a folder that looked like '00-0000...'. This happens when the sensor is not wired correctly. I went back to the breadboard (after turning off the Pi) and was a bit more careful when following the schematics. I think that I misplaced the resistor initially.

In any case, once I turned it back on, I reran the above commands, and saw that my sensor's serial number appears in the .../devices/ folder!

I was then able to read the sensor like so:

![reading data]({{site.url}}/assets/2017-01-21-setting-up-the-pi/reading_sensor_data.png){:class="img-responsive"}

The temperature "t=20187" means that it is 20.187 degrees Celsius in my living room.

Next time I'll figure out a way to log the data.

-Dan
