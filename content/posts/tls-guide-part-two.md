---
title: "SSL/TLS Guide Part Two: Certificates"
date: 2024-06-10T14:26:30-05:00
draft: false
toc: false
images:

---

In [Part One](/posts/2024/06/ssl/tls-guide-part-one-public/private-keys/) we discussed public key infrastructure (PKI) and how it enables privacy and integrity of communications between two parties. However, we still have the issue of trust, and deciding whether to trust the holder of a given private key or not. In Part Two of my TLS guide we will see how X.509 certificates enable us to rely on central sources of trust to weed out bad actors.

### Contents
1. [Part One: Public/Private Keys](/posts/2024/06/ssl/tls-guide-part-one-public/private-keys/)
2. [**Part Two: Certificates**](/posts/2024/06/ssl/tls-guide-part-two-certificates/)
3. [Part Three: Tools for Working with Certificates](/posts/2024/06/ssl/tls-guide-part-three-tools-for-working-with-certificates/)

## Certificates

This is where our public keys become certificates. Certificates are how clients verify servers are who they claim to be. Certificates provide this verification by including a signed message from a trusted company, known as a **Certificate Authority (CA)**, who verifies the domain name and company information listed on the certificate is correct and true.

A certificate is obtained from a Certificate Authority by first creating a keypair, then sending the public key along with all the information associated with the owner of the public key. The combination of public key and identifying information is called a **Certificate Signing Request (CSR)** and is the file (.csr) sent to the CA to request a certificate. The signed certificate is then sent back, which includes the public key and identifying information of the requestor, but also the signature of the CA verifying the validity of the information.

### Common Name and SAN Name

One of the fields for information is the **Common Name (CN)** which refers to the **fully qualified domain name (FQDN)** for the server. The FQDN simply refers to the complete URL without any protocol or port information. For example: [*alanabbott.me*](https://alanabbott.me/), *mail.google.com*, or *pmox-node01*. It is important to match the FQDN to the address you will use to reach the server, as the browser will use this to verify the certificate is valid.

If a server needs to be reached from multiple addresses a **Subject Alternate Name (SAN)** can be specified which provides alternate names from which the server can reached.

The other information fields should be completed if sending a CSR to a CA but otherwise don't matter.

### Types of Certificates
There are three types of certificates to be aware of:

#### Root Certificate
This is used by the CA to sign either intermediate or end entity certificates.
Typically have very long expiration dates.
These are what make up the Trusted Root Certificate Authorities.

#### Intermediate Certificate
This is a certificate that has been signed by a root certificate, but may be used to sign more certificates.
Can sign either end entity certificates or other intermediate certificates.
Shorter expiration dates and rotated more often.

#### End Entity Certificate
These are certificates assigned to individual servers.
They can not be used to sign more certificates.

It's good to know that individual certificates can also have restrictions placed on what they can be used for when they are created, which can sometimes cause headaches.

### Certificate Chains

A certificate chain is a series of certificates that originate from a trusted root certificate and extend through any number of intermediate certificates, ultimately leading to the end entity certificate. The purpose of a certificate chain is to establish a chain of trust from the end entity certificate back to the root certificate, verifying the identity and authenticity of the server or client involved in the communication.

The process of validating a certificate chain involves verifying the digital signatures of each certificate in the chain, starting with the end entity certificate and moving up the chain towards the root certificate. Each certificate in the chain is signed by the issuing authority of the next certificate in the chain. The chain is considered valid if the root certificate is found in the client's trusted root certificate store.

In summary, a certificate chain consists of:

 - End Entity Certificate - assigned to the server or client that needs to be authenticated.
 - Intermediate Certificates (if any) - signed by the root or another intermediate certificate and used to sign end entity certificates.
 - Root Certificate - the top-level certificate, trusted by clients and used to sign intermediate certificates.

### Certificate Bundles

A certificate bundle is a collection of multiple certificates combined into a single file. Bundles are used to simplify certificate management and distribution, particularly when dealing with certificate chains or multiple certificates for different domains or services. Bundles can be provided in various file formats, such as .pem, .pfx, or .crt, depending on the platform and application requirements.

### Expiration Dates

Both CA signed and self-signed certificates have an expiration date, requiring companies to confirm they still control the domain name and private key associated with the certificate every so often. In production it is good practice to set expiration dates conservatively and rotate certificates often. In a demo environment, I usually set them for as long as I expect the demo to live. If a company loses control of a production private key, they should get new certificates immediately, as they could be impersonated by anyone with the private key.

### Self-Signed Certificates

A self-signed certificate is represented by one of the above scenarios, where we must accept Person A's identity because they say so. A self-signed certificate provides a public key without a signed message verifying the identity of the key holder. Browsers by default show an error and don't visit sites with self-signed certificates, for good reason, I don't want anyone claiming to be my bank without signed verification.

To bypass this, we can add our self-signed certificate to the Trusted Root Certificate Authorities. Most operating systems have a default group of Trusted Root Certificate Authorities. Java has a separate group of default trusted CAs, known as cacerts. By adding our self-signed certificate to the Trusted Root CAs group, browsers will recognize our self-signed certificate and will not show errors, provided our certificate isn't expired and matches the FQDN used to reach the server.

### Self-Signed Certificate Authority

If you need to generate certificates for several servers, you can create a self-signed Certificate Authority, which can then be used to sign multiple certificates for all of the servers, while only requiring the self-signed CA to be added to the Trusted Root Certificate Authorities.

### Getting CA Signed Certificates for Free

Services such as [Let's Encrypt](https://letsencrypt.org/), a free certificate authority created and run by the [Internet Security Research Group (ISRG)](https://www.abetterinternet.org/), have made getting certificates significantly easier. Their work as made implementing TLS much simpler and helped proliferate secure HTTPS as the default in most places. I plan to write more about using Let's Encrypt in the future, and integrating with tools like Traefik, but for now check out their [Getting Started](https://letsencrypt.org/getting-started/) page.

Continue reading [Part Three](/posts/2024/06/ssl/tls-guide-part-three-tools-for-working-with-certificates/) to learn about some common tools used for working with certificates, or go back to [Part One](/posts/2024/06/ssl/tls-guide-part-one-public/private-keys/) to learn about public / private keys as the foundation for X.509 certificates.

If you found this helpful feel free to [buy me a coffee](https://buymeacoffee.com/alabbott), and if you have any feedback or corrections I would be happy to get in touch via [email](mailto:alan.l.abbott@gmail.com).
