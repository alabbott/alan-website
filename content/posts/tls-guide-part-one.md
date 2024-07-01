---
title: "SSL/TLS Guide Part One: Public/Private Keys"
date: 2024-06-03T18:26:20-05:00
draft: false
toc: false
images:

---

Transport Layer Security (TLS) or Secure Sockets Layer (SSL) protocols are the primary method for securing communications between servers and clients on the internet or other networks such as a plant floor or enterprise network. TLS is the successor to SSL, however the terms are used interchangeably, both referring to the newer TLS protocol. Without using TLS to encrypt the communication between servers and clients, their communications could be exposed to 3rd parties intercepting and possibly spoofing messages. With cyber-attacks on industrial manufacturers increasing, securing all production and development networks becomes increasingly important everyone from Controls Engineers and Developers to CEOs and CISOs.

Managing TLS and certificates can be a huge pain as well, especially in environments that can go long stretches between use and be shared by many users. We're probably all too familiar with the security warnings about certificate errors, either showing as insecure or worse, not loading at all.

In this guide, I will break down the pieces that come together to form the TLS protocol and go over some tips for working with certificates. Understanding the basics helps make sense of errors and makes the whole ordeal a little less daunting.

### Contents
1. [**Part One: Public/Private Keys**](/posts/2024/06/ssl/tls-guide-part-one-public/private-keys/)
2. [Part Two: Certificates](/posts/2024/06/ssl/tls-guide-part-two-certificates/)
3. [Part Three: Tools for Working with Certificates](/posts/2024/06/ssl/tls-guide-part-three-tools-for-working-with-certificates/)

## A precursor: Identity, Privacy, Trust, and Security

If two people want to send messages to each other they can pass notes using a messenger. This would not be very private however, because the messenger could easily read a message before delivering it. It would also not be very secure because the messenger could alter the message before delivery. There is no way to ensure or trust that the message is really from the sender or that it hasn't been changed. The identity of the sender can't be verified by the recipient.

The two people could implement a code, where they shift A to B, C to D, and so on. However, this would require them to agree upon this code via an outside channel. It would not be possible to establish trusted communication without meeting in person, or using an existing outside channel.  This is sometimes referred to as a shared secret.

If two people need to exchange information securely without meeting previously, such as webservers and a new client, they can use public and private keys to accomplish this. The use of public and private keys has many advantages, such as private communication without a shared secret, as well as verifying identity and providing trust through things like signatures and certificate chains.

It helped me to understand the various pieces of TLS certificates when I broke out the ideas of privacy, identity, and trust.

 - Privacy - Communication between parties can't be read by outside listeners
 - Identity - Can you verify who sent you that message and that it wasn't modified?
 - Trust - Can I verify that you are who you say you are?

In my mind, these are the core concepts behind "secure" communication. When we say we have a secure connection to a server or device, we mean we can verify the identity of the sender, the integrity of the message, and not worry that 3rd parties can read what we're sending. Hopefully by the end of this guide you will feel comfortable with the way TLS certificates provide privacy, identity, and trust which will make using them, and fixing them, much easier.

## Public Keys and Private Keys

The most important concept in the TLS protocol is public keys and private keys, which make up **public key infrastructure (PKI)**. In reality these keys are just very, very large numbers used in cryptography algorithms, stored in certain specific formats. These keys enable everything in TLS with two functions.

### Two important functions:

 - Messages encrypted with the *public key* can only be decrypted with the *private key*
 - Messages encrypted with the *private key* can only be decrypted with the *public key*

If *Person A* wants to send a message to *Person B*, they would each generate a keypair (public and private key) and exchange **public keys**, keeping their **private keys** secret. It doesn't matter if the public keys are intercepted, as they are public information and only represent the identity of the private key holder.

#### The process for conversation would be as follows:

1. For *Person A* to send a message to *Person B*, they would use *Person B*'s **public key** to encrypt the message


2. *Person B* would use their secret **private key** to decrypt the message, only readable to them

#### To respond:

1. When *Person B* sends a message to *Person A*, they would use *Person A*'s **public key** to encrypt the message


2. *Person A* would use their secret **private key** to decrypt the message, only readable to them

This system is great for ensuring privacy, as only the intended recipient can decrypt and read the message. It is not, however, a great system for verifying identity and providing trust. We must talk about signing and verification to understand the other side of public key infrastructure.

Remember, private keys can also encrypt messages that can only be decrypted by the public key, and the public key is widely available and tied to the key holder's identity. To verify their identity, the private key holder can use their secret key to encrypt a message, only decryptable by their public key. This process is known as signing the message.

When the recipient gets that message, they will use the sender's public key to confirm they are the one who encrypted it, in a process called verification. Because the public key is tied to the identity of the private key holder, the recipient can be certain the message is from the sender and hasn't been changed in transit.

#### The process for signing a message would be as follows:

1. *Person A* uses their **private key** to encrypt a message, and sends it to *Person B*


2. *Person B* uses *Person A*'s **public key** to decrypt the message, confirming it is from *Person A*

#### The process for verifying a message:

1. *Person B* receives an encrypted message from *Person A*, who claims to have signed it using their **private key**


2. *Person B* successfully decrypts the message using *Person A*'s **public key**, confirming it was sent by *Person A*

This system may seem to check all of our boxes, allowing for two-way, private communication where the identity and integrity of each message can be verified. However, we are required to trust that Person A and Person B are who they claim to be. How can we be sure it is indeed Person A and Person B who hold those private keys? If we had a signed message from someone we trusted, we could verify that Person A's public key (and private key) belong to them.

### Cryptography Nerd Moment

The above system roughly represents PGP (Pretty Good Privacy) which has been around for over 30 years and is pretty much the standard for encrypting or verifying communications. Beyond just messaging, PGP can be used to sign and verify blocks of code or even entire pieces of software. A developer can sign a package, and provide the signature. Using their public key and cryptography software, you can verify that the program is not corrupted, either accidentally or maliciously.

A limit of PGP is that it relies on a "web of trust" where individuals will endorse each other's public keys, confirming they belong to the claimed owner. While this decentralized model may work well in some situations, a system was needed for a more streamlined method of trust management, leading to X.509 certificates, which are described in the [Part Two](/posts/2024/06/ssl/tls-guide-part-two-certificates/) of this guide.

There are several algorithms that can be used to generate the key pairs, such as RSA or IDEA, but they all use incredibly large numbers and mathematical processes that would be practically impossible to undo using modern computers. A description of the math behind creating keys can be found here: [RSA (cryptosystem) - Wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem))

There is the possibility that quantum computers could very quickly break the encryption used in PGP and TLS communication. This would mean that virtually all information encrypted in the last 30 years could be decrypted and read. It's speculated that some people may collect encrypted information with the hope of decrypting it in the future using quantum algorithms. In light of this, work is being spent developing new cryptography algorithms, that rely on different principles and would make them resistant to future decryption using quantum computing.

Continue reading [Part Two](/posts/2024/06/ssl/tls-guide-part-two-certificates/) to learn about how X.509 certificates enable TLS communication and keep the modern internet secure.

If you found this helpful feel free to [buy me a coffee](https://buymeacoffee.com/alabbott), and if you have any feedback or corrections I would be happy to get in touch via [email](mailto:alan.l.abbott@gmail.com).