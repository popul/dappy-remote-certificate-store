# Why dappy dynamic certificates are a game changer ?

Today, managing certificates are such a pain. So many companies are exposed to security risks that comes from corrupted certificates and they didn’t know about this. When we know, it’s too late, damage is done. Expired certificates expose also our clients and partners to down time and reduce their trust. But why are we are in this situation and can we do something ?

## Definition

Certificates are the angular stone of TLS protocol and help 2 computers to communicate in a secure way. It’s a container that make a real and a non repudiable relationship between a name, an owner and a trust anchor. With a certificate, we make cryptographic proof that a unique public name is owned by the owner of a public key because we trust the entity that signed the certificate, the trust anchor.

In the public web, a name is a FQDN (https://en.wikipedia.org/wiki/Fully_qualified_domain_name), a unique public name, managed by DNS (https://en.wikipedia.org/wiki/Domain_Name_System), a public hierarchical distributed system managed by ICANN (https://en.wikipedia.org/wiki/ICANN), a private American corporation. 
The owner of certificate is anyone or any computer who knows the private key used to derive the certificate public key.
Trust anchors are certificates trusted by mainly all end user computers and called root certificates. A certificate is trusted if it has a relationship with one root certificate. Entities whose manage them are called certificate authorities and they are included in root certificates stores, like Mozilla (https://wiki.mozilla.org/CA), Microsoft (https://docs.microsoft.com/en-us/security/trusted-root/program-requirements), Google (https://www.chromium.org/Home/chromium-security/root-ca-policy/) and Apple (https://www.apple.com/certificateauthority/ca_program.html). All of these companies provide web browsers and must ensure a great level of trust to their end users.

## Problems

First of all, end user browsers trust servers because they trust related root certificates installed locally and not only because these servers have been signed by a certificate authority. There is a gap between local root certificates and root certificates listed in public root stores. The larger this gap, the higher the security risk. Navigating on the public web with an outdated local certificate store can be extremely insecure.

Also, certificates can’t be easily repudiated. If it has been signed by an another trusted certificate (by its corresponding private key), the cryptographic proof (signature) is true forever. CRL and OCSP protocols mitigate this issue but it depends entirely on softwares that interpret these OCSP and CRL metadata.. More on that, these software interpret OCSP and CRL in their way. They are just an overlay and do not invalidate CA signatures. OCSP and CRL protocols do not guarantee that you are browsing the web with non revoked certificates. Of course, set an expiration date minimize the attack surface, but it’s half a solution. How to install the new certificate before the expiration date in the local root store ? 

To be valid, a certificate must be signed by a certificate authority that your computer trust. In others worlds, your server identity can be impersonated or revoked by any certificate authority. Today, who can say that all certificate authorities are not influenced by countries like United States or China ? War in Ukraine show us (https://www.bleepingcomputer.com/news/security/russia-creates-its-own-tls-certificate-authority-to-bypass-sanctions/) that no one is the owner of its server identity. If just one certificate authority that you trust is compromised, your TLS communications are exposed to man in the middle attacks. It’s called TLS interception and this technique is used in company intranets to prevent data leaks. But few people realize that we are also exposed to this attack on the public web.

Public names are managed by DNS, which is the public name authority. So, we have a registry for public names, but it’s not the case for certificates. To be exact, we have as many certificate registries as root certificate authorities which are private by default. This lack of visibility about certificates is the first problem that a PKI must resolve, and It’s not easy one. Intuitively, if you are the owner of a domain name, only you can endorse this name, but it’s more complicated with current public root stores, as we discussed earlier. What happens when domain name has a new owner ? You guessed … certificates are still valid. And conversely, why a certificate become invalid despite the fact that name ownership didn’t changed.

Many attempts were made to patch DNS to store certificates like RFC 4398 (https://www.rfc-editor.org/rfc/rfc4398) or to save certificate fingerprints with DANE (https://datatracker.ietf.org/doc/html/rfc6698). But DNS failed to be secured using DNSSEC (https://datatracker.ietf.org/doc/html/rfc4033) in an end to end way.

## Solutions



## What next ?
