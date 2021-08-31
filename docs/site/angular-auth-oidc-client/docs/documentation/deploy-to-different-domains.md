---
sidebar_label: Deploying to different domains
sidebar_position: 5
---

# Deploying to different domains

If deploying the client application and the Security Token Service application with 2 different domains,
the X-Frame-Options HTTPS header needs to allow all iframes. Then use the CSP HTTPS header to only allow the required domains.
**The silent renew requires this.**

Add this header to responses from the server that serves your SPA:

```
Content-Security-Policy: script-src 'self' 'unsafe-inline';style-src 'self' 'unsafe-inline';img-src 'self' data:;font-src 'self';frame-ancestors 'self' https://localhost:44318;block-all-mixed-content
```

where `https://localhost:44318` is the address of your secure token secure server, i.e. the authority which issues tokens.

E.g. if you use nginx to serve your Angular application, you can configure the server as follows:

```
http {
  server {
    ...
    add_header Content-Security-Policy "script-src 'self' 'unsafe-inline';style-src 'self' 'unsafe-inline';img-src 'self' data:;font-src 'self';frame-ancestors 'self' https://localhost:44318;block-all-mixed-content";
```
