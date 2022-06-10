
This article is the first part of a two article series on the DNS, the public web PKI and dappy. It is the result of more than two years of research and exchange with engineers and researchers in cybersecurity and web technologies.

We will first describe the typology and properties of the web public key infrastructure, its relations with the Domain Name System and come up with a design proposition that aims at solving many issues.

# Use Dappy as remote trusted certificate store

Today, managing certificates is a pain for many companies. When exposing a B2B portal, online SaaS or APIs are exposed to many security and downtime risks coming from corrupted or expired certificates. When a company discovers a security breach such as a [Man-In-The-Middle attack](https://en.wikipedia.org/wiki/Man-in-the-middle_attack), it is often too late. All of these certificate issues reduce partner and client trust in you.

**Post incident analysis raises the following questions : Why are we are in this situation and can we do anything to remediate it ?**

## What are certificates and stores ?

Certificates are the angular stone of identity in the TLS protocol. Based on digital signatures, it allows authentication with the concept of [chain of trust](https://en.wikipedia.org/wiki/Chain_of_trust). There are two types of certificates. The first ones are **server certificate**, they are delivered by companies and institutional servers and contain the server's identity. Server certificates are signed by a second type of certificate, **trusted certificates** (intermediate and root certificates). Trusted certificates are installed on client devices, such as computers and mobile phones. A server certificate is trusted if signed by a trusted certificate known by the client (client may be different from physical device, it can be an operating system or a program like a web browser).

In a public web context, trusted certificates are distributed over Web PKI root stores, authored by browser companies like [Mozilla](https://wiki.mozilla.org/CA), [Microsoft](https://docs.microsoft.com/en-us/security/trusted-root/program-requirements), [Google](https://www.chromium.org/Home/chromium-security/root-ca-policy/), [Apple](https://www.apple.com/certificateauthority/ca_program.html). Root stores contain hundreds of certificates owned by public [certificate authorities](https://en.wikipedia.org/wiki/Certificate_authority). The trusted certificates have to be installed on their local stores in order to be trusted, this is usually done during software updates.

## What's wrong with certificates stores ?

Firstly, the local list of trusted certificate is the point of reference, each client has one, it does not matter that a web server has a certificate signed by a well known authority like "Let's Encrypt" if the clients don't have the corresponding trusted certificate installed. The trusted certificates a given client has (locally of course) may differ from trusted certificates listed in a Web PKI root stores. Quite obviously, the larger this gap, the higher the security risk. Browsing the public web with an outdated local certificate store can be extremely insecure.

Secondly, certificates can’t be easily repudiated. The cryptographic proof (signature) is valid forever because a certificate has been signed by a trusted certificate (to be exact, it has been signed by its corresponding private key). [OCSP](https://en.wikipedia.org/wiki/Online_Certificate_Status_Protocol) and [CRL](https://en.wikipedia.org/wiki/Certificate_revocation_list) protocols permit repudiation, the burden of checking the validity of certificates is on the softwares that interpret the metadata. These protocols are overlays, they can be implemented or interpreted differently by softwares, **ultimately, OCSP and CRL do not and cannot invalidate CA signatures**. Sadly, these protocols do not guarantee that clients or partners are browsing your website with only valid and non revoked certificates. Of course, setting an expiration date minimizes the attack surface, but that's only half the solution. Moreover, how would you install the new certificate before the expiry date in the local root store ? 

Ultimately, **your server identity can be impersonated or revoked by any certificate authority.** For example, on apple devices, [164 root certificates](https://support.apple.com/en-us/HT212140) are valid. Who can guarantee today that countries like United States, China or some private corporations are not conducting shady interceptions in relation with the trusted certificate stores ? War in Ukraine recently [illustrated](https://www.bleepingcomputer.com/news/security/russia-creates-its-own-tls-certificate-authority-to-bypass-sanctions/) that the chain of trust is easily corruptible. It takes only one trusted certificate authority to be compromised for your (or your client's) TLS communications to be exposed to Man-In-The-Middle attacks. MITM attacks are partly mitigated by [CT Logs](https://datatracker.ietf.org/doc/html/rfc6962), but still possible. TLS interception is not always bad, it is widely used in company intranets or private networks to prevent data leaks and monitor traffic. But few people realize that anyone is also exposed to these attacks on the public web. 

### Relation with DNS and the public names

[DNS](https://en.wikipedia.org/wiki/Domain_Name_System) is the authority for the management of public names. The typology of this network is pyramidal / top-down, it starts at the root and goes down the domains (root, top level, second level etc.). DNS is a unique tree, it is an ever changing but unique tree of public names. DNS stores many informations, the most important being the IP addresses of the servers. On the other hand **the certificate authorities system is a horizontal network with hundreds of authorities at the root, not at all a tree-like network**. The two typologies can only oddly coexist, for example you will very hardly know all the certificates that are linked to a public name at a given time, CAs are opaque.

One may say that a public PKI system must offer visibility, that is not the case today, the certificate authority system does not offer global visibility. No one should be able to impersonate a domain name, but as argued earlier the PKI and root stores of the public web make this hard to achieve. What happens when a domain name has a new owner ? You guessed it … certificates are still valid. And conversely, why does a certificate become invalid despite the fact that name ownership hasn’t changed.

Many attempts were made to patch DNS and allow DNS zones to store TLS certificates, [RFC 4398](https://www.rfc-editor.org/rfc/rfc4398), or to save certificate fingerprints with [DANE](https://datatracker.ietf.org/doc/html/rfc6698). But DNS failed to be secured using [DNSSEC](https://datatracker.ietf.org/doc/html/rfc4033) in an end to end way.

A public web identity is in reality borrowed from certificate authorities. If just one authority is compromised in any way, all secured public communications are compromised, a root authority can create a certificate for any domain **without the owner's permission**. We implicitly accept this **single point of failure** because it's the only effective way to secure communications and transactions on the internet. At the beginning we also explained that no unified policy exists to avoid the use of revoked certificates.

**The web deserves better**

One of the central ideas of our research and products at FABCO, and also a point on which we agree with some researchers is that too much trust is delegated to local stores and their trusted certificates. Security flaws, lack of visibility and control will be a gigantic issue as companies continue to digitize their operations, and deploy critical web applications on the public Internet. More and more value will be managed through APIs and web interfaces in the coming years, web cryptocurrency exchanges already handle daily volumes that are in the Billions, [home ownership can be digitalized using NFTs](https://www.forbes.com/sites/forbesbusinesscouncil/2022/02/16/nfts-and-the-future-of-commercial-real-estate/) and exchanged publicly. Not surprisingly, the number of cyber attacks has never been so high.

## No more local trusted store ! 

Secure communications implies that a client authenticates a server and they both exchange messages in a encrypted fashion. **The dappy name system** is a public name and PKI system that aims at solving the aforementioned issues related to server authentication like inconsistencies with DNS, use of invalid certificates, lack of transparency and TLS interception possibilities.

[**Dappy name system**](https://github.com/fabcotech/dappy-propositions/blob/master/01_co_resolution.MD) is a hierarchical name system like [DNS](https://en.wikipedia.org/wiki/Domain_Name_System), that :
- Allows companies and people to own [TLDs](https://en.wikipedia.org/wiki/Top-level_domain)
- Distribute trusted certificates or their fingerprints along side this TLD 
- Includes a network of independant companies that act as DNS resolvers, and allow clients to do co-resolution
- Is built in a 100% open source fashion
- Runs on the blockchain and is free from mismanagement or unilateral censorship

The dappy name system is a **remote trusted certificate store**, it plays the role of public identity registry and helps to authenticate TLS participants by distributing trusted certificates. Applications built on top of dappy, such as the [dappy browser](https://github.com/fabcotech/dappy), dappy agent (name and certificate client resolver), and librairies like the [dappy lookup](https://github.com/fabcotech/dappy-lookup) will **fetch trusted certificates dynamically**, removing all the problems with revocated trusted certificates. Data integrity is resolved using [co-resolution](https://github.com/fabcotech/dappy-propositions/blob/master/01_co_resolution.MD#4-co-resolution), a trustless mecanism, used to query dappy name system.

**Dappy authentication** protocol consists of resolving trusted certificates at runtime by walking through the dappy name system from leaf to root during name resolution (if not found on a.example.d, trusted from example.d is taken). Certificates are **always related to a domain name**. Companies and institutions don't have to trust and transfer their identity to a third party, like a public certificate authority.

But **Dappy name system** wants to do more for companies or teams that didn't have their own PKI or don't want to use an external one. To enable clients to trust their server certificate, the idea is to save certificates fingerprints in Dappy name system, in TLSA records like [DANE](https://datatracker.ietf.org/doc/html/rfc7671) does. In that way, clients can verify the authenticity and integrity of server certificates without PKI. We are convinced that software development teams will love how is that simple to enable TLS communcations with Dappy.

## What's next ?

Interested by our security solutions ? We provide free assistance for companies that wish to try dappy, you can reach out to us by email contact[at]fabco.tech or through the [dappy.tech/hello](https://dappy.tech/hello) form.
