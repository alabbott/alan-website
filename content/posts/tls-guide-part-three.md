---
title: "SSL/TLS Guide Part Three: Tools for Working with Certificates"
date: 2024-06-17T15:47:45-05:00
draft: false
toc: false
images:

---

In parts one and two we covered the concepts of public key infrastructure (PKI) and X.509 certificates, certificate authorities, certificate chains, and self-signed certificates. In part three, we will go over some more practical information for working with certificates, such as common file types, encodings, and tools for working with certificates on various platforms. 

### Contents
1. [Part One: Public/Private Keys](/posts/2024/06/ssl/tls-guide-part-one-public/private-keys/)
2. [Part Two: Certificates](/posts/2024/06/ssl/tls-guide-part-two-certificates/)
3. [**Part Three: Tools for Working with Certificates**](/posts/2024/06/ssl/tls-guide-part-three-tools-for-working-with-certificates/)


## Windows and Java File Types

Windows and Java platforms utilize different file formats to store and manage TLS certificates. Understanding these file types is essential for proper certificate handling, installation, and configuration in various applications and systems. The following are some common file formats used for TLS certificates on Windows and Java platforms:

### 1. Personal Information Exchange (PFX) or PKCS#12:

Windows systems often use the .pfx file format to store a certificate and its corresponding private key in a single, password-protected file. This format is useful for importing and exporting certificates with their associated private keys, simplifying certificate deployment and management. The PKCS#12 standard defines the .pfx file format, and these files are often used in Microsoft products, such as IIS and Exchange Server.

### 2. DER (Distinguished Encoding Rules):

Both Windows and Java platforms can use the .der file format, which is a binary encoding of a certificate. DER files typically contain only a single certificate without its associated private key. These files can be used for various purposes, such as installing root and intermediate certificates into the trusted certificate store.

### 3. PEM (Privacy-Enhanced Mail):

The .pem file format is a widely-used format for storing certificates and keys in plain-text, base64-encoded form. PEM files can include certificates, private keys, or both, and can be used across different platforms, including Windows and Java. They are often utilized in open-source applications, such as OpenSSL and Apache HTTP Server.

### 4. Java KeyStore (JKS):

Java applications use the Java KeyStore format to store certificates, private keys, and other cryptographic materials in a single, password-protected file. The .jks file format is specific to Java and is used by default in Java applications for managing keys and certificates. Java KeyStores can hold multiple entries, allowing for the storage of several certificates and keys in a single file.

### 5. Java TrustStore:

Similar to the Java KeyStore, the Java TrustStore is used to store trusted certificate authorities' certificates. It is often used to store root and intermediate certificates that establish a chain of trust for validating end entity certificates. TrustStores use the same .jks file format as Java KeyStores but serve a distinct purpose in the certificate management process.

## A note on encoding

When working with certificates, you'll encounter two primary encoding methods: DER (Distinguished Encoding Rules) and PEM (Privacy-Enhanced Mail). Understanding these encoding methods is crucial for handling and managing certificates effectively.

**DER (Distinguished Encoding Rules):** DER is a binary encoding method used for certificates, keys, and other cryptographic data. It is a strict, compact, and unambiguous representation based on the ASN.1 (Abstract Syntax Notation One) standard. DER-encoded files are typically smaller than their PEM counterparts and are often used in situations where space and performance are critical. DER files usually have extensions like .der, .cer, or .crt.

**PEM (Privacy-Enhanced Mail):** PEM is a text-based encoding method that uses base64 encoding to represent binary data as ASCII text. PEM-encoded files include a header and footer to mark the beginning and end of the certificate or key data. This format is widely used across different platforms and applications due to its human-readable nature and compatibility with various tools. PEM files typically have extensions like .pem, .crt, .cer, or .key.

In summary, DER and PEM are the two primary encoding methods used for certificates and cryptographic data. DER is a compact binary format, while PEM is a more human-readable text-based format. Understanding these encoding methods will help you manage and work with certificates effectively across various platforms and applications.

## Guide to File Types

 - **.pem (Privacy-Enhanced Mail):** A text-based, base64-encoded file used for storing certificates, private keys, or both; compatible with various platforms and applications.

 - **.crt (Certificate):** A file format used for storing certificates, which can be either DER-encoded (binary) or PEM-encoded (base64 text); primarily associated with public key certificates.

 - **.cer (Certificate):** Similar to .crt, a file format used for storing certificates, which can be either DER-encoded (binary) or PEM-encoded (base64 text).

 - **.der (Distinguished Encoding Rules):** A binary-encoded file format used for storing certificates and other cryptographic data; compact and unambiguous representation based on ASN.1 standard.

 - **.key (Key):** A file format used for storing private keys, typically in PEM-encoded (base64 text) format.

 - **.pfx (Personal Information Exchange) or .p12 (PKCS#12):** A binary file format used for storing a certificate and its associated private key in a single, password-protected file; commonly used on Windows systems for importing and exporting  - certificates and keys.

 - **.jks (Java KeyStore):** A binary file format used by Java applications for storing certificates, private keys, and other cryptographic materials in a single, password-protected file.

 - **.csr (Certificate Signing Request):** A file format used for sending a public key and identifying information to a Certificate Authority (CA) to request a signed certificate.

 - **.crl (Certificate Revocation List):** A file format used for listing revoked certificates, typically published and maintained by Certificate Authorities (CAs).


These file types are commonly used for managing and configuring certificates and cryptographic data across various systems, platforms, and applications.

## Tools for working with certificates

### OpenSSL

OpenSSL is a widely-used, open-source toolkit that implements the SSL (Secure Sockets Layer) and TLS (Transport Layer Security) protocols, as well as a robust cryptography library. It provides a versatile command-line interface for creating, managing, and converting certificates, keys, and other cryptographic data. OpenSSL supports a variety of operations, such as generating certificate signing requests (CSRs), signing certificates, creating and managing private keys, and validating certificate chains.

While OpenSSL can be really useful, using a command line tool can be intimidating when first starting out.

The OpenSSL source code can be downloaded here: [OpenSSL Source Downloads](https://www.openssl.org/source/)

Probably more practical for most folks will be the Windows installer made available by Shining Light Productions here: [Windows OpenSSL Installer](https://slproweb.com/products/Win32OpenSSL.html)

### Java Keytool

Java Keytool is a command-line utility bundled with the Java Development Kit (JDK) and Java Runtime Environment (JRE) for managing key pairs, certificates, and keystores. It supports operations such as creating and importing certificates, generating key pairs, exporting certificates, and listing the contents of a keystore. Java Keytool is particularly useful for managing certificates in Java-based applications and environments.

### CertMgr

CertMgr is a Microsoft Windows command-line utility for managing certificates in the Windows Certificate Store. It allows users to perform various tasks, such as adding, deleting, or viewing certificates, as well as managing certificate revocation lists (CRLs) and certificate trust lists (CTLs). CertMgr is a convenient tool for Windows administrators and developers who need to manage certificates on their local machines or in a Windows-based environment.

### KeyStore Explorer

KeyStore Explorer is a free, open-source, graphical tool for managing Java keystores and PKCS #12 files. It provides a user-friendly interface for creating, importing, and exporting certificates, as well as generating key pairs and converting between different keystore formats. KeyStore Explorer supports various operations, including signing and verifying certificates, editing certificate properties, and examining certificate chains. This tool is particularly useful for users who prefer a graphical interface over command-line utilities for managing certificates and cryptographic data.

KeyStore Explorer can be downloaded here: [KeyStore Explorer](https://keystore-explorer.org/downloads.html)

## Common Issues and How to fix them

### Warning for Self-Signed Certificates

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

I only recommend this solution for demonstration systems or development environments. For any production use case you should get a real certificate issued by a trusted CA. Nowadays, this can be done easily, often automatically, and free with tools from [Let's Encrypt](https://letsencrypt.org/). In future articles, I will go over setting up a reverse proxy like Traefik to handle all of this automatically for you, eliminating a lot of the hassle of dealing with certificates.

### Expired Certificate

1. KeyStore Explorer is a graphical tool for managing Java keystores and cryptographic data. To create a new self-signed certificate using KeyStore Explorer, follow these steps:

2. Download and install KeyStore Explorer from the official website ([http://keystore-explorer.org/](http://keystore-explorer.org/)) if you haven't done so already.

3. Launch KeyStore Explorer.

4. In the main menu, click on "Create a new KeyStore." Select the type of KeyStore you want to create (e.g., JKS, PKCS #12, BKS, UBER, etc.). For most purposes, JKS (Java KeyStore) is a common choice.

5. Once the new KeyStore is created, right-click on the empty space in the main window and select "Generate Key Pair."

6. Choose the key pair algorithm (e.g., RSA, DSA, EC) and the key size (e.g., 2048 bits for RSA), then click "OK."

7. In the "Generate Key Pair - Enter Details" window, fill in the required information for the new self-signed certificate. This information typically includes fields such as Common Name (CN), Organization (O), Organizational Unit (OU), Locality (L), State (ST), and Country (C). Make sure to enter the correct information, as it will be used to identify the certificate owner.

8. Set the validity period for the certificate (usually specified in days). A longer validity period is generally more convenient but may pose security risks if the private key is compromised. Click "OK" to proceed.

9. Enter an alias for the key pair entry in the KeyStore. This is a unique identifier for the key pair and certificate within the KeyStore. Click "OK."

10. You'll be prompted to set a password for the key pair entry. Enter a strong password and click "OK."

11. The new self-signed certificate and its corresponding key pair are now created and added to the KeyStore. You can view the details of the certificate by double-clicking on the entry.

12. To save the KeyStore, go to the main menu and click "File" > "Save KeyStore." Choose the location to save the KeyStore file and set a password to protect its contents.

With these steps, you have successfully created a new self-signed certificate using KeyStore Explorer. Remember that self-signed certificates might cause trust warnings in browsers and other applications, as they lack a trusted Certificate Authority's endorsement.

Again, this solution should only be done for demonstration systems or development environments. For any production use case you should get a real certificate issued by a trusted CA. Nowadays, this can be done easily, often automatically, and free with tools from [Let's Encrypt](https://letsencrypt.org/).

### CN Mismatch

A Common Name (CN) mismatch error occurs when the domain name in a website's SSL/TLS certificate does not match the domain name in the browser's address bar. To fix a CN name mismatch error, follow these steps:

Verify the domain name: Double-check the domain name in the browser's address bar and ensure it matches the domain name specified in the SSL/TLS certificate. If there's a typo or mistake in the address, correct it and reload the page.

Check for Subject Alternative Names (SANs): If you expect the certificate to work for multiple domain names, ensure that all necessary domain names are listed as SANs in the certificate. If they are missing, you'll need to reissue the certificate and include the missing domain names as SANs.

Reissue the certificate: If the domain name in the certificate is incorrect, you'll need to obtain a new SSL/TLS certificate for the correct domain name. Contact your Certificate Authority (CA) to reissue the certificate with the correct CN, or create a new Certificate Signing Request (CSR) with the correct CN and submit it to the CA.

Install the corrected certificate: Once you have the new certificate with the correct CN or updated SANs, install it on your web server according to the server's documentation. Make sure to replace the old certificate with the new one, and ensure the server is properly configured to use the updated certificate.

Test the connection: After installing the new certificate, clear your browser's cache, and restart the browser. Navigate to your website using the correct domain name, and check if the CN mismatch error is resolved.

By following these steps, you should be able to fix a CN name mismatch error and ensure secure communication between the website and its visitors.

This concludes the SSL/TLS guide, I hope it will be a good resource for anyone looking to better understand this fundamental part of security that is found nearly everywhere nowadays. I know writing this guide really helped solidify the concpets and has helped me in professioanl and personal projects alike. 

If you found this helpful feel free to [buy me a coffee](https://buymeacoffee.com/alabbott), and if you have any feedback or corrections I would be happy to get in touch via [email](mailto:alan.l.abbott@gmail.com).

Thanks for reading!