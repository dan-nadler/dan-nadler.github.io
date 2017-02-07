---
layout: post
title:  "thermoDashboard"
date:   2017-02-01 18:30:00 -0500
categories: thermopi dashboard analysis
comments: true
---
<!--more-->
# thermoDashboard

I got tired of constantly querying my database to see the current status of my house, so I decided to build a simple dashboard.

I chose to use Zappa and Flask to display some simple Highcharts graphs on a webpage. The database is still hosted at Google, which isn’t ideal for this setup. I do like the Google Cloud interface, and the SQL service they have is easy to use with auto-scaling. I’ll have to weigh my options and decide if it’s worth the move to AWS.

It was quite simple to set up, and I put the dashboard on GitHub, here: https://github.com/dan-nadler/thermoDashboard

I plan on slowly transforming this into a home-automation control app as I tie in the HVAC system and other home automation ideas.

![dashboard]({{site.url}}/assets/2017-02-01-dashboard/dashboard.png){:class="img-responsive"}

My deployment of this app is running [here](https://l7a4zdmpzg.execute-api.us-east-1.amazonaws.com/dev/), if you’d like to take a look. Just be aware that it might be unreliable, because I am actively developing it:

[https://l7a4zdmpzg.execute-api.us-east-1.amazonaws.com/dev/](https://l7a4zdmpzg.execute-api.us-east-1.amazonaws.com/dev/)

It's a bit slow to load at the moment. Probably because I'm pulling way more data than I actually need, but it gets the job done for now.

-Dan
