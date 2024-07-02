---
title: "Homelab Tour"
date: 2024-07-01T11:29:43-05:00
draft: false
toc: false
images:

---

I have hosted home servers on and off since I was 12 years old, initally hosting a [XAMPP](https://www.apachefriends.org/) server before moving to Ubuntu and running the LAMP (Linux, Apache, MySQL, PHP) stack. It wasn't until 2021 when I started working with the IT systems at work that I got interested in setting up a true homelab. I found myself overwhelmed with the scale and complexity of the systems I was working with, and wanted a lower stakes place to experiment and learn to use the tools I was working with daily.

## What is a homelab?

A homelab is a generally a server or collection of servers located in a person's home, used to *experiement* with new IT skills or tools, hence "lab." The purpose of such a setup is to try new hardware and software, learn new IT concepts, and practice in an environment with lower stakes than the production systems often found at one's day job. 

A homelab is similar to a home server, but usually differs in scope and intent. A tech enthusiast might host a home server, either on a dedicated PC or something like a Synology NAS (network attached storage) device, with a very specific purpose. Often these servers are used to stream media, host games, or store files for the owner. While a homelab can be as small as a single Raspberry Pi, it generally will feature multiple servers networked with each other, designed to mimic the type of architecture you would find in a professional production system.

Since then my homelab has taken on many forms, and been rebuilt more times than I'd like to think about. It has been a very rewarding experience however, and I often will apply a skill at work shortly after learning to apply it in my homelab. It has been very rare to not see an immediate return on the time invested in learning something new. 

If you're reading this and thinking this might benefit you, I highly encourage you to take the leap. I started with a single Dell SFF PC purchased from Facebook Marketplace for $40. Generally I have taken the "do more with less" approach in an attempt to save money and get creative with solutions. While it's the dream of many a nerd to have a full rack in their closet, starting where you can is far better than not starting at all.


## First Iteration - Starting Small

I began my homelab journey with a Dell OptiPlex 5040 SFF PC, picked up on Facebook Marketplace for $40. It had 4 cores and 8 GB of memory, which was pretty paltry compared to some of the rack mounted options available, but the cost couldn't be beat. These SFF PCs are still my server of choice while I live in a small apartment and they do 90% of what I want to do. They are also extremely quiet and consume much less power than their rack mounted couterparts. For someone on a budget, in a small space, or unsure of committing to the homelab they are an excellent choice.

### Hardware
Node 1:
 - Intel Core i5-6500 (4 Cores)
 - 8 GB RAM
 - 256 GB SSD, 1 TB HDD
 - Onboard Intel I219-V 1GbE NIC

 Power consumption at idle: 15-20 watts

### Network
I described my home network setup a couple years ago here: [Home Networking Project](/posts/2022/08/home-network-project/)

I just plugged my single server into my router and assigned it a static IP on the existing 192.169.x.x network. I had some sense and kept the static IP out of the DHCP range, but otherwise had very little networking know-how, which was fine to start out.

### Hypervisor - Free VMWare ESXi
Because I wanted my homelab to resemble the system I was using at work, I opted for a free version of VMWare's ESXi hypervisor. This let me practice with the same tools I was using at work and get comfortable making changes, breaking and fixing things, and my boss had less headaches when I inevitably messed something up.

I never got past a couple VMs, as my hardware wasn't really cut out for it, but it let me practice the concepts.

#### What is a hypervisor?
A **hypervisor** is a piece of software that allows you to run multiple *virtual machines* on a single PC.

A hypervisor can run on your typical OS like Windows 10/11 in the case of something like VMWare Workstation (Type 2 Hypervisor).

It can also run on the "bare metal" as an OS in the case of VMWare's ESXi or the open source Debian based Proxmox (Type 1 Hypervisor). 

### Total Costs

 - Hardware: $40 on Facebook Marketplace
 - Power Consumption: ~$20/year
 - Licensing: Free

 For under $50 I was up and running, learning new things, and growing my skills rapidly.

### Downsides - Licensing
The downside of this system pretty quickly became resources and features. My hardware was not really cut out for running ESXi and left little room for VMs on top. I also kept running into features only available with a paid license. This was fine for my goal of learning ESXi basics, but soon I wanted more out of my homelab. This led me to exploring other hypervisors and the first ground up rebuild.

## Move to Proxmox
My previous experience with linux and love for open source software led me to find [Proxmox](https://www.proxmox.com/en/), a free, open source hypervisor based on Debian Linux. Proxmox does offer subscriptions for greater support and stability but it doesn't restrict any features behing a paid license, which was perfect for me. It's a little different than ESXi, but I soon found myself more comfortable in Proxmox than I ever was with ESXi.

While I count this as the first teardown / rebuild, there wasn't really of substance running on ESXi, so instead of moving my Windows Server VMs over, I started with an Ubuntu VM running Docker. This is actually what I used to run most things, just an Ubuntu VM w/ Docker. I never explored LXCs and I liked the simplicity. Eventually though, I needed more resources, my poor OptiPlex was using 90%+ of it's memory constantly and I wanted to grow the homelab.

## Adding Nodes
I liked the Dell OptiPlex SFF size and simplicty so much I decided to go for another for my second server. I wanted more horespower so ended up picking up a OptiPlex 7050 SFF with an i7-7700 and 16 GB of RAM. It's not state of the art or anything, but it over doubled my resources. I did pay a little more for the i7, $120 total but it had a 1 TB hard drive, more RAM, and 8 cores.

I knew at this point I wanted to explore clustering and high availability, shared storage, instant failover, etc. After doing some research I learned that odd numbers are better for clusters and while I could probably hack something together with two Proxmox nodes and a Raspberry Pi, I decided to keep looking for another SFF PC.

I finally found a PC in price range, an HP EliteDesk 800 G2 SFF with an i5-6600 and 16 GB of RAM for $60. Pricier than my first server but I think I just got a great deal on that one. I generally see these SFF PCs on Facebook Marketplace for $60 at the low end to $200+ at the high end. Obviously the cheaper the better but look out for things like CPU generation, cores, and size.

The size of the EliteDesk really surprised me. I was assuming (foolishly) that it would be approximately the same size as my OptiPlexes but is in fact a good bit larger. Lessons learned. Let's call it added redundancy with another hardware platform.

Finally I had three servers running Proxmox, it was time to cluster.

## Clustering
The actual clustering process was pretty simple, just a few commands outlined in the [documentation](https://pve.proxmox.com/wiki/Cluster_Manager). 

#### Side Thought: Reading the docs
Generally speaking the documentation is the best tutorial you can find for most things, and if it's not making sense you usually need to go a level or two up and learn the basics. Although there are plenty of tech blogs with quick and dirty how to guides (much like the one you're reading now), usually the docs are the best place to start. 

Once you get really in the weeds with weird situations, that's when tech blogs, reddit posts, and StackOverflow will become your best friend. It's a weird kinship with strangers online who had your exact problem, hopefully they were kind enough to post their eventual solution.

{{< image src="https://imgs.xkcd.com/comics/wisdom_of_the_ancients.png" alt="relevant xkcd" position="center" >}}

## Networking
Of course reading the docs can't save you from your own stupidity. Immediately after I had my cluster set up and running well, I decided I wanted to segment my network, keeping the standard 192.168.x.x subnet for my personal devices, but move the servers and IoT devices to 10.x.x.x  subnets and add VLANs to keep everything separate.

This was a simple enough configuration change in my router, the reliable but often weird TP-Link ER605. The real issue came when I tried to reassign the IP addresses of the nodes in my cluster. Somehow I broke the whole thing and after a few hours decided to rebuild. I managed to move VMs from node to node as I rebuilt, saving my Ubuntu / Docker VM. I felt so satisfied when I finally had a running cluster on a 10.x.x.x subnet with it's own VLAN. I felt like I finally had something worthy of being called a "homelab."

This is the platform I have been building on for a couple years now. It has been remarkably stable and reliable. I have continued to add and remove VMs, host more services, and use it as an all purpose resource for anything I need. I'll recap where I am now and some of the things running today.

## Current State
Currently I have three nodes in my cluster, plus a Raspberry Pi for backup DNS and VPN access. After a thoughtless reboot where I locked myself out of my network while on vacation, I decided I needed some redundancy for these key functions. I've added some RAM, some SSDs, a networking card for a future OpnSense project, but the bones are the same.

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
  - Guest: Guest Wifi

### Total Costs

#### Hardware
 - $220 - SFF PCs
 - $40 - SSDs: 1 TB from MicroCenter, 1 TB for Christmas (Thanks Mom and Dad!)
 - $40 - Intel I350-T2 Dual Port 1GbE Card for OpnSense
 - $75 - Router/Switch - not including APs
 - $75 - Raspberry Pi

**Total Hardware Cost: $450**

#### Electricity
I don't know the exact wattage of the system, although I would love to do a project to monitor this number. I would estimate the total consumption to average around 90 watts, which includes the network hardware which I would be running anyway.

90 Watts / 1000 W/kW x 24 hours/day x $0.1621/kWh * 365 days = $127.79

**Total Electricity Cost: ~$130/year**

###  All in for 3 years of Homelabbing: $831

While this might seem excessive to some, and I confess I did get a little sticker shock running the numbers, a similarly capable server on Azure would run $347.48/month or $12,509.28 over 3 years. Of course Azure is a little nicer than my little cluster, but for the capabilites, experience, and lessons learned, I think building your own homelab is a steal.

## Services Hosted
One of the things I really like about having a pretty robust homelab is the ability to spin up and blow away services pretty easily. If I read about a cool project (like [Automatisch](https://automatisch.io/)), I can have it running and accessible from anywhere with a few quick steps. It makes trying out new things really easy and fun. Not everything sticks around, but I love to check out and support new things.

Some of these are services or applications that I use as an "end user" and others are more infrastructure to keep everything running. I'll break them down into a few categories below.

### Infrastructure
My core infrastructure services include a reverse proxy (Traefik), authentication (Authentik), automatic updates (Watchtower), DNS (Pihole), VPN (Wireguard), and XDR/SIEM (Wazuh). Most, but not all, services are run in Docker using Docker Compose. Backups are handled by Proxmox and run nightly. I also keep all of my docker-compose files stored in a repository for change tracking and easier recovery, should I need to rebuild. Finally, I use Cloudflare Tunnels and Zero Trust tools to provide remote access to my services while keeping everything secure.

#### [Traefik](https://traefik.io/)
Traefik is a reverse-proxy and load balancer, which essentially means it hands off requests to the appropriate services as they come in. Traefik is easy to configure, once you figure it out, and integrates with other tools to make deploying services really easy.

Traefik can [pull configurations from docker-compose files](https://doc.traefik.io/traefik/user-guides/docker-compose/basic-example/), which makes deploying new services a one-file exercise most of the time. The endpoint, port, and host can all be defined in the docker-compose file alongside the rest of the stack. Using Docker networking you can block all connections to your services except through Traefik, which can force authentication and enforce other rules.

Traefik middlewares can utilize authenticaiton from Authentik or other identity providers and enforce access rules.

Finally, Traefik can work with Let's Encrypt to automatically get certificates for your services, making deploying on HTTPS a breeze.

#### [Authentik](https://goauthentik.io/)
Authentik is an open source identity provider that integrates fairly seamlessly with other platforms, including Proxmox and Traefik, as well as some of the other services I host. I opted for Authentik over [Keycloak](https://www.keycloak.org/) or [Authelia](https://www.authelia.com/) because it seemed like it offered a good balance between capabilities and simplicty.

A potential downside of Authentik is that it's relatively young, and becuase of that I'm planning to switch to Keycloak in the future. Keycloak is also more common and getting some experience with it would be a benefit to me.

#### [Watchtower](https://github.com/containrrr/watchtower)
Watchtower will update containers to the latest version automatically. For me, who runs most of my services in Docker, this is great. While automatically updating to the latest image probably isn't the best idea in a production system, I know myself well enough to know it could be weeks or months between logins to my homelab, so keeping things at the latest version works for my use case.

#### [Pihole](https://pi-hole.net/)
Pihole is a DNS server that is designed to block ads and provde DNS level filtering of web content. I originally used Pihole solely for ad-blocking, but as I did more and more with my homelab, it's become an all purpose DNS server. Is it the best tool for the job? Probably not. But it works and I'm familair with it.

I currently have a Pihole container running in Docker on my main cluster, and a backup running on a Raspberry Pi, for those situations where I reboot the server and still need the internet to work.

#### [Wireguard](https://www.wireguard.com/)
Wireguard is a really fast and simple VPN, using [modern cryptography](https://www.wireguard.com/protocol/). I use Wireguard when I need remote access to my home network beyond what Cloudflare Tunnels or Guacamole can give me. I've also used it to help my wife attend Zoom meetings from an airplane and help friends access various services while travelling abroad. It has been a really nice option to have ready to go when needed.

Additionally, I use [wg-easy](https://github.com/wg-easy/wg-easy) to provide a nice UI for managing the keys and configuratiuons. 

#### [Wazuh](https://wazuh.com/)
Wazuh is an open source security platform that provides XDR and SIEM capabilities. I use Wazuh to monitor security events on all of my servers. The intallation of the agent is really easy and makes monitoring things much easier.

#### [Cloudflare Tunnels](https://www.cloudflare.com/products/tunnel/)
Cloudflare Tunnels allows you to expose services running on your home network without actually exposing your home network. You can also tie into other Cloudflare Zero Trust tools like Applications and WAF for everything from access controls to DDoS protections. If properly networked, you can allow the tunnel client ([cloudflared](https://github.com/cloudflare/cloudflared)) access to only the services you want to expose, further limiting what could be exposed on the open internet. 

#### [K3s Kubernetes](https://k3s.io/)
In addition to the stack above, I have a small k3s cluster running with nodes spread across each Proxmox host. It's far from a perfect high availabilty system, but it's allowed me to get more familiar with Kubernetes and I hope to move more of my services to k3s over time.

### Administration
I keep Portainer running as a GUI for my docker-compose stacks, although I find myself more often than not just using VSCode Remote and the terminal to update things. I also run Uptime Kuma to monitor the availabilty of everything, but candidly usually if something is down so is Uptime Kuma, so this might get moved to the Raspberry Pi. I also have Rancher running for my small k3s cluster, which I expect to become more important as I move services from Docker to k3s over time.

I also run Apache Guacamole for remote access via web browser. I really like being able to have a central place to SSH or RDP into all my various systems.

#### [Portainer](https://www.portainer.io/)
Portainer is a container management tool that and help deploy and troubleshoot applications. It integrates directly with the Docker socket and is really easy to use. It also allows for single sign-on with Authentik, which is nice. Because I've deployed everything from docker-compose, and gotten more comfortable in the terminal, I've trended towards just working in the terminal, but it's nice to have a GUI sometimes.

#### [VSCode Remote SSH](https://code.visualstudio.com/docs/remote/remote-overview)
This isn't really a service, more a way of using VSCode, but it has become a pretty key workflow for me. Using VSCode to edit configuration or docker-compose files, and using the built-in terminal I can do nearly anything on all of my servers, and the process is really easy and seamless. Definitely recommend checking out the remote SSH capabilities of VSCode.

#### [Guacamole](https://guacamole.apache.org/)
Apache Guacamole is a clientless remote desktop gateway. Essentially it lets you stream a remote desktop or SSH connection to any device through your browser. It's snappy and reliable, and it has been a great tool to have in the back pocket when away from home.

#### [Uptime Kuma](https://github.com/louislam/uptime-kuma)
Uptime Kuma lets you create a nice status page for all of your self-hosted services. If you're providing access to your services to more than just yourself it can keep people informed of updates or downtime. I mostly use it to track how well I'm keeping everything running. It's satisfying to see a page full of green when everything is ticking along.

#### [Rancher](https://www.rancher.com/)
Rancher is a tool for deploying and managing Kubernetes clusters. Candidly, I'm not using Rancher for much since I don't have much on my k3s cluster, but it's installed and there for when I start to migrate things over.

### End User Apps
These are the fun ones! This is also the category that changes the most week to week. I'll go through the staples below.

#### [NextCloud](https://nextcloud.com/)
One of the perks of hosting your own services is not paying for cloud file storage. NextCloud lets you host your own OneDrive or Google Drive alternative. Admittedly it's a resource hog, a bit clunky, and I don't trust myself enough to let this be my only storage option. It still provides a nice UI and file sync tool. It has capabilities far beyond what I'm using it for if you're looking for a full Office 365 alternative. 

I think I could probably let this one go, as it is a resource hog, but the polished UI has kept me from killing it and it has saved me a couple times by letting me access certain files when away from home.

#### [Joplin](https://joplinapp.org/)
Joplin is a notes platform with desktop and mobile apps, similar to Evernote or other notes platforms. I used to have an Evernote subscription but wanted to try my hand at self-hosting my notes. I've been nothing but satisfied with Joplin. I definitely recommend it to anyone looking for a free, open source alternative to Evernote or others.

#### [OneDev](https://onedev.io/)
OneDev provides git repositories as a self-hosted alternative to GitHub. I really like the UI when compared to GitLab or Gitea. There are several options for self-hosting repos but the nice thing about self-hosting is if you want to pick the prettiest one, you're free to do so!

I use OneDev for personal projects, backing up docker-compose and other config files, and the rest of the stuff you use git repos for. I still put stuff on GitHub for visibilty but I like having a private option available as well.

#### [HomeAssistant](https://www.home-assistant.io/)
HomeAssistant is awesome and frustrating and incredible all at once! It is essentially a smart home IoT platform that provides control of smart home devices, automations, integrations, data logging, and more. It took me a while to understand the true power of HomeAssistant but the gist is you can do anything you want, if you're willing to learn how to configure it.

I use HomeAssistant to collect data from my IoT sensors, control a few smart home devices, and send all the data to InfluxDB for logging. I used to use it more heavily, controlling every light in the house, but the occasional outage led to complaints from other users (my wife) and helped me learn the importance of reliability and maintainability. 

HomeAssistant can act as a bridge to Apple Home which means you can use HomeAssistant to do more complex automations but still play nice with the Apple ecosystem. HomeAssistant has its own app as well, if you want to fully make the switch.

#### [ESPHome](https://esphome.io/index.html)
ESPHome is a system for controlling microcontrollers and IoT devices with simple configuration files. I'm still blown away with the power of ESPHome combined with inexpensive ESP32 or ESP8266 microcontrollers. It felt like a whole new world of IoT possiblities opened up when I learned about this platform.

I currently have several ESP32 devices around the house doing things from environmental monitoring of temperature and air quality to controlling motors and showing room occupancy. If you're a smart home or IoT enthusiast, I can't recommend ESPHome enough.

#### [InfluxDB](https://www.influxdata.com/)
InfluxDB is a popular open source time-series database. I use InfluxDB to store all the data from my various sensors around the house. I like that it's simple to use and integrates well with Grafana.

#### [Grafana](https://grafana.com/)
Grafana is an open source analytics and monitoring tool. It integrates well with InfluxDB and other data sources and makes it really easy to build dashboards to see data in different ways. I have various dashboards for air quality, weather, power consumption, and the homelab itself. Grafana is also a common addition to Cognite DataFusion, so my Grafana skills have directly translated to benefits at work, which is wonderful.

#### [Mealie](https://mealie.io/)
Mealie is a simple recipe management app. Surprisingly, this is probably the service I use the most out of everything and I would be devastated if I lost this data. Mealie will pull in recipes from a link and organize them, put them in a common format, and will even help with meal planning and grocery lists. While I do use the rest of my self-hosted services, this is the one I come back to nearly every day. Highly recommend Mealie if you're looking to get some utility out of a self-hosted homelab.

### Other Options
The GitHub repo [Awesome-Selfhosted](https://github.com/awesome-selfhosted/awesome-selfhosted) contains lists of services that can be self-hosted in a homelab or home server setup. It has more services than I think I could ever try, and there's something in there for everyone.

The subreddit [/r/selfhosted](https://www.reddit.com/r/selfhosted/) is another great resource for finding and getting help with various self-hosted services.

## Future Plans

My current areas of interest are cybersecurity, infrastructure, cloud computing, and machine learning. I would like to move the homelab in a direction to support learning in these areas.

### Planned Projects

 - Switch to Keycloak from Authentik
 - Move services to k3s, increase resiliance of system
 - Implement shared storage such as CephFS or Longhorn
 - Build SAN with 10Gpbs network
 - Implement terraform and ansible, learn infrastructure-as-code tools
 - Practice machine learning on the data collected by my IoT devices

## Conclusion
This concludes the tour! I hope you have found this interesting and helpful. It has been a long journey consisting of lots of frustration and late nights, but the lessons learned are priceless to me.

For learning tech skills, there's nothing like actually doing it to build your understanding of different systems and the complexity of all the moving parts. Additionally, keeping key services running has taught me lessons about updates, backups, reliability, and the importance of keeping your users happy (aka my wife).

This is just the start, and represents hundreds of tradeoffs between the right way and the most practical way, and of course constrained by time, budget, and physical space. Still, I'm proud of the system I've built over time and the skills I've developed along the way.

If you found this helpful feel free to [buy me a coffee](https://buymeacoffee.com/alabbott), and if you have any feedback or input I would be happy to get in touch via [email](mailto:alan.l.abbott@gmail.com).