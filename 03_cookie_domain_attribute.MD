## Proposition 3, removal of the domain attribute

Reference: [RFC 6265](https://datatracker.ietf.org/doc/html/rfc6265)

### Abstract

Today it is impossible to securely lend a subdomain. Online governance models (like DAOs) and blockchain-based organizations already exist and will gain popularity, this will become a problem. 

*Domain* attribute forbids hierarchical structures from being expressed in the address bar through domains and subdomain ownerships. The simple reason as explained in later paragraph is that *child.example.com* can set and override cookies on *example.com*.

### Explanation

From [RFC 6265 secion 4.1.2.3](https://datatracker.ietf.org/doc/html/rfc6265#section-4.1.2.3)

```if the value of the Domain attribute [on a cookie] is "example.com", the user agent will include the cookie in the Cookie header when making HTTP requests to example.com, www.example.com, and www.corp.example.com.
[...]
The user agent will reject cookies unless the Domain attribute specifies a scope for the cookie that would include the origin server.
```

The behavior is tricky to understand, 99% of web developers probably don't know that subdomains can set cookies to their parents domain (except for TLDs like *.com*).

### Proposition

**We propose to ignore/remove the *Domain* attribute in the *Set-Cookie* header**, and to enforce the exact match of origins when *Cookie* header is built in subsequent top-level of second-level requests. A given origin server cannot set cookies on another host (a host includes all levels of domains), similarly, cookies that have been set on host A will never be included into a request going to host B.

Example:

Under navigation on *example.com* if second-level requests are sent to *bar.foo.example.com* and *bank.com*, *Domain* attribute in the *Set-Cookie* header will be ignored, and enforced respectively to *bar.foo.example.com* and *bank.com*.

### Expected outcome

Decentralized Autonomous Organizations, as well as legacy corporations will be able to lend subdomains, mapping them to different departements, branch of activities, independant third party contractors, with no risk of sharing critical data that is stored in client's browser through cookies.

Illustration:

*example.com*
*us.example.com*
*ny.us.example.com*
*la.us.example.com*
*independantproject.us.example.com*

Each of these website manage its cookies in an independant fashion, none is automatically shared.