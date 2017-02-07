---
layout: post
title:  "Of Relays and Thermostats"
date:   2017-02-05 13:50:00 -0500
categories: thermopi hardware software
comments: true
image:
    feature: ../../assets/2017-02-05-of-relays-and-thermostats/relay_breadboard_off.png
---

This weekend I decided to take the leap and add thermostat functionality to my setup. 

1. Connect a Pi to furnace
1. Write the software to control the furnace

## Connecting to the Furnace

This was actually relatively straightforward. My thermostat is connected to the furnace via two wires. When the thermostat completes the circuit, the furnace
turns on. When it breaks the circuit, the furnace turns off. There's a little nuance here, as I imagine the details can vary from furnace to furnace. For example,
my furnace has a variable blower, but I don't have enough wires connected to my thermostat to dynamically control it, I can only turn it on and off. I'll address 
this limitation in the future.<!--more-->

In order to break the circuit from my Pi, I connected a [relay ($5.80 on Amazon)](https://www.amazon.com/gp/product/B00VRUAHLE/ref=oh_aui_detailpage_o03_s01?ie=UTF8&psc=1)
to a GPIO pin.

Then, I set up a simple test on my breadboard with an LED:

![relay_breadboard_off]({{site.url}}/assets/2017-02-05-of-relays-and-thermostats/relay_breadboard_off.png){:class="img-responsive"}

When the relay is off, the circuit is broken, and the LED is off.

Then, I tested the relay by SSHing into the Pi, and running this code interactively:
{% highlight python %}
import RPi.GPIO as GPIO
GPIO.setmode(GPIO.BCM)

GPIO_pin = 25
GPIO.setup(GPIO_pin, GPIO.OUT)

GPIO.output(GPIO_pin, GPIO.HIGH) # LED turns on
GPIO.output(GPIO_pin, GPIO.LOW) # LED turns off
{% endhighlight %}

![relay_breadboard_on]({{site.url}}/assets/2017-02-05-of-relays-and-thermostats/relay_breadboard_on.png){:class="img-responsive"}

After I had it working, I had to write some code to flip the relay based on the temperature.

# Expanding the Database

I decided to expand my database a bit to keep track of the various actions that my system will eventually control. Now, it looks like this:

![database_layout]({{site.url}}/assets/2017-02-05-of-relays-and-thermostats/database_layout.png){:class="img-responsive"}

# Writing the Thermostat code

I started off fairly simple, querying the database for the temperature of my sensors, and then checking if a single sensor was above or below a certain target.

I defined a `Thermostat` class to check the temperature of all the indoor sensors. This could have just been a function, but at the time I was 
planning on expanding on this class.

{% highlight python %}
class Thermostat():

    def __init__(self, user_id):
        self.user_id = user_id

    def check_recent_temperature(self, window=5, verbose=False):
        """
        This function returns a dict of the average temperature 
        of each sensor over the past 5 minutes
        """
        session = get_session()
        indoor_temperatures = session.query(
            Temperature.location,
            func.sum(Temperature.value) / func.count(Temperature.value)
        ).filter(
            Temperature.record_time > datetime.now() - timedelta(minutes=window)
        ).join(Sensor).filter(
            Sensor.indoors == True and Sensor.user == self.user_id
        ).group_by(
            Temperature.location
        ).all()

        if verbose:
            for i in indoor_temperatures:
                print "%s, %.1f" % (i[0], i[1])

        return {i[0]: i[1] for i in indoor_temperatures}
{% endhighlight %}

I also defined an `HVAC` class that manages the GPIO communication:

{% highlight python %}
class HVAC():

    def __init__(self, heat_gpio_pin, gpio_mode=GPIO.BCM):
        self.heat_pin = heat_gpio_pin

        GPIO.setwarnings(False)
        GPIO.setmode(gpio_mode)
        GPIO.setup(heat_gpio_pin, GPIO.OUT)
        GPIO.setwarnings(True)

        self.check_relays()

    def __exit__(self):
        GPIO.cleanup()

    def turn_heat_on(self):
        GPIO.output(self.heat_pin, GPIO.HIGH)

    def turn_heat_off(self):
        GPIO.output(self.heat_pin, GPIO.LOW)

    def heat_relay_is_on(self):
        return GPIO.input(self.heat_pin) == 1

    def check_relays(self):
        try:
            self.turn_heat_on()
            assert (self.heat_relay_is_on() == True)

            self.turn_heat_off()
            assert (self.heat_relay_is_on() == False)

        except AssertionError:
            print("Heating relay check failed, did you assign the correct GPIO heat_pin?")
{% endhighlight %}

Finally, I wrote this loop to trigger the relay based on the temperature:

{% highlight python %}
if __name__ == '__main__':
    zone1 = HVAC(25)

    thermostat = Thermostat(1)

    while True:

        room_temps = thermostat.check_recent_temperature(window=5)

        target = 70
        room = 'Living Room (South Wall)'

        print("%s: %.2f" % (datetime.now().strftime('%m/%d/%Y %H:%M:%S'), room_temps[room]))

        if room_temps[room] < target and not zone1.heat_relay_is_on():
            print("Turning heat on.")
            zone1.turn_heat_on()

        elif room_temps[room] >= target and zone1.heat_relay_is_on():
            print("Turning heat off.")
            zone1.turn_heat_off()

        time.sleep(10)
{% endhighlight %}

Then, I ran the script via `python -m thermo.control.thermostat`:

![turn_on_relay]({{site.url}}/assets/2017-02-05-of-relays-and-thermostats/turn_on_relay.png){:class="img-responsive"}

Success!

I spent a good amount of time iterating on the code after this. You can see the current version on [Github](https://github.com/dan-nadler/thermopi)

# Pi on the Wall!

Once I felt that the code was good enough, I put the relay on my second Pi (which I had been using to collect temperature data from my bedroom), 
and connected it to the furnace circuit on my wall.

![pi_on_the_wall]({{site.url}}/assets/2017-02-05-of-relays-and-thermostats/pi_on_the_wall.png){:class="img-responsive"}

As you might expect, my girlfriend isn't too thrilled about this, so I'll be working on a more aesthetically pleasing version soon. For now, I'll just be putting this
up to test my control algorithm.

-Dan
