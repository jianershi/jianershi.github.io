---
layout: post
title:  "Philips Hue Color Adaptive Sunlight"
date:   2013-12-05 19:07:00
categories: technical
---

[Philips Hue Color Adaptive Sunlight](https://github.com/paulshi/PhilipsHueColorAdaptiveSunlight) project was inspired by [f.lux](http://justgetflux.com/). It adjusts your screen temperature to match the color of the sun so your bright screen doesn't hurt your eyes. I really like f.lux, and since I was working on [Philips Hue Remote API](http://blog.paulshi.me/technical/2013/11/27/Philips-Hue-Remote-API-Explained.html), I thought I can create a Philips Hue version as well. I set some basic requirements for it:

1. It needs to automatically adjust the color temperature unmonitored
2. It needs to get sunrise / sunset info everyday
3. It should change color temperature without affecting on/off status

Since it needed to run unmonitored, it would need to go on a web server.  For getting the sunrise and sunset info, I chose [Weather Underground API](http://www.wunderground.com/weather/api/) because this was the easiest solution as well as the most clean solution without burdening codebase. I only needed to provide a zip code and the API would tell me the sunrise and sunset info for that post code area.

The sunrise and sunset time was updated once per day at midnight without exhausting my free API limit with Weather Underground.

It turned out I cannot really control the color temperature of a lightbulb when it was turned off. It also turned out that in terms of calling API, the response was the same with light on or off. So without a callback API offered by Philips Hue, I basically just sent them control command once per second.

That was pretty much it. Originally I let the transition happen immediately at sunrise and sunset, this turned out to cause eye strain. I have since added support for transition steps, so you won't really notice the transition. It happens naturally in a span of around 40 seconds by default.