# KTS10301 Two-Way SSL \( Spring Boot + Tomcat Embedded \)

## 1. Background

If you’re using relatively new versions of Spring Boot and Tomcat, configuring a HTTPS server with a client-certificate authentication \(mutual authentication\) is actually pretty straightforward. The information on how to achieve this, however, is not. So once I figured it out, I thought I’d write it down for future reference and for anyone else looking for a straightforward guide.

Software versions: Spring Boot 1.4, Tomcat 8.5, Firefox 49.

## 2. Preknowledge

This guide assumes that you have a basic understanding of / experience with SSL certificates and a working Spring Boot web application.

## 3. Part 1: Certificates

### 3.1. Create certificates

Grab your favorite certificate management tool \(I use[XCA](https://sourceforge.net/projects/xca/)\) and create three certificates and private keys \(XCA has convenient templates for all three, all I do is change the validity to 10 years and set commonName\):

1. Certificate Authority \(CA\) to sign all the others. Export certificate only.
2. Server, signed by the CA. Export the key pair \(certificate + private key\). Make sure the commonName matches the domain name you will be using
3. Client, signed by the CA. Export the key pair.

To avoid certificate warnings/errors, import the CA certificate as a trusted authority in the OS/browser you use.



