---
title: 'OpenWRT on a FortiGate 50E'
date: '2026-03-11'
#lastmod: '2026-02-28T19:43:11-07:00'
draft: false
ongoing: false
description: "My experience so far with OpenWRT"
summary: "Reflashing and configuring an old Fortinet firewall with OpenWRT"
slug: "openwrt"
tags: ["OpenWRT", "Fortinet", "Networking", "Unifi", "Shortcuts", "DIY"]
showDateUpdated: true
---

A few months ago, I brought home a used Fortinet FortiGate 50E firewall from work that I had replaced with a Unifi firewall. I had previously been using Google's Mesh WiFi system, and had wanted to replace it for quite some time. I had some experience with Fortinet's user interface, but was curious about what other open source options existed out there.

I looked at several options, and finally decided on OpenWRT, a simple, open source firewall firmware. It has support for my firewall, and was said to be easier than other firmware to load and configure.

Following the [installation instructions](https://openwrt.org/toh/fortinet/fortinet_fortigate_50e) for my model, I set up a TFTP server on my laptop, then booted the firewall, interrupted the boot process, and forced it to pull the OpenWRT files from my laptop. It booted successfully, and I got to work figuring out how to use it!

I followed a few other tutorials to figure out some of the more complicated setup procedures, and soon had it working perfectly! I also replaced my Google Mesh system with a few Unifi APs I had also gotten used from work (finally a network system I can take control of!).

One of my next challenges was to implement some kind of downtime for specific devices. I needed the TVs and computer to be blocked from accessing the internet after 9:00 PM each school night. This was a pretty simple set up with Google's mesh system, but took more time to get working properly with OpenWRT. 

I created a time based traffic rule that rejects traffic coming from the MAC addresses of each device I wanted to block, then set it to block from 9:00 PM until 5:00 AM the next morning. This worked for a while, until I realized this setup would only block new connections. Existing connections, for example, if someone started playing on an online Minecraft server before 9:00 PM, would not be severed at 9:00. 

I discovered that severing these connections required running a Conntrack command to drop all existing connections to the IP addresses in question. I statically assigned the devices, installed Conntrack, and created a Cron task:

```bash
# Drop all connections on specified devices at 9:00 PM Monday through Thursday.
1 21 * * 1,2,3,4 conntrack -D -s 10.40.0.20 && conntrack -D -s 10.40.0.21 && conntrack -D -s 10.40.0.22
```

Now everything was working how I wanted it to!

But as always, I wanted to improve this system. It all worked great, but occasionally I would want to disable it to keep the internet on longer. Logging into the router to disable the rule was clunky, so I decided to create a Shortcut with Apple's Shortcuts app to make the job easier.

I installed an awesome little package called luci-app-commands. This package allows you to create scripts, then run them by opening a URL on a locally connected device.

I created a few different scripts, and created a Shortcut that allows me to cancel or re-enable a traffic rule for the day.

{{< figure
    src="shortcut.png"
    alt="Screenshot of the shortcut in action, displaying several options"
    caption="I added a few other functions I could control with just a few taps!"
>}}

I've since added the ability to pause the internet to those few devices at any time with just a single button press, and added a second traffic rule that starts blocking at 11:00 PM every night.

Of course, to make sure everything is reset for the next day, a simple cron task runs each morning:

```bash
# Enable/disable custom rules that may have been disabled/enabled previously. Every morning at 6:00.
0 6 * * * uci set firewall.@rule[0].enabled=0 && uci set firewall.@rule[1].enabled=1 &&  uci set firewall.@rule[2].enabled=1 && uci commit firewall && service firewall reload
```

While the setup process is more extensive than I would have liked, I am quite happy with the result! Being able to control internet connection to specific devices faster and easier than even Google's system has been very handy! 