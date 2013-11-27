---
layout: post
title:  "Philips Hue Remote API Explained"
date:   2013-11-27 03:24:00
categories: technical
---

This post is a response to Stackoverflow question [How to connect backend service with philips hue bridge remotely?](http://stackoverflow.com/posts/20233705/) I realized very late the I don't have enough reputation to post so many links. So it is explained here.

The question actually comes as two part:

* Authentication
* Remote Control

##Authentication

I haven't figure out how to do that automatically. Right now I have to do this:

1. Find your Bridge ID from [https://www.meethue.com/api/nupnp](https://www.meethue.com/api/nupnp)

2. Get access token

		www.meethue.com/en-US/api/gettoken?devicename=iPhone+5&appid=hueapp&deviceid=**BRIDGEID**

3. Right Click on `BACK TO THE APP` and write down `phhueapp://sdk/login/**ACCESSTOKEN**`

Basically it is a hack to get your access token. You fake your app as the official iOS Hue App, and ask for access token that way. I am not sure how to do this automatically. I tried with iFrame but this result in cross-domain iFrame in which I cannot really monitor the ACCESSTOKEN because of security block by browser vendors.

##Remote Control

Once authentication is done, this part can be done automatically. There are several known private endpoints for sending control command and getting all the status of the philips hue bridge. 

* Sending Command Endpoint:

		GET https://www.meethue.com/en-US/user/sendMessageToBridge

* Getting Status Endpoint:

		GET https://www.meethue.com/api/getbridge

`ACCESSTOKEN` you obtained earlier is needed for the URL Parameter: 
	
	?token=ACCESSTOKEN

with request header

	content-type=application/x-www-form-urlencoded

with the body

	clipmessage={ bridgeId: "**BRIDGEID**", clipCommand: { url: "/api/0/**APIENDPOINT**", method: "**METHOD**", body: **JSONCOMMAND** } }


* `BRIDGEID` is the same one you obtained earlier
* `APIENDPOINT` the same as official API `/api/<username>/***` by removing `/api/<usename>/` part
* `METHOD` PUT/GET/POST/DELETE the same 4 method as official API. Despite GET really doesn't work since all response from the Sending Command Endpoint is 200 explained in the following part, while delete is not tested
* `JSONCOMMAND` The actual command body for example `{"on":true}`

###Limitations

Current limitation is you cannot immediately know from the response whether your control command succeeded like the official API. All the response you get from calling the Sending Command Endpoint is pretty much always `<200>` if you are doing it correctly. But you can always pull all the status from the Getting Status Endpoint


###Remote Control API

I wrote [Philips HUE Remote API](https://github.com/jarvisinc/PhilipsHueRemoteAPI) to specifically solve the remote control problem. So peace~~ :)


##Paper

For full documentation please refer to this excellent paper:

[Hacking Lightbulbs: Security Evaluation of the Philips Hue Personal Wireless Lighting System](http://www.dhanjani.com/docs/Hacking%20Lighbulbs%20Hue%20Dhanjani%202013.pdf) by [Nitesh Dhanjani](http://www.dhanjani.com/about.html)
