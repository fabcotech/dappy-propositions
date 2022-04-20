## Blockchain plus co-resolution

### Abstract

DNS has made the internet better. By allowing resources to have names that humans can remember and communicate easily.

This document describes a design for a public name system (other than DNS) based on an combination of blockchain technology, HTTPS end-to-end encryption and co-resolution by independent resolvers.

Here we will refer to "resolution" as the operation that is performed to resolve IP addresses and recover TLS certificates. We will describe what co-resolution is, how blockchain fits into the design, and how it differentiates from DNS lookup and TLS certificates validation by browsers and other programs.

Dappy is one application that does co-resolution, this document describes the overal design and philosophy, any program could follow this same principles and achieve same levels of trustlessness and security.

### 1. Context, existing problems and limitations

The Domain Name System is a protocol that names resources on the internet network.

It can be useful to see what a DNS zone looks like, an example of a DNS zone with IP addresses for a domain *example.com*, a subdomain *hello.example.com* and also a TXT record for *example.com*:

```
Origin: "example.com"
Records:
  type: "A",   host: "@",     data: "12.13.14.15"
  type: "A",   host: "hello", data: "15.16.17.18"
  type: "TXT", host: "@",     data: "arbitrarydata"
```

The Certificate Authorities validate certificates that are linked to the domain names of the DNS, and allow client-to-server of even server-to-server HTTPS (or HTTP+TLS) connections on the public web.

Those two networks or services (DNS and CAs) are built as a chain of corporations (or private services), each one doing a specific job. Numerous single point of failures exist, and greatly limit the accuracy and security levels that can be achieved when distributing a web application.

- A hacked or malicious DNS resolver leads to every client that use it potentially receiving fake/malicious IP addresses.
- A hacked or malicious registrar company leasds to an entire set of domain names being potentially corrupted all at once.
- A hacked or malicious registry leads to even more harmful consequences because registries tend to manage more domain names than registrars. As an example Palisade team [discovered a SQL injection vulnerability](https://palisade.consulting/blog/tld-hacking) in the TLD registrar Tonga Network  (*.to* domain names). For years it has been possible to change the IP resolution of every domain names under *.to*. 
- A hacked or malicious certificate authority (CA) also can lead to issuance of fake TLS certificates, and harmful man-in-the-middle attacks.

A registrar or registry hack opens the possibility to issue a valid TLS certificate, linked to the new malicious IP address.

The co-resolution and blockchain-based system that is described here solves those problems all at once. Removes the possibility of hacks on the network, and allows users and companies to move to higher levels web applications.

### 2. The blockchain

The co-resolution system could easily be built without a blockchain or distributed ledger technology of any kind. In such a configuration the co-resolvers can use the registries of the DNS, or another name system in which they will have to agree on who owns which domain name, what the corresponding IP addresses are.

The configuration that this document focuses on includes blockchain as a decentralized booking and renewal solution for a public name system. It may as well be labelled as *co-resolution + blockchain*.

Blockchain has the negative effect of forbidding human mediation, because of its decentralized nature. You cannot recover lost or stolen bitcoins or ETH. A co-resolution + blockchain name system removes any kind of litigation just like bitcoin does not have litigation processes.

This negative effect, is also very positive depending on the light with which you look at it, it allows unprecedented accuracy and trustlessness levels. The DNS+CA is mounted on top of private databases and programs with resitricted access. In the present proposition we drop every private program solution and a blockchain becomes the unique source of truth for domain names as well as TLS certificates.

A given name (ex: *example*) if available can be purchased with cryptocurrency. It must also be renewed periodically (see **Economic incentive** section to understand why this is important). Price, renewal fees, possibility to resell a domain names are implementation details and out of scope here.

The ownership is recorded in an decentralized public ledger, and secured by asymmetric cryptography. 

The Zone format may or may not follow [this draft published by AFNIC](https://tools.ietf.org/id/draft-bortzmeyer-dns-json-01.html). The dappy system has chosen to comply with this format proposition.

### 3. TLS certificates recorded alongside the name

TLS certificates are recorded alongside the name. `A`, `AAAA`, `TXT` records are valid record types in the DNS. A new type `CERT` has been appended for this purpose. Since certificates include carriage returns, CERT records are stored in hexadecimal format. TLS Certificates also often exceed the 255 characters limit, since our propositions (proposition 1 and 4) includes record types that may exceed this limit, in the dappy implementations we have decided to add a route `/dns-query-extended` that supports the new record types, `/dns-query` only support the legacy DNS types. 

A given first level name in a co-resolution + blockchain name system can be considered as a TLD (to avoid collisions they may be displayed with a suffix though, ex: *.dappy* or *.anothernamesystem*).

Certificates at the first (TLD) level can therefore be self signed, and used as a root for a PKI, certificates authorities are not needed because there is no need for some private organizations to attest that a given TLS certificate is valid. Blockchain tells the truth (see **multisig ownership**).

**Note on TLD and domain levels**

In the dappy implementations, a suffix may be displayed or made mandatory in the browser, like *.dappy*, *.dy* or *.d*, nevertheless it is a visual suffix that is here to avoid collisions with ICANN's domain names and other name system projects that sell TLD and display them as such in their applications. This suffix does not correspond to an authority that would be above the registrants of the name system. This is why we refer to *first level* or Top Level Domains (TLD) (ex: *example* in *example.d* is a TLD). 

**recursive CERT record resolution**

*Ongoing specification and request for opinions from reviewers*

It is possible to make a certificate valid for multiple hosts, typically some web servers will use the same for *example.com* and *www.example.com*. When navigation on *bar.foo.example.com*, should the web browser retrieve certificates in a rotation fashion ?
- (1) first for *bar.foo.example.com*
- (2) then if not found from *foo.example.com*
- (3) then if not found from *example.com*

This feature would allow to have fewer certificates than supported hosts, ease management and renewal of certificates very much.

### 4. Co-resolution

Co-resolution is the operation performed by a client that uses the name system for communicating with a named resource on the internet network, this client can be a browser or any other arbitrary program. Co-resolution can consist in resolving IP addresses, TXT records or TLS certificate.

The API may or may not follow [DNS Queries over HTTPS](https://www.rfc-editor.org/rfc/rfc8484.html) proposed by IETF. The DNS messages format may or may not follow [RFC 8427](https://www.rfc-editor.org/rfc/rfc8427.html). The dappy system has chosen to comply with both specifications from IETF.

In a co-resolution+blockchain system many independant corporations act as a trustless API layer for reading (and in some case writing) from or to the blockchain. They are called resolvers, co-resolvers or validators. Each resolver runs a DNS-over-HTTPS server, and has a special adapter that reads in the blockchain and sends back data in the appropriate format. They do not deal at all with registries of the DNS.

**Instead of doing unique trustful DNS lookup requests. Clients always query multiple resolvers at the same time. And perform consensus on the set of anwsers received.**

Depending on the accuracy level that a client requires, and/or the resolvers that are available, a client may query 3 resolvers, 5, 10, 60% or even 100% of the resolvers. A client may also require 100% of identical answers, or could tolerate only 90% or 66.7%. Those are implementation details and are out of scope here.

Example 1:

A network of 10 resolvers runs a co-resolution + blockchain name system. A given web browser wants to resolve *hey.dappy*, the settings will be 6/100%. Which means that it will query resolvers until it gets 6 answers (ignoring resolvers that do not respond for any reason), those 6 answers must be 100% identical.

A A A A A A -> resolution successful (A)
A A A A A B -> resolution not successful
A A A B B C -> resolution not successful
A A A x x x -> resolution not successful

Example 2:

Some settings that can lower the time of resolution, and also allow greater resiliency would be 4/75%. The co-resolution stops when 4 answers are received, only 3 out of 4 must be the same.

A A A A -> resolution successful (A)
A A B A -> resolution successful (A)
A A B B -> resolution not successful
A A A x -> resolution not successful


Co-resolution allows for higher resiliency of the name system because some resolvers can go down from time to time, it has no letal effect on the co-resolution performed by clients. Whereas in the DNS, if Google's 8.8.8.8 goes down, entire web services go down with it in a typical single point of failure situation.

Co-resolution allows for higher accuracy and security, it is virtually impossible to hack co-resolution because of end-to-end encryption right from the browser or the program doing the resolution.

There is no reason to think that a resolver cannot be hacked from time to time (developers, sysadmins, employees make mistakes), in this case end-to-end encryption becomes useless because one end is corrupted. There are many reasons to think that a hack in a 40 companies network, each with its own implementation of the software and/or security infrastucture cannot occur.

### 5. Conclusion

Reliance on a decentralized blockchain, end-to-end encryption of name system communications and co-resolution right from the browser (or another programs) is the most resilient and secure way of resolving IP addresses and TLS certificates.

- It allows for less tension on resolvers because responsibility is not concentrated on a single one. They can undergo downtime and even intrusion/hacks from time to time.
- For any company, organization, or individual that distributes its public web application, SaaS portal, or even a simple API or TLS endpoint can only have more trust and confidence in this system. They can deploy higher value web applications and endpoints, companies achieve lower costs (because less knowledge and tooling is needed) and superior security.

### Appendix 1. Economic incentive system

The DNS has another big issue, which is the separation between DNS resolvers and the DNS market. DNS resolvers do not structurally benefit from the sell and renewal of domain names, registrars are the company that do. Therefore only few corporations can afford to maintain public DNS resolvers.

A co-resolution + blockchain name system may include a princing for domain name, as well as a periodic renewal system. Every resolver can then directly benefit from the sell and renewal of domain names (for example 5 resolvers each get 20%). Moreover this healthy economic incentive model is easy to implement because blockchains and smart contrat platforms often provide payment systems.

### Appendix 2. Multi ownership

It has been argued that blockchain has the negative effect of reemoving human mediation, making it impossible for a stolen domain name to be given back to the legitimate owner. This is particularily true for big corporations and governements, it would be akward that *fr* and *amazon* belong to anyone else than the French governement and Amazon corporation in the USA.

Multi ownership exist for this purpose, removing the private key loss ot steal problem. In a multi ownership (or multisig) configuration you can make a domain name (also cryptocurrency) the ownership of 100 persons. And then 66.7% of those persons would have to agree on a change of ownership, or change of data associated to the domain, for this change to actually occur. Of course it can also be a 3 persons, or 10 persons ownership, members of the multisig could also be organizations (like registrars).

In such a configuration no system administrators, CEO or CTO can mess up something and loose ownership of a domain name, it is again superior to what DNS and registrar-managed ownerships propose.

### Appendix 3. Designated authorities

Another simple way to resolve the loss of private keys or stealing of domain names is by proposing or enforcing a designated authority system. In this system clients could be free to designate authorities that will be queried before the network of co-resolvers is queried. In a corporate environment for example, employees would designate *airbos* or *kaspersku* as an authority. And those services would maintain a list of blocked websites, and provide alternative IP addresses and TLS certificates. 