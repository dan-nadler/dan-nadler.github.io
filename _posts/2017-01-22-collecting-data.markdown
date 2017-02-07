---
layout: post
title:  "Collecting Data"
date:   2017-01-22 18:00:00 -0500
categories: thermopi setup software
---

# Reading the Sensor #

As a reminder, the sensors write to files located here: `/sys/bus/w1/devices/<DEVICE-ID>/w1_slave`, and they look something like this:

{% highlight text %}
24 01 4b 46 7f ff 0c 10 48 : crc=48 YES
24 01 4b 46 7f ff 0c 10 48 t=18250
{% endhighlight %}

The `t=18250` is the temperature (this would be 18.25 degrees celsius, and `crc=48 YES` shows that the sensor is functioning properly. If it was not, it would say `NO`.

It’s pretty simple to read this file… here’s the function I wrote to do it:

__IO.py__

{% highlight python %}
def read_temp_sensor(device_id, units='F'):
    """
    Read temperature from the sensor and convert to <units>
    :param device_id:
    :param units:
    :return:
    """

    path = '/sys/bus/w1/devices/{0}/w1_slave'.format(device_id)
    with open(path, 'r') as f:
        line1 = f.readline().strip()
        line2 = f.readline().strip()

    is_on = re.search('[A-Z]+', line1).group()
    if is_on == 'NO':
        raise Exception('Sensor {} is offline.'.format(device_id))

    temp_raw_string = re.search('(t\=)(\-|)[0-9]+', line2).group()
    temp_raw = re.search('(\-|)[0-9]+', temp_raw_string).group()

    temp_celsius = float(temp_raw) / 1000.
    temp_fahrenheit = (temp_celsius * 9./5.) + 32.

    if units == 'F':
        out = temp_fahrenheit
    elif units == 'C':
        out = temp_celsius
    else:
        raise Exception('Units not recognized.')

    return is_on, out
{% endhighlight %}

I’m converting the temperature into Fahrenheit with this function as well.

To store the data, I’ll start with a simple CSV file, and append the observations.

{% highlight python %}
def record_to_csv(record_time, temp, location, file):
    with open(file, 'a+') as f:
        line = "{0},{1},{2}\n".format(
            str(temp),
            record_time.strftime('%Y-%m-%d %H:%M:%S'),
            location
        )
        f.write(line)
{% endhighlight %}

Finally, I’ll add a loop to this file to record the sensor every 10 seconds:

{% highlight python %}
if __name__ == '__main__':
    device_ids = {
            'Living Room (North Wall)': '28-04165425e4ff',
        }

    file_path = '/home/pi/temps.csv'

    while True:

        for location, device_id in device_ids.iteritems():

            try:
                _, temperature = read_temp_sensor(device_id)
                record_to_csv(datetime.now(), temperature, location, file_path)

                print(location, temperature)

            except Exception as e:
                print(e)

        time.sleep(10)
{% endhighlight %}

Here’s a screenshot of the data streaming from the PuTTY console. I added a second sensor before taking this screenshot. I’ll cover that in a future blog post.

![data]({{site.url}}/assets/2017-01-22-collecting-data/data.png){:class="img-responsive"}

Now, I just have to sit back and wait to collect enough data… the suspense is killing me.

-Dan
