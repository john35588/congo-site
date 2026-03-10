---
title: "Building My First FPV Drone"
date: 2026-02-22
draft: false
description: "My experience building my first FPV drone."
summary: "My experience building my first FPV drone"
slug: "first-fpv-drone"
tags: ["FPV", "drone", "DIY", "hobby", "betaflight", "analog", "TBS source one v6", "3D printing", "3D design"]
---

{{< figure
    src="complete-drone.webp"
    alt="My 5 inch FPV drone"
    caption="My completed 5 inch FPV drone"
>}}

Several years ago, I saw a video online of someone flying a drone in a style I hadn't seen before. They were flying through an old building, in and out of windows, dodging through tight spaces at breakneck speeds. I was immediately intrigued and wanted to try it. I began to research and found just the beginning of how massive the "FPV rabbit hole" was. 

I was excited and ready to buy all the parts to build my own drone right then, but life had different plans, and my FPV dreams were put on hold for the next couple of years.

Fast forward to about six months ago, I was ready at last to start again! I bought a controller (the Radiomaster Pocket ELRS version) and began practicing on Velocidrone. Meanwhile, I spent hours on [Oscar Liang's website](https://oscarliang.com/) and YouTube learning all I could about the hobby. Eventually, I bought a Betaflight Air65 Tiny Whoop to practice around the house with. After a few months of periodic practice and around the start of 2026, I was finally ready to begin searching for parts. 

After my research, I had a pretty good idea on what I wanted my drone to be capable of: speed, agility, durability, etc. So, I tried to find parts that were centered around what I knew I wanted. Below is a list of the parts I ended up with, and why I chose each one.

## Parts List
|Part|Model|Why I chose this|
|---|---|---|
|Frame|TBS Source One v6|I knew I wanted a 5 inch drone, and I wanted something well documented and durable. The Source One checks all these boxes, and provides a large area to build in. It has handled exceptionally well, and shows no signs of damage from my several crashes.|
|Motors|Xing 2207 1855KV|Excellent reviews and great price. I wanted to use 5S and 6S batteries, so I went with the lower 1855KV option.|
|ESC|GOKU G55M AM32|This ESC came with my flight controller. It runs AM32, and can manage 55 amps per motor.|
|Flight Controller|GOKU H743 PRO|This flight controller has many of the modern amenities built in! A powerful processor, dual gyroscopes, several UARTs, and two camera ports!|
|Camera|Caddx Ratel 2|A very well reviewed camera with decent quality for a decent price.|
|VTX|TBS Unify Pro32 HV|An amazing video transmitter. It can output at over 1W, and supports TBS Smart Audio.|
|VTX Antenna|XILO AXII MMCX RHCP|For this part, I didn't know for sure what to get. There weren't many parts available online for the Source One v6, so I figured I'd be designing my own mount for the antennas. This one had decent reviews and looked to be pretty easy to design a mount for.|
|Receiver|HGLRC ELRS 2.4 RX-T|A small, simple receiver, nothing too fancy, but gets the job done.|

Something experienced builders may have noticed is the inclusion of an analog FPV system. Analog has been the standard for years, but has recently been becoming obsolete as it's being replaced by digital systems like HDZero, Walksnail, and DJI's digital system. I knew I would be getting lower quality video, but I wasn't ready to invest $300+ in just a video system quite yet. Besides, if I started with Digital, I knew I would never be able to go back to any analog system again.

I ended up with a happy medium, the TBS Unify Pro32 HV provides a strong, stable link with decent quality (for an analog system), and didn't cost more than the rest of the drone did.

## Assembly

{{< alert "thingiverse" >}}
All my 3D models used for this build are available on Thingiverse [here](https://www.thingiverse.com/thing:7280553).
{{< /alert >}}

Finally, the parts all arrived, and I was ready to assemble everything. The frame was first, and it went together well, though I discovered I would be needing longer stack screws than the ones it came with. I printed some feet I designed, installed the motors, then soldered them to the ESC. I had read about the difficulty others have while soldering motors and battery leads to the ESC, and had similar difficulty. It's a rite of passage I guess, but in the end I had everything attached well and not looking too bad. 

I mounted the camera, then the VTX and receiver in the back. I used thermal tape to attach the VTX to the frame, as I'd heard it could get hot, then I opened Fusion 360 to figure out how I'd mount the antennas. Here is what I came up with:

{{< figure
    src="antenna-mounts.webp"
    alt="Mounts for RX and VTX antennas installed on drone"
    caption=" My custom mounts for RX and VTX antennas installed on drone"
>}}

The RX antenna mount is held in place by the screws on the bottom of the frame, the RX antenna is zip-tied on with some small zip-ties I had laying around.

The VTX antenna mount "hugs" the top plate of the frame, and is held in place by the top screws. The VTX antenna is slid into a curved slot, then held in place by a small section of a zip-tie. The zip-tie fits snugly inside it's channel and keeps the antenna at a gentle bend.

I saw some concerns online about the exposed carbon fiber on either side of the camera, some said it would start failing after a few head-on crashes, so I designed these guards to lessen that chance:

{{< figure
    src="camera-guard.webp"
    alt="Thin, printed guards on either side of the camera"
    caption="My custom camera guards installed"
>}}

## Programming

Like most other FPV drones, I opted to use Betaflight. I plugged in my drone for the first time, set each motor direction, configured the UART ports so my receiver and Smart Audio VTX control would work, and adjusted the OSD to my liking. There are so many options and settings within Betaflight, and I only scratched the surface of what's available. 

## First Flight

After I had completed my drone, I waited in agony for about two weeks before the weather (and my schedule) finally cooperated enough for me to fly. When it finally did, I found an open field and flew around! It was still cold enough that I only managed to use one battery before my hands were too cold to continue, but it was a blast! Since then, I've been able to fly a few more times. I'm still pretty timid, but I'm excited to continue with this new hobby!