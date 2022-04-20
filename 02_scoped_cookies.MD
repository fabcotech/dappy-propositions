## Proposition 2, scoped cookies

RFC 6265
RFC 6265 bis


### Abstract

This document describes a simple improvement that could make at least 90% of cross-site and cookie-based attacks impossible to perform. It also probably breaks retro-compatibility with some web services, that rely on cross-site cookies. This feature is independent from dappy, or any co-resolution or blockchain based name system, it can be implemented in any DNS browser (again it will probably break many things).

The security guarantees that are acheived with scoped cookies speciffication can be acheived without it. nevertheless scoped cookies make things simpler and requirying less knowledge.

### Context

From [RFC 6265 bis section 1.2](https://datatracker.ietf.org/doc/html/draft-ietf-httpbis-cookie-same-site-00#section-1.2)

```Subsequent requests from that user agent can be expected to contain the following header field if and only if both the requested resource and the resource in the top-level browsing context match the cookie.```

### 1. Scoped cookies

Scoped cookies is an isolation and security feature for cookies (State Management in web browsing context). The top-level's (address bar) host defines a scope (or isolated location) under which cookies are stored. Cookies, from any host reached by second-level requests (xhr, images, libraries) will be sent only if the top-level host is an exact match (eq if the browser's active address bar is on the exact same host).

For example, a user browses *example.com* (top-level), during this navigation some cookies are set, attached to *example.com* and also *auth.bank.com* (a XHR POST request was sent to *auth.bank.com*).

Later the client browses *evil.com*, the javascript triggers requests to *auth.bank.com* and *evil.com* hosts. No cookies previously set on *example.com* is sent, because the new top-level host is *evil.com*. All cookies under *example.com* are isolated from other hosts.

**Expected outcome and pros arguments**

A developer working on *example.com* never has to worry about anything that occurs in other hosts, cookies set on top-level navigation under *example.com* will never be sent from a top-level navigation under another host. He does not need to protect his server with CORS. The cookies set under *example.com* top-level are scoped to *example.com*.

**SameSite**

Some reflexions around *SameSite* cookie attribute and how it articulates with the scoped cookies behavior.

*SameSite* is a security feature that allows a server (ex: *example.com*) to tell the browser to send cookies only and only if the top-level address host matches with the cookies's host. It prevents *evil.com* from sending a XHR request to *example.com* and trigger some restricted actions on behalf of the user (CSRF attack).

Scoped cookies also protect against typical cookie-based CSRF attack scenario, if top-level address host is *evil.com*, a XHR request going to *example.com* will not include the cookies. The additional protection of *SameSite* attribute in this scenario is useless.

*SameSite* in addition to the scoped cookies behavior is nevertheless very useful if *auth.bank.com* want to make sure that cookies are sent only if the browsing context is *auth.bank.com* and not any other host.
