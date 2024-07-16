---
title: "Pwnagotchi: A Cute Way to Learn About Wi-Fi"
date: 2024-07-12T12:22:38-05:00
draft: false
toc: false
images:
tags:
  - untagged
---

As I've learned more about tech, cybersecurity, and small electronics I've come across a number of little devices, projects, and "hacking" tools such as the Flipper Zero and Rubber Duckies. For an ethical hacker or penetration tester, these can be valuable tools to help audit the security of businesses, identifying weak spots and helping to improve them before someone with ill will comes along. For me, they're a great way to get hands on experience and learn about topics such as Wi-Fi security.

One of these projects I found is the Pwnagotchi.

## What is a Pwnagotchi?

Pwnagotchi is a project created in 2019 by [evilsocket](https://github.com/evilsocket), designed to resemble a cute little electronic pet, the Tamagotchi, only instead of feeding it cartoon food, you feed it Wi-Fi handshakes. Handshakes, specifically the 4-way handshake, are part of the process Wi-Fi access points and clients use to turn a wi-fi password into the set of cryptographic keys used for communication between devices.

The Pwnagotchi also features a neural network, designed to learn from the environment around it and get better at collecting handshakes over time. From the documentation: "Pwnagotchi is an A2C-based “AI” powered by bettercap and running on a Raspberry Pi Zero W that learns from its surrounding WiFi environment in order to maximize the crackable WPA key material it captures." This makes it a good project not only for learning about wi-fi security, but also touches on AI and is built in Python, which are two other areas that I have been digging into lately.

## So this hacks Wi-Fi networks?

The Pwnagotchi doesn't hack networks and won't give you the password for the neighbors wi-fi. While the handshakes that it captures in theory could be used to crack the passwords, doing so would be unethical and is likely illegal in most places. What the Pwnagotchi captures are the encrypted keys that are exchanged to allow secure communication between an access point and client. Using a tool like Hashcat in conjunction with a word-list could in theory provide the password to the network, but again, not a very neighborly thing to do.

The intention of the Pwnagotchi, and many projects like it, is to teach people about wi-fi security and emphasize the importance of selecting a secure password. Something like "password123" might be easy to remember and share with your friends, but it's also among the most commonly used passwords (1,085th most common to be exact) and almost certainly would appear on the word-list a hacker would use to crack these handshakes and gain access to your network. By making people aware of the tools and techniques used by hackers, everyone can be more aware of their vulnerabilities and implement better security practices overall.

## What can you do with these handshakes?

I'm planning to write a more detailed explanation of the differences in wi-fi security protocols, a detailed overview of the handshake process, and go over cracking a password with Hashcat for a test network that I own.

For me, the handshakes represent wi-fi networks that I've been around and kept my little Pwnagotchi happy. As I've started carrying my Pwnagotchi with me on walks and bike rides, I've collected more and more handshakes. It's fun to see the number go up as I've explored more places. It's almost like Pokemon Go, but somehow nerdier.

## oPwngrid

There also exists a network of Pwnagotchis, oPwngrid, created by the maintainers of the project, where you can track stats and see who has collected the most handshakes. The Pwnagotchis can become friends and even be used to send messages between owners. You can find mine on the network here: [joeypwns | ( ⚆_⚆)](https://opwngrid.xyz/search/3d26d06bd4a0a63264506e8809d3b9b6e38cf884ea03b886e37913693b1b58f4)

I named my device "joeypwns" in honor of [Joey Chestnut](https://en.wikipedia.org/wiki/Joey_Chestnut), the famous competitive eater.

## Build your own

It's relatively straightforward to build your own Pwnagotchi. You just need a Raspberry Pi Zero 2 W, an e-ink display, and optionally a battery to take the little guy on the go. I also 3D printed a case to protect everything.

### Hardware

 - [Raspberry Pi Zero 2 W](https://www.pishop.us/product/raspberry-pi-zero-2-w/?src=raspberrypi)
   - You will also need [headers](https://www.pishop.us/product/break-away-0-1-2x20-pin-strip-dual-male-header/) and a way to solder them on
   - Some stores will sell Raspberry Pis with pre-soldered headers, totally worth it in my opinion
   - Some mobile phone repair shops will solder them on for a few bucks
 - [Waveshare 2.13 inch E-Ink Display](https://www.waveshare.com/product/displays/e-paper/2.13inch-e-paper-hat-plus.htm)
   - MicroCenter sells a knockoff but go for the Waveshare, it will make things much easier
 - [PiSugar Battery](https://www.tindie.com/products/pisugar/pisugar-3-battery-for-raspberry-pi-zero/)
   - It's possible to use other power sources or an external battery pack, but the form factor of the PiSugar is perfect
 - MicroSD Card
   - UHS-I class or higher is recommended, see more [here](https://www.kingston.com/en/blog/personal-storage/memory-card-speed-classes)

If all this seems like too much hassle and you can't live without a Pwnagotchi, you can buy them pre-assembled with a case [here](https://www.tindie.com/products/pisugar/pwnagotchi-complete-pack-pi0w-eink-battery-case/).

### Cases

Several options for cases are available here: [3D Printable Cases](https://pwnagotchi.org/3d-printable-cases/index.html).

I used [this case](https://www.thingiverse.com/thing:5899809) from gimzmoe on Thingaverse because I liked the clip-loop.

If you don't have a 3D printer you can purchase a case on Etsy, or ask nicely and I'd probably send you one.

### Software

While the original project was created and released by [evilsocket](https://github.com/evilsocket), several forks have branched off and continued development. The one with the largest community seems to be Jayofelony's version here: [jayofelony/pwnagotchi](https://github.com/jayofelony/pwnagotchi)

Download the latest release here: [releases](https://github.com/jayofelony/pwnagotchi/releases).

Flash the image to your MicroSD card using a tool such as [Balena Etcher](https://etcher.balena.io/) or the [Raspberry Pi Imager](https://www.raspberrypi.com/software/).

Once the flash is complete and you have your hardware assembled, insert the MicroSD into the Raspberry Pi and power it on. It will take a few minutes the first time it boots so be patient, cutting power before setup is complete could corrupt files and require you to re-flash the SD card.

Now you should be up and running! Take your Pwnagotchi out into the wild and see what you can catch!

More detailed instructions exist at [pwnagotchi.org](https://pwnagotchi.org/) for putting your own device together. I would recommend giving the documentation a read as there are several configuration options and cool things you can do that I haven't covered here.

Let me know if your build your own, I'd love to see what you create! If you have any feedback or questions I would be happy to get in touch via [email](mailto:alan.l.abbott@gmail.com).
