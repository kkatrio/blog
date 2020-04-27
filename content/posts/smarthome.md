---
title: "Smart home"
date: 2020-03-22T12:33:31+02:00
---


This is how I try to make my home a smart and safe place. 

## Motivation
I live in the country, near some sea, not close to a city. Had you spent a dark windy winter night alone here, you wouldn't find it easy to feel completely safe. I have had the idea of installing cameras for years but never had I found the urgency and the courage to do the appropriate research and risk buying expensive hardware. Until I did.

It is difficult at first because you realize that you must research many components and combine various different parts: network equipment, computers, cameras, electronics, software. Now I see the camera feed as the equivalent of logging in software. Good logging is essential.

## Zoneminder
Free and open source,  Zoneminder is an alternative to a ready -out of the box- NVR solution. Knowing Zoneminder exists has been very important to encourage me getting started. The community is alive and helpful, offers lots of useful information and guidance.

As a complete solution at first it may seem a bit intimidating, because you need dedicated hardware to install it, and hardware resources is an issue since video feed and its analysis is no negligible task. A budget and good enough solution, according to the zoneminder community, is to buy a used server. An alternative is to build your own server or use some old-ish pc. I like building things so I built my own.

## Home server
Information about gaming oriented desktop hardware online is abundant. Finally, I bought a completely overkill 8-core 16-thread 3rd gen AMD cpu, 16 gb of memory, a 300 euro motherboard and a good power supply. For less important parts I used old materials that I had laying around: drives, cables, graphics cards. Oh, and I bought a new cool case with built-in rgb leds. That was just for fun.

Building was interesting. I built it little by little during a week. First I put together all the basic parts outside the case, and when I realized that it was working I got fiiled with satisfaction. 

![leak](/img/serverpost.jpg)

Then I put everything together in the case, I installed ubuntu server, zoneminder, the first camera and I left it on during the night. Next day, I come back from work and the computer was shut off. Was it a incompatible combination of parts or some sudden power surge burning something? I eliminated failure on one part after the other. Turns out, after 10 hours the motherboard has enough and does not boot any more - not without the latest bios version. Thankfully my motherboard (Aorus x570 pro) has bios flash back functionality, that is you can update it with a usb stick without booting into an operating system. Taking out the cpu, cleaning the paste, cleaning the paste that had accidentally fell onto the 330 euro cpu pins, was something I did not enjoy. Lesson learned: If you buy a new motherboard, just update the bios before you do anything else.

Since, I managed to setup a quite stable network. I still use the ISP provided equipment as a router but it works for now and I have plans to replace it. I keep expanding with switches and cables, access points and of course cameras. Also I bought a good UPS which powers all the network: If the power goes down, the cameras will keep on recording for about 90 minutes. 

![leak](/img/cables.jpg)

Cable management probably is something I can improve. Also I use iperf3 for measuring network performance. Mine's bottleneck is the cat5e cable at 100Mbps, but I chose that consciously. It should be enough for a 4MP feed, and I think I don't need more. Unless I do.

## Cameras
The problem with zoneminder at first is weather your camera will be compatible with it. After you take the risk and buy your first camera, you realise it's not a big problem. Most cameras work, I think. My first camera was a hikvision 2045 4MP. Now I bought another, less expensive but good enough. The thing is, most of the features that you pay for in an expensive camera are provided by the awesome zoneminder. So you don't gain much if you record in 4MP instead of 2MP, or if you take 30 fps instead of 10. Or if your camera can save in a sd card. I am planning on buying two 4TB HDD and upload on a server of mine, I don't care about saving on sd cards.

![leak](/img/cam1.jpg)

Drilling a hole in the wall was necessary too. Can you drill a hole in your home if you need to?

![leak](/img/wallhole.jpg)

## Scale
Right now I have two cameras deployed but my plans invole at least 5 more. I will be adding and expanding gradually. That's one benefit of doing it on your own I guess.

Now, using your cameras outdoors is completely different from using them indoors. So so many variables and conditions difficult to predict. There are no correct sensitivity values if you do motion detection. There are flies and insects attracted by the infrared light of the cameras during the night. There is rain, there are dogs, cats, wind, leaves, sun, all creating creating false alarms and parameters to take into account. But I don't mind. 

## Motion detection
Apart from recording everything I do motion detection with zoneminder. When motion is detected, I have setup ssmtp and I get an email with an image attached. On top of zoneminder I use a software called zmeventnotification, an event server built to work with zoneminder by someone in the community. It offers a lot, including machine learning plugins, instant notifications on your mobile, and running scripts at the start and at the end of an alarm. And more, that I am not aware of yet. I use that to execute python scripts to send signals through a usb port. Which brings us to:

## Electronics
An arduino nano receives a byte indicating an alarm and another indicating the end of an alarm. It is just plugged in a usb port of my server and listens through its serial port. 

![leak](/img/arduino.jpg)

I use various small electronics to make an alarm sound when there is motion detected. The piezo element produces sound by vibrating due to small electrical current running through it. I have connected two piezo elements in parallel. Once you get into electronics you come up with endless ideas. I have done just some very basic stuff and it's been fun and amazing. Whenever a car passes on the road, the alarm sets off. When the car is out of the specified zone, the sound stops. It works, at least as a proof of concept, but it has been very robust so far. In the night, if I hear the sound I can immediately look at the video and see what's happening. The potential with electronics is truly huge. Right now I am setting up a circuit to use a relay that will turn on a 220V light. Lights, sounds, lasers, so many things you can do if you just understand Ohm's law and have a soft spot for mosfets.

## What's next
I have only two cameras right now, but I think my server hardware can support 20. Maybe ever more, probably more, depending on the resolution and the fps. There are so many details and things I have been meaning to write that I forget. So here are my plans for the next:

### Offline backups
Do I need to explain how much important is this? What is "offline" though? A raspberry pi on the basement with a couple of hard drives attached to it is a good solution? Upload on a VPS, where i must setup sftp, firewall, and pay about 5 euros per month? Is sftping it to another machine through the internet in my parents house in the city an option? My upload speed here is not great, if I use the internet I must be very clever.

### Less false positives
Flies, spiders and all kinds of insects are flying in front of the infrared leds of the cameras. An idea is to install infrared bulbs on the ground so that the infrared light on the camera does not need to be on -- can I really dig everywhere really and install power cables underground? I probably can, and will do. Outdoor smart things need so much research on everything - wires, bulbs, voltage drops - so much time.

### Access through internet safely
OpenVPN? Just a good firewall in the router with an open port? Maybe just uploading to a server elsewhere? I must be very very careful here.

### Smart lights in the garden
A particular set of lights may be turned on when something is moving on a particular part of the garden. That is easy actually. Apart from the lights installation.

### More solid home router
pfsense on a old machine? Does it worth it? Probably. Also I need more switches and network cables. And more access points.
