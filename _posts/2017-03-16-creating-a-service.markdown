---
layout: post
title:  "A Quick Note on Services"
date:   2017-03-16 20:00:00 -0500
categories: thermopi software
comments: true
---

I recently ran into an issue where my master control process was crashing. I haven't yet figured out the root cause, but it runs fine for 12-24 hours
before crashing. After crashing, I have been able to restart the process without issue. As a stop-gap measure, I decided to set up the process
to automatically restart in the event of a crash...
<!--more-->

To do this, I created a systemd service by creating the following file:

`/lib/systemd/system/thermo.service`

```
[Unit]
Description=The thermoPi master control service
After=multi-user.target

[Service]
ExecStart=/usr/bin/python /home/pi/thermoPiDev/thermo/control/master.py
Restart=always
RestartSec=1

[Install]
WantedBy=multi-user.target
Alias=thermo.service
```

I then ran the following commands:

```bash
sudo systemctl enable thermo
sudo systemctl start thermo
```

using `ps aux | grep thermo` I could see that the service was running:

```bash
pi@pi1:~ $ ps aux | grep thermo
root       901  2.4  4.4  88804 42096 ?        Ssl  20:24   0:11 /usr/bin/python /home/pi/thermoPiDev/thermo/control/master.py
pi        1012  0.0  0.2   4276  2008 pts/0    S+   20:32   0:00 grep --color=auto thermo
```

Finally, to test that the automatic restart worked, I used `sudo pkill -g 901 -e` to kill the process, and then `ps aux | grep thermo` to see if it 
was restarted:

```bash
pi@pi1:~ $ ps aux | grep thermo
root      1057 53.6  4.2  87628 40576 ?        Rsl  20:35   0:02 /usr/bin/python /home/pi/thermoPiDev/thermo/control/master.py
pi        1069  0.0  0.2   4276  1908 pts/0    S+   20:35   0:00 grep --color=auto thermo
```

Note that the thermo process has a new PID, which I think means it was killed and successfully restarted.

Hopefully this will keep my pipes from freezing until I can *actually* fix whatever is causing the crash!
