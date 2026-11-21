---
title: 'OpenWRT on a FortiGate 50E'
date: '2026-03-11'
lastmod: '2026-11-20'
draft: false
ongoing: false
description: "My experience so far with OpenWRT"
summary: "Reflashing and configuring an old Fortinet firewall with OpenWRT"
slug: "openwrt"
tags: ["OpenWRT", "Fortinet", "Networking", "Unifi", "Shortcuts", "DIY"]
showDateUpdated: true
---

A few months ago, I brought a used FortiGate 50E firewall home from work that I had replaced with a Unifi firewall. Previously, I had been using Google's Mesh WiFi system, and had wanted to replace it for quite some time. I had some experience with Fortinet's user interface, but was curious what other open source options existed out there.

I looked at several options, and finally decided on [OpenWRT](https://openwrt.org/), a simple, open source firewall firmware. It supports the 50E, and was said to be easier to install and learn than other firmware options.

Following the [installation instructions](https://openwrt.org/toh/fortinet/fortinet_fortigate_50e) for my model, I set up a [TFTP server](https://pjo2.github.io/tftpd64/) on my laptop, powered on the firewall, interrupted the boot process through a console connection, and forced it to pull the OpenWRT files from my laptop. It booted successfully, and I got to work figuring out how to use it!

I followed a few other tutorials to set up the DHCP and firewall rules, and soon had it working perfectly! I also replaced my Google Mesh system with a few Unifi APs I had also gotten used from work (finally a network I can take control of!).

## Internet Downtime for Devices

My next challenges was to implement some kind of downtime for specific devices, something that was easy to accomplish on the Google Mesh system. I needed the TVs and computer to be blocked from accessing the internet after 9:00 PM each school night. I created a time based traffic rule that rejects traffic coming from the MAC addresses of each device I wanted to block, then set it to block from 9:00 PM until 5:00 AM the next morning. 

{{< figure
    src="9FirewallRule.png"
    alt="Screenshot of the firewall rule in OpenWRT"
    caption="The firewall rule I created to block traffic from specific devices after 9:00 PM"
>}}

This worked for a while, until I found out that this setup would only block the initiation of new connections. Existing connections—for example, if someone started playing on an online Minecraft server before 9:00 PM—would not be severed at 9:00. 

I discovered that severing these connections required running a Conntrack command to drop all existing connections to the IP addresses in question. I statically assigned the devices, installed Conntrack (System → Software), and created a Cron task from the web interface (System → Scheduled Tasks):

```bash
# Drop all connections on specified devices at 9:00 PM Monday through Thursday.
1 21 * * 1,2,3,4 conntrack -D -s 10.40.0.20 && conntrack -D -s 10.40.0.21 && conntrack -D -s 10.40.0.22
```
(Conntrack cannot use MAC addresses, so the addresses above are the static IPs I assigned to the devices I wanted to block.)

Now everything was working how I wanted it to!

## iOS Shortcuts Integration

But, as always, I wanted to make some improvements. It all worked great, but occasionally I would want to disable it to keep the internet on longer. Logging into the router to disable the rule was clunky, so I decided to create a Shortcut with Apple's Shortcuts app to make the job easier.

I installed an awesome little package called [luci-app-commands](https://openwrt.org/packages/pkgdata/luci-app-commands). This package allows you to create scripts, then run them by calling a URL on a locally connected device.

Here's the script that disables the blocking rule:

```bash
# !/bin/sh

# Make sure the selected rule is one of the three custom rules I created
case "$1" in
    0|1|2)
        uci set firewall.@rule[$1].enabled=0 &&
        uci commit firewall &&
        /etc/init.d/firewall reload
        ;;
    *)
        echo "Error: Invalid firewall rule number."
        exit 1
        ;;
esac
```

Which is tied to a URL like this using the luci-app-commands package:

```https://openwrt.local/cgi-bin/luci/command/cfg0d3256?args=[Firewall Rule Number]```

The Firewall Rule Number is the index of the rule in the firewall configuration. I have mine set up this way so I can use the same URL and script to disable any of my custom rules. The script ensures that only the three firewall rules can be changed this way.

I created a few different scripts, and created a Shortcut that allows me to disable or re-enable a traffic rule for the day.

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

While the setup process is more extensive than I would have liked, I've been quite happy with OpenWRT! Being able to control the internet connection to specific devices faster and easier than even Google's Mesh WiFi system through Shortcuts has been very handy! 