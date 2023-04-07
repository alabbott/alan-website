---
author:
  name: "Alan Abbott"
date: 2022-08-10
linktitle: Home Network Project
type:
- post
- posts
title: Home Network Project
weight: 10
---

I recently moved into a new apartment and decided I needed to upgrade my home network. I still had a router that I got in college but I knew the technology had moved forward since then. I was also frustrated by my previous roommate’s wifi situation. I had dead spots in my room that made streaming difficult and I heard about it frequently.

We also qualify for fiber internet at the new apartment, and I could get near gigabit speeds for $55/month through RCN. I wanted to be able get that internet speed and come close to taking advantage of it on my network. Therefore, my requirements became a gigabit ethernet network for the home, plus wifi that is as reasonably fast as possible with good coverage of the whole apartment, including the back patio.

I also wanted to be able to VLAN segment my network and have separate wifi networks or SSIDs for each VLAN. I wanted to keep my smart devices from communicating with my personal devices, unless they needed to to perform a smart function. This requirement basically bumped me into small business / prosumer hardware, unfortunately.

I started looking at wifi, because this is really the only place I knew to look. I quickly decided I needed a Wifi 6 router, the latest and greatest protocol for the fastest speeds and most clients. Functionally this meant faster connection to my laptop and phone, since I was planning to hardwire the TV for streaming. The next questions were mesh or not? And do I want a separate router and access point (AP)?

I initially decided not to go for a mesh system, instead going for the cheapest Wifi 6 router I could find, this ASUS router. I planned to put it in AP mode and use a separate router for my VLANs and other traffic routing. This was a great plan until I realized you needed multi-SSID and VLAN support in the AP in order to have wifi devices on different networks. This meant to keep my light bulbs from hacking into my laptop, I needed an AP with VLAN and multi-SSID support. This directed me to the Orbi Pro Mini Wifi 6. This router was on sale I think at Micro Center because it had all the features I wanted, plus a mesh satellite, for only $40 more than the cheap ASUS. So this got me my AP for the network.

Next was the router / switches. I had decided in deciding my wifi AP that I would use a separate gigabit router for my network. This was motivated by getting a cheap Wifi 6 router and getting the rest of the required features from the router. When I upgraded to the Orbi, I didn’t reconsider my router choice. My system is working well for me, but I think I could maybe have used the Orbi for everything and skipped the separate router. Anyway. My router handles the most of the work for the network, and the Orbi APs give me multiple SSIDs to keep my devices separate.

I also used CAT6 cabling for all the runs between devices the the router. This lets me get gigabit speeds, fully capable of taking advantage of my fiber internet, anywhere with an ethernet cable. Currently I have a direct line to my home server, the Apple TV, and one under the couch for working in front of the TV. I also have my docking station hardwired in. This keeps me covered with fast internet most of the time, and the wifi is good and speedy everywhere else as well.

Overall probably not necessary, but it was a good learning exercise in setting up and managing a network. I also setup a Pi-hole DNS service so I am doing some basic ad-blocking and tracking prevention at a network level, but I’ll write about that later. It’s nice having fast internet all over the house. My nerdy inner child is so happy.