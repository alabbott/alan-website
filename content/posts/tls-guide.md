---
title: "TLS/SSL Certificates Guide"
date: 2024-06-24T15:32:03-05:00
draft: true
toc: false
images:
tags:
  - untagged
---

Transport Layer Security (TLS) or Secure Sockets Layer (SSL) protocols are the primary method for securing communications between servers and clients on the internet or other networks such as a plant floor or enterprise network. TLS is the successor to SSL, however the terms are used interchangeably, both referring to the newer TLS protocol. Without using TLS to encrypt the communication between servers and clients, their communications could be exposed to 3rd parties intercepting and possibly spoofing messages. With cyber-attacks on industrial manufacturers increasing, securing all production and development networks becomes increasingly important to our customers.

Managing TLS and certificates can be a huge pain as well, especially in demo environments where we want to show secure capabilities, but demos can go long stretches between use and get shared among TCs. We're probably all too familiar with the security warnings about certificate errors, either showing as insecure or worse, not loading at all.

In this guide, I will break down the pieces that come together to form the TLS protocol and go over some tips for working with certificates. Understanding the basics helps make sense of errors and makes the whole ordeal a little less daunting.

## A precursor: Identity, Privacy, Trust, and Security

If two people want to send messages to each other they can pass notes using a messenger. This would not be very private however, because the messenger could easily read a message before delivering it. It would also not be very secure because the messenger could alter the message before delivery. There is no way to ensure or trust that the message is really from the sender or that it hasn't been changed. The identity of the sender can't be verified by the recipient.

The two people could implement a code, where they shift A to B, C to D, and so on. However, this would require them to agree upon this code via an outside channel. It would not be possible to establish trusted communication without meeting in person, or using an existing outside channel.  This is sometimes referred to as a shared secret.

If two people need to exchange information securely without meeting previously, such as webservers and a new client, they can use public and private keys to accomplish this. The use of public and private keys has many advantages, such as private communication without a shared secret, as well as verifying identity and providing trust through things like signatures and certificate chains.

It helped me to understand the various pieces of TLS certificates when I broke out the ideas of privacy, identity, and trust.

 - Privacy - Communication between parties can't be read by outside listeners
 - Identity - How can you verify who sent you that message and that it wasn't changed before it arrived?
 - Trust - Can I verify that someone I trust has endorsed that you are who you say you are?

In my mind, these are the core concepts behind "secure" communication. When we say we have a secure connection to a server or device, we mean we can verify the identity of the sender, the integrity of the message, and not worry that 3rd parties can read what we're sending. Hopefully by the end of this guide you will feel comfortable with the way TLS certificates provide privacy, identity, and trust which will make using them, and fixing them, much easier.

## Public Keys and Private Keys

The most important concept in the TLS protocol is public keys and private keys, which make up public key infrastructure (PKI). In reality these keys are just very, very large numbers used in cryptography algorithms, stored in certain specific formats. These keys enable everything in TLS with two functions

### Two important functions:

 - Messages encrypted with the public key can only be decrypted with the private key
 - Messages encrypted with the private key can only be decrypted with the public key

If Person A wants to send a message to Person B, they would each generate a keypair (public and private key) and exchange public keys, keeping their private keys secret. It doesn't matter if the public keys are intercepted, as they are public information and only represent the identity of the private key holder.

#### The process for conversation would be as follows:

1. For Person A to send a message to Person B, they would use Person B's public key to encrypt the message
2. Person B would use their secret private key to decrypt the message, only readable to them

#### To respond:

1. When Person B sends a message to Person A, they would use Person A's public key to encrypt the message
2. Person A would use their secret private key to decrypt the message, only readable to them

This system is great for ensuring privacy, as only the intended recipient can decrypt and read the message. It is not, however, a great system for verifying identity and providing trust. We must talk about signing and verification and talk about the other side of public key infrastructure.

Remember, private keys can also encrypt messages that can only be decrypted by the public key, and the public key is widely available and tied to the key holder's identity. To verify their identity, the private key holder can use their secret key to encrypt a message, only decryptable by their public key. This process is known as signing the message.

When the recipient gets that message, they will use the sender's public key to confirm they encrypted it, in a process called verification. Because the public key is tied to the identity of the private key holder, the recipient can be certain the message is from the sender and hasn't been changed in transit.

#### The process for signing a message would be as follows:

1. Person A uses their private key to encrypt a message, and sends it to Person B
2. Person B uses Person A's public key to decrypt the message, confirming it is from Person A

#### The process for verifying a message:

1. Person B receives an encrypted message from Person A, who claims to have signed it using their private key
2. Person B successfully decrypts the message using Person A's public key, confirming it was sent by Person A

This system may seem to check all of our boxes, allowing for two-way, private communication where the identity and integrity of each message can be verified. However, we are required to trust that Person A and Person B are who they claim to be. How can we be sure it is indeed Person A and Person B who hold those private keys? If we had a signed message from someone we trusted, we could verify that Person A's public key (and private key) belong to them.

### Cryptography Nerd Moment (Feel free to skip)

The above system roughly represents PGP (Pretty Good Privacy) which has been around for over 30 years and is pretty much the standard for encrypting or verifying communications. Beyond just messaging, PGP can be used to sign and verify blocks of code or even entire pieces of software. A developer can sign a package, and provide the signature. Using their public key and cryptography software, you can verify that the program is not corrupted, either accidentally or maliciously.

A limit of PGP is that it relies on a "web of trust" where individuals will endorse each other's public keys, confirming they belong to the claimed owner. While this decentralized model may work well in some situations, a system was needed for a more streamlined method of trust management, leading to X.509 certificates, which are described below.

There are several algorithms that can be used to generate the key pairs, such as RSA or IDEA, but they all use incredibly large numbers and mathematical processes that would be practically impossible to undo using modern computers. A description of the math behind creating keys can be found here: RSA (cryptosystem) - Wikipedia

There is the possibility that quantum computers could very quickly break the encryption used in PGP and TLS communication. This would mean that virtually all information encrypted in the last 30 years could be decrypted and read. It's speculated that some people may collect encrypted information with the hope of decrypting it in the future using quantum algorithms. In light of this, work is being spent developing new cryptography algorithms, that rely on different principles and would make them resistant to future decryption using quantum computing.

## Certificates

This is where our public keys become certificates. Certificates are how clients verify servers are who they claim to be. Certificates provide this verification by including a signed message from a trusted company, known as a Certificate Authority (CA), who verifies the domain name and company information listed on the certificate is correct and true.

A certificate is obtained from a Certificate Authority by first creating a keypair, then sending the public key along with all the information associated with the owner of the public key. The combination of public key and identifying information is called a Certificate Signing Request (CSR) and is the file (.csr) sent to the CA to request a certificate. The signed certificate is then sent back, which includes the public key and identifying information of the requestor, but also the signature of the CA verifying the validity of the information.

Common Name and SAN Name

One of the fields for information is the Common Name (CN) which refers to the fully qualified domain name (FQDN) for the server. The FQDN simply refers to the complete URL without any protocol or port information. For example: [rockwellautomation.com](http://rockwellautomation.com/), [mail.google.com](http://mail.google.com/), or ftis-twx9. It is important to match the FQDN to the address you will use to reach the server, as the browser will use this to verify the certificate is valid.

If a server needs to be reached from multiple addresses a Subject Alternate Name (SAN) can be specified which provides alternate names from which the server can reached.

The other information fields should be completed if sending a CSR to a CA but otherwise don't matter.

Types of Certificates
There are three types of certificates to be aware of:

Root Certificate
This is used by the CA to sign either intermediate or end entity certificates.
Typically have very long expiration dates
These are what make up the Trusted Root Certificate Authorities

Intermediate Certificate
This is a certificate that has been signed by a root certificate, but may be used to sign more certificates
Can sign either end entity certificates or other intermediate certificates
Shorter expiration dates and rotated more often

End Entity Certificate
These are certificates assigned to individual servers
They can not be used to sign more certificates

It's good to know that individual certificates can also have restrictions placed on what they can be used for when they are created, which can sometimes cause headaches.

Certificate Chains

A certificate chain is a series of certificates that originate from a trusted root certificate and extend through any number of intermediate certificates, ultimately leading to the end entity certificate. The purpose of a certificate chain is to establish a chain of trust from the end entity certificate back to the root certificate, verifying the identity and authenticity of the server or client involved in the communication.

The process of validating a certificate chain involves verifying the digital signatures of each certificate in the chain, starting with the end entity certificate and moving up the chain towards the root certificate. Each certificate in the chain is signed by the issuing authority of the next certificate in the chain. The chain is considered valid if the root certificate is found in the client's trusted root certificate store.

In summary, a certificate chain consists of:

End Entity Certificate - assigned to the server or client that needs to be authenticated.
Intermediate Certificates (if any) - signed by the root or another intermediate certificate and used to sign end entity certificates.
Root Certificate - the top-level certificate, trusted by clients and used to sign intermediate certificates.

Certificate Bundles

A certificate bundle is a collection of multiple certificates combined into a single file. Bundles are used to simplify certificate management and distribution, particularly when dealing with certificate chains or multiple certificates for different domains or services. Bundles can be provided in various file formats, such as .pem, .pfx, or .crt, depending on the platform and application requirements.

Expiration Dates

Both CA signed and self-signed certificates have an expiration date, requiring companies to confirm they still control the domain name and private key associated with the certificate every so often. In production it is good practice to set expiration dates conservatively and rotate certificates often. In a demo environment, I usually set them for as long as I expect the demo to live. If a company loses control of a production private key, they should get new certificates immediately, as they could be impersonated by anyone with the private key.

Expiration dates are one major cause of certificate errors in demos. You can save hassle later on by creating demo certificates with long expiration dates. We will discuss replacing expired certificates later on in this guide. The other major cause of security warnings is using self-signed certificates. Or really, not configuring demos to trust our generated self-signed certificates.

Self-Signed Certificates

A self-signed certificate is represented by one of the above scenarios, where we must accept Person A's identity because they say so. A self-signed certificate provides a public key without a signed message verifying the identity of the key holder. Browsers by default show an error and don't visit sites with self-signed certificates, for good reason, I don't want anyone claiming to be my bank without signed verification.

To bypass this, we can add our self-signed certificate to the Trusted Root Certificate Authorities. Most operating systems have a default group of Trusted Root Certificate Authorities. Java has a separate group of default trusted CAs, known as cacerts. By adding our self-signed certificate to the Trusted Root CAs group, browsers will recognize our self-signed certificate and will not show errors, provided our certificate isn't expired and matches the FQDN used to reach the server.

Self-Signed Certificate Authority

If you need to generate certificates for several servers, you can create a self-signed Certificate Authority, which can then be used to sign multiple certificates for all of the servers, while only requiring the self-signed CA to be added to the Trusted Root Certificate Authorities. The FactoryTalk Analytics DataView 3.03 includes scripts for generating a self-signed CA and certificates for servers. The scripts require OpenSSL for Windows which can be downloaded below:

Win32/Win64 OpenSSL Installer for Windows - Shining Light Productions ([slproweb.com](http://slproweb.com/))

Recommend version Win64 OpenSSL v1.1.1t

Windows and Java File Types

Windows and Java platforms utilize different file formats to store and manage TLS certificates. Understanding these file types is essential for proper certificate handling, installation, and configuration in various applications and systems. The following are some common file formats used for TLS certificates on Windows and Java platforms:

1. Personal Information Exchange (PFX) or PKCS#12:

Windows systems often use the .pfx file format to store a certificate and its corresponding private key in a single, password-protected file. This format is useful for importing and exporting certificates with their associated private keys, simplifying certificate deployment and management. The PKCS#12 standard defines the .pfx file format, and these files are often used in Microsoft products, such as IIS and Exchange Server.

2. DER (Distinguished Encoding Rules):

Both Windows and Java platforms can use the .der file format, which is a binary encoding of a certificate. DER files typically contain only a single certificate without its associated private key. These files can be used for various purposes, such as installing root and intermediate certificates into the trusted certificate store.

3. PEM (Privacy-Enhanced Mail):

The .pem file format is a widely-used format for storing certificates and keys in plain-text, base64-encoded form. PEM files can include certificates, private keys, or both, and can be used across different platforms, including Windows and Java. They are often utilized in open-source applications, such as OpenSSL and Apache HTTP Server.

4. Java KeyStore (JKS):

Java applications use the Java KeyStore format to store certificates, private keys, and other cryptographic materials in a single, password-protected file. The .jks file format is specific to Java and is used by default in Java applications for managing keys and certificates. Java KeyStores can hold multiple entries, allowing for the storage of several certificates and keys in a single file.

5. Java TrustStore:

Similar to the Java KeyStore, the Java TrustStore is used to store trusted certificate authorities' certificates. It is often used to store root and intermediate certificates that establish a chain of trust for validating end entity certificates. TrustStores use the same .jks file format as Java KeyStores but serve a distinct purpose in the certificate management process.

A note on encoding

When working with certificates, you'll encounter two primary encoding methods: DER (Distinguished Encoding Rules) and PEM (Privacy-Enhanced Mail). Understanding these encoding methods is crucial for handling and managing certificates effectively.

DER (Distinguished Encoding Rules): DER is a binary encoding method used for certificates, keys, and other cryptographic data. It is a strict, compact, and unambiguous representation based on the ASN.1 (Abstract Syntax Notation One) standard. DER-encoded files are typically smaller than their PEM counterparts and are often used in situations where space and performance are critical. DER files usually have extensions like .der, .cer, or .crt.

PEM (Privacy-Enhanced Mail): PEM is a text-based encoding method that uses base64 encoding to represent binary data as ASCII text. PEM-encoded files include a header and footer to mark the beginning and end of the certificate or key data. This format is widely used across different platforms and applications due to its human-readable nature and compatibility with various tools. PEM files typically have extensions like .pem, .crt, .cer, or .key.

In summary, DER and PEM are the two primary encoding methods used for certificates and cryptographic data. DER is a compact binary format, while PEM is a more human-readable text-based format. Understanding these encoding methods will help you manage and work with certificates effectively across various platforms and applications.

Guide to File Types

.pem (Privacy-Enhanced Mail): A text-based, base64-encoded file used for storing certificates, private keys, or both; compatible with various platforms and applications.
.crt (Certificate): A file format used for storing certificates, which can be either DER-encoded (binary) or PEM-encoded (base64 text); primarily associated with public key certificates.
.cer (Certificate): Similar to .crt, a file format used for storing certificates, which can be either DER-encoded (binary) or PEM-encoded (base64 text).
.der (Distinguished Encoding Rules): A binary-encoded file format used for storing certificates and other cryptographic data; compact and unambiguous representation based on ASN.1 standard.
.key (Key): A file format used for storing private keys, typically in PEM-encoded (base64 text) format.
.pfx (Personal Information Exchange) or .p12 (PKCS#12): A binary file format used for storing a certificate and its associated private key in a single, password-protected file; commonly used on Windows systems for importing and exporting certificates and keys.
.jks (Java KeyStore): A binary file format used by Java applications for storing certificates, private keys, and other cryptographic materials in a single, password-protected file.
.csr (Certificate Signing Request): A file format used for sending a public key and identifying information to a Certificate Authority (CA) to request a signed certificate.
.crl (Certificate Revocation List): A file format used for listing revoked certificates, typically published and maintained by Certificate Authorities (CAs).

These file types are commonly used for managing and configuring certificates and cryptographic data across various systems, platforms, and applications.

Tools for working with certificates

OpenSSL

OpenSSL is a widely-used, open-source toolkit that implements the SSL (Secure Sockets Layer) and TLS (Transport Layer Security) protocols, as well as a robust cryptography library. It provides a versatile command-line interface for creating, managing, and converting certificates, keys, and other cryptographic data. OpenSSL supports a variety of operations, such as generating certificate signing requests (CSRs), signing certificates, creating and managing private keys, and validating certificate chains.

While OpenSSL can be really useful, using a command line tool can be intimidating when first starting out.

Java Keytool

Java Keytool is a command-line utility bundled with the Java Development Kit (JDK) and Java Runtime Environment (JRE) for managing key pairs, certificates, and keystores. It supports operations such as creating and importing certificates, generating key pairs, exporting certificates, and listing the contents of a keystore. Java Keytool is particularly useful for managing certificates in Java-based applications and environments.

CertMgr

CertMgr is a Microsoft Windows command-line utility for managing certificates in the Windows Certificate Store. It allows users to perform various tasks, such as adding, deleting, or viewing certificates, as well as managing certificate revocation lists (CRLs) and certificate trust lists (CTLs). CertMgr is a convenient tool for Windows administrators and developers who need to manage certificates on their local machines or in a Windows-based environment.

KeyStore Explorer

KeyStore Explorer is a free, open-source, graphical tool for managing Java keystores and PKCS #12 files. It provides a user-friendly interface for creating, importing, and exporting certificates, as well as generating key pairs and converting between different keystore formats. KeyStore Explorer supports various operations, including signing and verifying certificates, editing certificate properties, and examining certificate chains. This tool is particularly useful for users who prefer a graphical interface over command-line utilities for managing certificates and cryptographic data.

KeyStore Explorer is installed on the 4PAK and is the recommended tool for dealing with certificate issues on the 4PAK, due to the ease of use and graphical interface.

Common Issues and How to fix them

Warning for Self-Signed Certificates

A warning for self-signed certificates might appear because self-signed certificates lack the endorsement of a trusted Certificate Authority (CA). A CA acts as an independent, trusted third party that verifies the identity of a certificate owner. When a certificate is self-signed, the issuer and the subject of the certificate are the same entity, meaning the certificate owner is vouching for their own identity. This raises concerns about the authenticity and integrity of the certificate, as there is no external validation from a trusted CA.

Often, browsers will let you continue with a self-signed certificate, but will show the connection as Not Secure and have a red warning where the lock icon would be in the address bar. Some browsers and devices, such as iPads and iPhones, will not access sites with self-signed certificates at all without additional configuration.

To eliminate the warning for a self-signed certificate, you can add the certificate to the Trusted Root Certificates store on Windows. This action tells the system to trust the certificate, effectively treating it as if it were issued by a trusted CA. Here's how to add a self-signed certificate to the Trusted Root Certificates store:

    1. Locate the self-signed certificate file (usually in .cer or .crt format).

    2. Double-click the certificate file to open the Certificate dialog box.

    3. Click on the "Install Certificate" button to launch the Certificate Import Wizard.

    4. Select "Local Machine" as the store location and click "Next."

    5. Choose "Place all certificates in the following store" and click "Browse."

    6. In the "Select Certificate Store" dialog, select "Trusted Root Certification Authorities" and click "OK."

    7. Click "Next" and then "Finish" to complete the import process.

After completing these steps, the self-signed certificate will be added to the Trusted Root Certificates store on your Windows machine, and the warning should no longer appear when accessing the resource secured by the certificate. Keep in mind that this process should be done cautiously, as adding a self-signed certificate to the Trusted Root store may expose the system to security risks if the certificate owner is not trustworthy.

Expired Certificate

KeyStore Explorer is a graphical tool for managing Java keystores and cryptographic data. To create a new self-signed certificate using KeyStore Explorer, follow these steps:

Download and install KeyStore Explorer from the official website ([http://keystore-explorer.org/](http://keystore-explorer.org/)) if you haven't done so already.

Launch KeyStore Explorer.

In the main menu, click on "Create a new KeyStore." Select the type of KeyStore you want to create (e.g., JKS, PKCS #12, BKS, UBER, etc.). For most purposes, JKS (Java KeyStore) is a common choice.

Once the new KeyStore is created, right-click on the empty space in the main window and select "Generate Key Pair."

Choose the key pair algorithm (e.g., RSA, DSA, EC) and the key size (e.g., 2048 bits for RSA), then click "OK."

In the "Generate Key Pair - Enter Details" window, fill in the required information for the new self-signed certificate. This information typically includes fields such as Common Name (CN), Organization (O), Organizational Unit (OU), Locality (L), State (ST), and Country (C). Make sure to enter the correct information, as it will be used to identify the certificate owner.

Set the validity period for the certificate (usually specified in days). A longer validity period is generally more convenient but may pose security risks if the private key is compromised. Click "OK" to proceed.

Enter an alias for the key pair entry in the KeyStore. This is a unique identifier for the key pair and certificate within the KeyStore. Click "OK."

You'll be prompted to set a password for the key pair entry. Enter a strong password and click "OK."

The new self-signed certificate and its corresponding key pair are now created and added to the KeyStore. You can view the details of the certificate by double-clicking on the entry.

To save the KeyStore, go to the main menu and click "File" > "Save KeyStore." Choose the location to save the KeyStore file and set a password to protect its contents.

With these steps, you have successfully created a new self-signed certificate using KeyStore Explorer. Remember that self-signed certificates might cause trust warnings in browsers and other applications, as they lack a trusted Certificate Authority's endorsement.

CN Mismatch

A Common Name (CN) mismatch error occurs when the domain name in a website's SSL/TLS certificate does not match the domain name in the browser's address bar. To fix a CN name mismatch error, follow these steps:

Verify the domain name: Double-check the domain name in the browser's address bar and ensure it matches the domain name specified in the SSL/TLS certificate. If there's a typo or mistake in the address, correct it and reload the page.

Check for Subject Alternative Names (SANs): If you expect the certificate to work for multiple domain names, ensure that all necessary domain names are listed as SANs in the certificate. If they are missing, you'll need to reissue the certificate and include the missing domain names as SANs.

Reissue the certificate: If the domain name in the certificate is incorrect, you'll need to obtain a new SSL/TLS certificate for the correct domain name. Contact your Certificate Authority (CA) to reissue the certificate with the correct CN, or create a new Certificate Signing Request (CSR) with the correct CN and submit it to the CA.

Install the corrected certificate: Once you have the new certificate with the correct CN or updated SANs, install it on your web server according to the server's documentation. Make sure to replace the old certificate with the new one, and ensure the server is properly configured to use the updated certificate.

Test the connection: After installing the new certificate, clear your browser's cache, and restart the browser. Navigate to your website using the correct domain name, and check if the CN mismatch error is resolved.

By following these steps, you should be able to fix a CN name mismatch error and ensure secure communication between the website and its visitors.
