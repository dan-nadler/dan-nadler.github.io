---
layout: post
title:  "Controlling the Thermostat, and Better Reliability"
date:   2017-02-16 20:00:00 -0500
categories: thermopi software 
comments: true
---

This past week I spent some time improving the reliability of the thermostat, and also working on a way to set the temperature
without having to SSH into the Pi.

## An Interface for the Thermostat
I decided to embed a small Flask application inside the thermo package in order to create a simple web-based UI. This would
allow me to control the thermostat from a screen attached to the Pi itself, and also through any other device on my local network.
I could theoretically port-forward the application so that I could access it from outside my local network, but I will need to 
add some security before doing something like that.

The first step was to sketch out a plan for the UI. I came up with this simple interface after a few iterations. This is just the 'main menu,' I haven't 
designed the other pages yet.

![sketch]({{site.url}}/assets/2017-02-16-thermostat-control/sketch.png){:class="img-responsive"}

My Flask application is laid out so that it generally calls a function from an API file. The functions in the API file reach out to other 
modules within `thermo` or go straight to the database to insert or update a record.<!--more-->

Here is an example of a function that sets a constant temperature target for the thermostat by inserting a row in the `message` table.

{% highlight python %}
@duplicate_locally
def set_constant_temperature(user, zone, temperature, expiration, local=False):
    """
    Send message to override temperature schedule for all sensors attached to the specified zone
    :param user: user id
    :param zone: zone affected
    :param temperature: float: temperature target
    :param expiration: datetime for expiration of temperature target
    :param local: Use the local SQLite database if true
    :return:
    """
    session = get_session(local=local)
    results = session.query(Sensor).filter(Sensor.user==user).filter(Sensor.zone==zone)
    session.close()

    target = {}
    for sensor in results:
        target[sensor.location] = temperature

    message = {
        'target': target,
        'expiration': expiration.isoformat(),
        'zone': zone,
    }

    j = json.dumps(message)

    session = get_session(local=local)
    new_message = Message(record_time=datetime.now(), user=user, json=j, type='temperature override')
    session.add(new_message)
    session.commit()
    session.close()
    return
{% endhighlight %}

### An Aside on Reliability
`@duplicate_locally` is a new decorator in `thermo.common.models` that duplicates the call to a local SQLite database. This is part of my push to improve
reliability. I also created a decorator for read operations. The distinction is that I always *write* to the local database, but only *read* from the local database 
If `thermo` cannot reach the normal server. Additionally, I now specify a 'fallback' sensor in the `local_settings.py` file. This is a sensor 
that is connected directly to this Raspberry Pi, and can be used to run the thermostat if it can't connect to the database.

{% highlight python %}
def duplicate_locally(function):
    """
    Call the function with the local SQLite database in addition to the remote database
    :param function:
    :return:
    """
    def wrapper(*args, **kwargs):
        try :
            function(*args, local=False, **kwargs)
        except Exception as e:
            print('Failed using remote database.')

        try:
            function(*args, local=True, **kwargs)
        except Exception as e:
            print('Failed using local database.')
            raise(e)

        return

    return wrapper
{% endhighlight %}


### Back to the Interface

Once I had a handful of API functions to call, I was able to create a simple Flask app to get me started:

{% highlight python %}
from flask import Flask, render_template, request, redirect, url_for
from thermo.control.UI.api import *
from thermo import local_settings
import numpy as np
from datetime import datetime, timedelta


def aggregate_temperatures(temperatures, minutes=1, method='Median'):

    temp_list = [value for key, value in temperatures.iteritems()]

    if method == 'Median':
        current_temp = np.median(temp_list)
    else:
        current_temp = np.median(temp_list)

    return current_temp


app = Flask(__name__)
zone = 1

@app.route('/', methods=['POST', 'GET'])
@app.route('/index', methods=['POST', 'GET'])
def index():
    minutes = 1

    room_temps = get_current_room_temperatures(local_settings.USER_NUMBER, zone, minutes)
    room_targets = get_current_target_temperatures(zone)

    context = {
        'current_temp': aggregate_temperatures(room_temps),
        'current_target': aggregate_temperatures(room_targets)
    }

    return render_template('index.html', **context)


@app.route('/override.html', methods=['POST'])
def override():
    temperature = request.form.get('target')
    expiration = datetime.now() + timedelta(hours=int(request.form.get('hours')))
    set_constant_temperature(local_settings.USER_NUMBER, zone, temperature, expiration)
    return redirect(url_for('index'))

if __name__ == '__main__':

    app.run(debug=True, port=8080, host='0.0.0.0')

{% endhighlight %}

This app allows me to see the current temperature and target, and also allows me to override the default schedule for a specified number of hours!

![webpage_beginnings]({{site.url}}/assets/2017-02-16-thermostat-control/webpage_beginnings.png){:class="img-responsive"}

-Dan
