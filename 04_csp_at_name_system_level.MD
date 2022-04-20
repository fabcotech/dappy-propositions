## Proposition 4, Content-Security-Policy headers at the name system level

[W3C's Content Security Policy Level 2](https://www.w3.org/TR/CSP2/)
[W3C's Content Security Policy Level 3](https://www.w3.org/TR/CSP3/)

### Abstract

Content-Security-Policy is a powerful feature, it *defines a mechanism by which web developers can control the resources which a particular page can fetch or execute, as well as a number of security-relevant policy decisions.* (from W3C's CSP3 document).

For example under top-level navigation on *example.com* a developer may want to only allow images to be loaded from *https://img.example.com*, and javascript scripts to be loaded from *https://cdn.js.com* and *https://example.com*. CSP headers allow you to express just that in a whitelist fashion, thus blocking all image and javascript loads from other hosts (or protocol).

### Proposition

**We propose to add the possibility to store Content-Security-Policy header's content at the name system level**. Proposition 1 includes a new DNS record type called `CERT`, the current proposition would naturally include another new DNS record type called `CSP` that would follow the exact same format as the Content-Security-Policy proposed by W3C.

If a `CSP` record is found, the browsers injects it as if it was coming from the web server, if a Content-Security-Policy is already set in the web request, it is overwritten, the name system level has priority. 

### Expected outcome

Under our set of proposals, the name system lives on the blockchain, which is a public resilient database secured by cryptographic access. Storing key public security data at the name system level makes lot of sense. A corrupted web server, or malicious web developer who does not have access to the zone on the blockchain cannot in any way perform spoofing or front-end based data exfiltration.

Content-Security-Policy as a control and security feature for web developers can be moved one level upper in the name system.

From a user or developer experience and audit perspecive this feature of centralizing security data at the name system level allows easier and superior monitoring capabilities for the entire name system zone security properties, and to easieventual weak points.

**recursive CSP record resolution**

Same questions as in proposition 1 for `CERT` record recursive resolution.
