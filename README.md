# Why dappy dynamic certificates are a game changer ?

Today, managing certificates is a pain for many companies. When exposing a B2B portal, online SaaS or APi, they are exposed to many security and downtime risks coming from corrupted certificates or simply downtimes. When a company discovers that a security breach has been exploited through Man-In-The-Middle TLS interception, it is often too late. Expired certificates also expose the clients and partners to downtimes and reduce their trust.

**Why are we are in this situation and can we do something ?**

## Definitions and context

Certificates are the angular stone of TLS protocol and allow two computers to communicate in a confidential and secure fashion. A certificate is a container that make a real, cryptographic and a non repudiable relationship between a name (ex: example.com), an owner and a trust anchor. A certificate is a cryptographic proof that the owner of a key pair controls the server linked to a unique public name (ex: example.com), it is a proof in the sense that a trusted - the trust anchor - entity signed the certificate.

In the public web, a name is a Fully Qualified Domain Name (FQDN) (https://en.wikipedia.org/wiki/Fully_qualified_domain_name), a unique public name, managed by DNS (https://en.wikipedia.org/wiki/Domain_Name_System), a public hierarchical distributed system managed by ICANN (https://en.wikipedia.org/wiki/ICANN), a private American corporation. 

The owner of certificate is anyone or any computer who knows the private key used to derive the certificate public key.

Trust anchors are materialized by certificates trusted by mainly all end user computers, we call those certificates root certificates. A certificate is trusted if it has relationship with a root certificate. Entities who manage them are called certificate authorities and they are included in root certificates stores, like Mozilla (https://wiki.mozilla.org/CA), Microsoft (https://docs.microsoft.com/en-us/security/trusted-root/program-requirements), Google (https://www.chromium.org/Home/chromium-security/root-ca-policy/) and Apple (https://www.apple.com/certificateauthority/ca_program.html). All of these companies provide web browsers and must ensure a great level of trust to their end users.

## Problems

First of all, end user browsers trust servers because they trust related root certificates installed locally and not only because these servers have been signed by a certificate authority. Local root certificates may differ from root certificates listed in public root stores. The larger this gap is, the higher are the security risk. Navigating on the public web with an outdated local certificate store can be extremely insecure.

Also, certificates can’t be easily repudiated. If it has been signed by an another trusted certificate (by its corresponding private key), the cryptographic proof (signature) is valid forever. CRL and OCSP protocols allow to do repudiation, but the burden to check the validity of certificates is on the softwares that interpret these OCSP and CRL metadata. OCSP and CRL are overlays, they may be interpreted differently by softwares, they do not and cannot invalidate CA signatures. OCSP and CRL protocols do not guarantee that your clients and partners are browsing your website with non revoked certificates. Of course, setting an expiration date minimize the attack surface, but it’s half a solution. How to install the new certificate before the expiration date in the local root store ? 

In order to be valid, a certificate must be signed by a certificate authority that your computer trusts. It means that **your server identity can be impersonated or revoked by any certificate authority.** For example, on apple devices, [164 root certificates](https://support.apple.com/en-us/HT212140) are valid. . Who can guarantee today that all certificate authorities are not influenced by countries like United States or China ? War in Ukraine illustrates us (https://www.bleepingcomputer.com/news/security/russia-creates-its-own-tls-certificate-authority-to-bypass-sanctions/) that no one is the owner of its server identity. It just takes one trusted certificate authority to be compromised for  your (or your client's) TLS communications to be exposed to man in the middle attacks. This attack is called TLS interception and is used in company intranets to prevent data leaks. But few people realize that we are also exposed to this attack on the public web.

Public names are managed by DNS, which is the public name authority. So, we have a registry for public names, but it’s not the case for certificates. To be exact, we have as many certificate registries as root certificate authorities which are private by default. This lack of visibility about certificates is the first problem that a PKI must resolve, and It’s not an easy one. Intuitively, if you are the owner of a domain name, only you can endorse this name, but as argued earlier it’s more complicated with the PKI and root stores of the public web. What happens when domain name has a new owner ? You guessed … certificates are still valid. And conversely, why a certificate become invalid despite the fact that name ownership didn’t changed.

Many attempts were made to patch DNS and allow DNS zones to store TLScertificates, RFC 4398 (https://www.rfc-editor.org/rfc/rfc4398) or to save certificate fingerprints with DANE (https://datatracker.ietf.org/doc/html/rfc6698). But DNS failed to be secured using DNSSEC (https://datatracker.ietf.org/doc/html/rfc4033) in an end to end way.

## Solutions

As we discussed, many problems are related to local root certificate stores. At Fabco, we are convinced that too much trust is delegated to local stores and their root certificates. Revocated certificates are still used by end users and no system give this garantee today. Also, in practice, anyone that want to secure their public communications, **have** to provide a certificate signed by one these root certificates. They are owned by certificate authorities, which are all exposed to security issues. If just one authority is compromised, all public secured communications are compromised because a root authority can create a certificate for any domain **without the owner permission**. Today, we implicitly accept this situation because it's the only way to secure communications and transactions on Internet.

At the beginnning, people and companies just want to communicate in private on Internet. Maybe, for communications without values, the web public key infrastructure is an acceptable tradeoff. But is it an acceptable solution within a world more and more digitalized ? Today, house ownership can be digitalized using NFTs and exchanged publicly and the number of cyber attacks has never been so high.

In theory, to secure a TLS communication, you just have to emit a server certificate, distribute it to your client and finally they have to trust it. Trusting third parties using signed certificates issued by root authorities is totally useless if you already trust your server certificate. How big problem persists, how to distribute your server certificate in a trusted way ?

This is what Dappy trying to solve. At Fabco, we are trying to give the most secure and efficient way to distribute your  certificates, but not only. The web public key infrastructure is based on trust we put into certificates that brings all security flaws we discussed. What if instead of trusting local certificates, we trust a decentralized remote data source where all certificates are stored and fetchable in a safe way ?

## What next ?
