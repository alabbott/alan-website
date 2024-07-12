---
title: "My DIY Data Center: How I Built Enterprise IT Skills at Home"
date: 2024-07-11T17:51:10-05:00
draft: false
toc: false
images:
tags:
  - untagged
---

I have hosted home servers on and off (and been a huge nerd) since I was 12 years old. I started off running a LAMP (Linux, Apache, MySQL, PHP) server on a tower PC my dad pulled out of the trash. I wanted to publish the websites I was building to show off to my friends and family but my allowance didn't go very far when it came to paying for web hosting. Thus began my journey into the world of tech and IT, building and hosting servers at home.

While things are a little more complicated now than simple HTML sites and CI/CD consisting solely of FileZilla, some things, like my hosting budget, stay the same. How much does a Standard_D4_v4 on Azure cost nowadays anyway? 

Building and hosting a server at home has been a great way to learn new skills and host some small services on a budget. In this post, I'm detailing my homelab journey, from a single server to a multi-node cluster, as well as the skills I have learned along the way.

## Why build a homelab?

I have found building a homelab to be a great way to build relevant IT skills and put things into practice, beyond simply learning the theory. Taking an action oriented approach forces me to consider the real world implications of different technologies rather than only understanding how something is *supposed* to work, or situations where I *might* use them.

Building a homelab has also taught me some important lessons, such the importance of reliability when my "users" (my wife) couldn't access my "services" (turning on our smart lights) or the impact certain choices have on total cost of ownership, eg: my cloud budget and electricity bill.

## First Iteration - Starting Small

My first server was a single small form factor Dell OptiPlex PC. These small tower PCs make a great choice for the beginner homelabber as they are small, quiet, energy efficient, and can be found secondhand for under $100, often as low as $50-$60.

While it may seem like a good idea to pick up a rack mounted option like a used Dell PowerEdge or HP ProLiant, consider the size, noise, and power consumption of such a system. These enterprise grade servers are large, loud, and power hungry, potentially costing over $200/year to run 24/7. Not very living room friendly. The skills and concepts apply to small servers just as much as big ones.

### Hardware
 - Intel Core i5-6500 (4 Cores)
 - 8 GB RAM
 - 256 GB SSD, 1 TB HDD
 - Onboard Intel I219-V 1GbE NIC

 Power consumption at idle: 15-20 watts

### Network
I connected my single server into my router and assigned it a static IP address on the existing 192.168.x.x subnet. I had some sense and kept the static IP out of the DHCP range, but otherwise had very little networking know-how, which was fine to start out.

### Software - VMWare ESXi
I started with a free version of VMWare's ESXi hypervisor; this let me practice using a similar system to the one I was using at work, without the pressure of breaking anything critical. I never got past a couple VMs, as my hardware wasn't really cut out for it, but it let me practice the concepts.

### Total Costs

 - Hardware: $40 on Facebook Marketplace
 - Power Consumption: ~$20/year
 - Licensing: Free

 For under $50 I was up and running, learning new things, and growing my skills rapidly.

 ### Skills Learned

  - Choosing the right hardware for the job
  - Installing an OS on bare metal
  - Working with VMWare ESXi
  - Creating and using virtual machines
  - Some basic networking, like setting static IPs and avoiding clashes with DHCP

Overall, this first iteration of my homelab was a huge success. I was rapidly building skills and learning new concepts that would help me every day at work. Most importantly, it took me out of theory and into the real world, giving me a practical foundation on which I could continue to build.

## Second Iteration - Move to Proxmox, Adding Nodes
I quickly ran into two bottlenecks — my server wasn't very powerful, and free ESXi has a lot of limitations. This led me to explore other options and discover [Proxmox](https://www.proxmox.com/en/), a free open-source hypervisor based on Debian Linux. It also led to me purchasing another SFF PC to add resources to my homelab.

These additions let me begin to learn about high-availability and implement automatic backups and shared storage. I also began to host useful services such as [Joplin](https://joplinapp.org/) for synchronizing my notes and [Pihole](https://pi-hole.net/) for network level ad-blocking and DNS.

### Skills Learned
  - Linux administration, managing Proxmox
  - Automatic Backups
  - Network File System (NFS) and shared storage
  - DNS Filtering

As my homelab continued to grow, I continued to grow and develop my skill set and started to get some personal benefits as well. I had some momentum and felt like this was a platform with which I could scale and grow.

## Third Iteration - Clustering

I consider this a third iteration because, well, I broke things pretty thoroughly in the process. Thank God this wasn't a production system.

I purchased another SFF PC, this time an HP EliteDesk, which gave me a sufficient number of servers to create a Proxmox cluster. This allowed for central management of all servers, easily migrating VMs between servers, and high availability VMs.

Adding the third server also prompted me to reconsider my network. Not only was I using up more IP addresses, I was running out of physical ports, and wanted to be able to separate server network traffic from personal network traffic.

It was during the migration from my 192.168.x.x subnet to a 10.x.x.x subnet that I broke the cluster. This led me down several rabbit holes and I began to learn the intricacies of distributed systems. I was finally able to restore the cluster and not lose any data in the process, gaining a real appreciation for the challenges presented by complex systems.

I added a switch and used VLANs to segment my network, allowing me to control the flow of traffic between devices. It certainly isn't an enterprise data center, but it gave me a taste of configuring a more complex network, and thinking about the flow of traffic through the system. 

All of a sudden, tools like Ansible and Terraform made much more sense. Not just in a theoretical way but in a "I can't believe I have to update this in so many places" way. I started using automation and Infrastructure-as-Code tools to help me manage everything.

### Skills Learned

  - Setting up and managing a Proxmox cluster
  - High availability VM configuration
  - Network segmentation, configuring VLANs
  - Subnet design, implementing a new addressing scheme
  - Distributed systems, understanding the complexity of multi-node setups
  - Data preservation, not losing any information as I managed major system changes
  - Using tools like Ansible to make server administration easier

This has been the system on which I have continued to build. It has presented me with several challenges and opportunities. I'm really proud of the homelab I've built and the skills I've learned along the way. With this server cluster and network configuration I feel like I have something worthy of the title of "homelab". It has served as my DIY data center and I use it to host several services today.

## Final State, for now...
Currently, I still have three nodes in my cluster, plus a Raspberry Pi for backup DNS and VPN access. After a thoughtless reboot where I locked myself out of my network while on vacation, I decided I needed some redundancy for these key functions. I've added some RAM, some SSDs, and a networking card for a future OpnSense project, but the bones are the same.

#### Physical Servers:
 - Dell OptiPlex 7050 SFF: i7-7700, 8 cores, 16 GB RAM
 - Dell OptiPlex 5040 SFF: i5-6600, 4 cores, 16 GB RAM
 - HP EliteDesk 800 G2 SFF: i5-6500, 4 cores, 8 GB RAM
 - Raspberry Pi 4 8 GB

#### Network:
 - Layer 3: TP-Link ER605
 - Layer 2: TP-Link TL-SG105
 - APs: Netgear Orbi Pro WiFi 6 SXR/S30 in AP mode

#### VLANs:
  - Personal: PCs, phones, tablets, Apple TV
  - Servers: Proxmox nodes, VMs
  - IoT Devices: Light bulbs, smart plugs, ESP32s
  - Sus: Sketchy IoT devices, anything untrusted
  - Guest: Guest Wi-fi

### Total Costs

#### Hardware
 - $220 - SFF PCs
 - $40 - SSDs: 1 TB from MicroCenter, 1 TB for Christmas (Thanks Mom and Dad!)
 - $40 - Intel I350-T2 Dual Port 1GbE Card for OpnSense
 - $75 - Router/Switch - not including APs
 - $75 - Raspberry Pi

**Total Hardware Cost: $450**

#### Electricity
I would estimate the total consumption to average around 90 watts, incl.

90 Watts / 1000 W/kW x 24 hours/day x $0.1621/kWh * 365 days = $127.79

**Total Electricity Cost: ~$130/year**

###  All in for 3 years of Homelabbing: $831

If you're reading this and thinking this might benefit you, I highly encourage you to take the leap. I started with a single Dell SFF PC purchased secondhand for $40. Generally I have taken the "do more with less" approach in an attempt to save money and get creative with solutions. While it's the dream of many an IT professional to have a full rack in their closet, starting where you can is far better than not starting at all.

If you have any feedback or input I would be happy to get in touch via [email](mailto:alan.l.abbott@gmail.com). Let me know what you would do different or ask any questions about getting started!