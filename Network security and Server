# HTTP vs HTTPS: Security Basics for Developers

## 1. The short version

**HTTP** (Hypertext Transfer Protocol) carries requests and responses between a client (such as a browser or mobile app) and a server. By itself, HTTP does not protect data while it travels across a network.

**HTTPS** is HTTP sent through **TLS** (Transport Layer Security). TLS provides encryption in transit, checks that the server has a valid certificate for the requested domain, and detects changes to data while it is being transmitted.

Use HTTPS for every production website and API, including pages that do not handle passwords or payments. HTTPS protects the whole connection, not just form submissions.


## 2. The basic pieces of a website

These terms describe different parts of how a website works:

- **HTTP/HTTPS** is the communication rule used to request and deliver web content.
- **A server** is a computer or software program that receives requests and sends responses. It may run your application, store files, or connect to a database.
- **Cloud hosting** means using computing resources from a provider over the internet instead of managing all the physical machines yourself. The provider may run virtual servers, databases, storage, or managed app services for you.
- **A CDN** (Content Delivery Network) is a group of servers in many locations. It can deliver copies of static files (such as images, stylesheets, and scripts) from a location closer to the visitor. Many CDNs can also cache pages, filter traffic, and help absorb attacks.

A cloud service can run your application server, and a CDN can sit in front of that server. HTTP or HTTPS is used on the connections between these parts.

### What happens when someone opens a website?

A simplified request looks like this:

```text
Browser → CDN (optional) → Web server / application → Response back to browser
```

1. The browser looks up the website's domain name to find where to connect.
2. It connects to the website using HTTP or HTTPS. With HTTPS, it first establishes a TLS-protected connection and checks the certificate.
3. If configured, a CDN receives the request. It may return a cached copy; otherwise it forwards the request to the origin server.
4. The server or application handles the request, perhaps reading from a database, and sends a response back through the CDN (if present) to the browser.

The **origin** is the main server or service where the site's content comes from. A CDN is often a layer in front of the origin, not a replacement for the application itself.

### Where HTTPS protection applies

HTTPS protects one connection at a time, from one TLS endpoint to another. If a CDN terminates HTTPS, the browser's encrypted connection ends at the CDN. The CDN can then read and process the request. The CDN should use HTTPS for its connection to the origin as well, especially when traffic crosses a network or trust boundary.

For example:

```text
Browser == HTTPS ==> CDN == HTTPS ==> Origin server
```

If the second connection uses plain HTTP, traffic between the CDN and origin is not encrypted on that leg. Configure HTTPS at every relevant hop, and restrict the origin so users cannot bypass the CDN's protections unless that is intended.

### A few useful distinctions

- **Server is not the same as cloud:** a server is a machine or service; cloud is one way to rent and manage computing resources, including servers.
- **CDN is not the same as hosting:** a CDN can cache and deliver content, but dynamic requests may still need the origin application.
- **HTTPS is not the same as a CDN:** a small site can use HTTPS without a CDN, and a CDN should still use HTTPS.
- **Cloud does not automatically mean secure:** you still need correct access controls, updates, backups, network rules, and application security.
## 3. What can go wrong with plain HTTP?

A network operator, attacker on an untrusted Wi-Fi network, compromised router, or other on-path observer may be able to:

- **Read traffic:** see pages, API data, cookies, credentials, and other unencrypted content.
- **Change traffic:** modify responses, inject scripts or ads, or alter downloads.
- **Impersonate a service:** redirect a user to a look-alike site or a malicious endpoint.

HTTPS makes these attacks substantially harder by protecting the connection between the client and the TLS endpoint. It does not hide everything: IP addresses, connection timing, and often the destination domain may still be observable, and data is visible at the client and server endpoints.

## 4. What TLS does

A TLS connection provides three main protections:

1. **Confidentiality:** encryption makes captured traffic unreadable without the session keys.
2. **Integrity:** authentication checks detect data altered in transit.
3. **Server authentication:** the browser validates the server's certificate chain and that the certificate matches the hostname.

During the TLS handshake, the client and server negotiate supported cryptographic settings, authenticate the server, and establish keys for the connection. Modern TLS commonly uses ephemeral key exchange, which helps protect past sessions if a server's long-term private key is later exposed (forward secrecy).

TLS does **not** prove that a site is honest, safe, or free of vulnerabilities. A phishing site can obtain a valid certificate for its own misleading domain. HTTPS also does not protect a compromised device, server, browser extension, or application.

## 5. Certificates and trust

A TLS certificate binds a public key to a hostname. Browsers check that it is within its validity period, matches the hostname, and chains to a trusted certificate authority (CA), among other checks.

Developer essentials:

- Serve a certificate valid for every hostname users access (including the correct `www` or API host).
- Install the full certificate chain and renew certificates before they expire.
- Protect the private key; restrict access, avoid committing it to source control, and rotate it if exposed.
- Automate issuance and renewal where possible, and monitor failures.
- Never train users to bypass certificate warnings. Fix the certificate or hostname problem instead.

A certificate encrypts and authenticates the TLS connection; it is not a security audit or a guarantee about the business behind the site.

## 6. HTTP vs HTTPS at a glance

| Property | HTTP | HTTPS |
|---|---|---|
| Default URL scheme | `http://` | `https://` |
| Transport protection | None built in | HTTP protected by TLS |
| Content confidentiality in transit | No | Yes, between TLS endpoints |
| Tampering detection in transit | No | Yes |
| Server certificate validation | No | Yes |
| Recommended for production | No | Yes, everywhere |

HTTPS uses TLS; it is not a different application protocol. HTTP/2 and HTTP/3 are commonly used over HTTPS, and HTTPS can also carry HTTP/1.1.

## 7. Practical deployment checklist

### Make HTTPS the default

- Configure the web server, load balancer, or hosting platform to serve HTTPS.
- Redirect all HTTP requests to HTTPS with a permanent redirect (commonly `301` or `308`). Do not send sensitive content before redirecting.
- Ensure the redirect preserves the intended hostname, path, and query safely; avoid open-redirect behavior.
- Update canonical URLs, links, API endpoints, callbacks, and documentation to use `https://`.
- Use HTTPS between internal services too when traffic crosses networks or trust boundaries. A private network is not automatically safe.

### Add HSTS carefully

The `Strict-Transport-Security` (HSTS) response header tells browsers to use HTTPS for future visits:

```http
Strict-Transport-Security: max-age=31536000
```

Only send HSTS over HTTPS. Start with a modest `max-age`, confirm all subdomains support HTTPS before adding `includeSubDomains`, and consider preload only when you understand its long-term implications. HSTS can make a site difficult to access if HTTPS is misconfigured.

### Use secure cookies

For session and authentication cookies, set appropriate attributes, commonly:

```http
Set-Cookie: session=...; Secure; HttpOnly; SameSite=Lax; Path=/
```

- `Secure`: send the cookie only over HTTPS.
- `HttpOnly`: prevent JavaScript from reading it; this helps limit cookie theft through some cross-site scripting (XSS) attacks.
- `SameSite`: reduce some cross-site request forgery (CSRF) risks. Choose `Strict`, `Lax`, or `None` based on the application flow. `SameSite=None` requires `Secure` in modern browsers.

Cookie flags are not substitutes for CSRF defenses, XSS prevention, or session-management controls.

### Prevent mixed content

A page loaded over HTTPS should not load scripts, stylesheets, images, API calls, or other resources over HTTP. Browsers may block or upgrade some insecure requests, but do not rely on that behavior. Update all resource URLs and inspect browser developer tools for mixed-content warnings.

### Protect application data

- Do not put passwords, access tokens, session IDs, or sensitive personal data in URLs. URLs can appear in browser history, logs, analytics, and referrer data.
- Avoid logging secrets and redact sensitive request and response fields.
- Validate authorization and input on the server. TLS does not prevent SQL injection, XSS, CSRF, broken access control, or insecure business logic.
- Keep TLS termination and proxy configuration correct. Trust forwarded headers (such as `X-Forwarded-Proto`) only from known proxies; otherwise attackers may spoof them.

## 8. TLS configuration basics

- Use currently supported TLS versions and secure defaults provided by your platform; disable obsolete protocols and weak cipher suites.
- Prefer TLS 1.2 or TLS 1.3 where supported by your environment. Avoid SSL and obsolete TLS versions.
- Let a well-maintained platform or library handle cipher selection and key exchange rather than inventing custom cryptography.
- Keep TLS libraries, operating systems, web servers, and dependencies patched.
- Do not disable certificate validation in clients. In APIs and service-to-service clients, validate both the certificate chain and hostname.
- Use automated configuration checks and monitor certificate expiry, handshake errors, and unexpected HTTP traffic.

Exact settings vary by platform and compatibility needs; use current guidance from your hosting provider and TLS library.

## 9. Common misconceptions

- **“HTTPS means the website is safe.”** It means the connection to that hostname is protected and the certificate validated. The site can still be malicious or vulnerable.
- **“Only login pages need HTTPS.”** Any page can expose private browsing activity or be modified in transit. Use HTTPS throughout.
- **“Our traffic is internal, so HTTP is fine.”** Internal networks can be misconfigured, shared, or compromised. Protect traffic across trust boundaries.
- **“A padlock means the company is legitimate.”** It does not certify the site's intentions or quality.
- **“HTTPS prevents XSS or SQL injection.”** It does not. Those require application-level defenses.
- **“Encryption hides all metadata.”** It protects content in transit, but some connection metadata remains visible.

## 10. Quick review checklist

Before release, confirm:

- [ ] Every production page and API is available over HTTPS.
- [ ] HTTP redirects to HTTPS without exposing sensitive content first.
- [ ] Certificates match hostnames, include the needed chain, and renew automatically.
- [ ] TLS versions and cryptographic settings use secure platform defaults.
- [ ] No mixed content or hard-coded HTTP endpoints remain.
- [ ] Session cookies use `Secure`, `HttpOnly`, and an appropriate `SameSite` value.
- [ ] HSTS is configured only after HTTPS works across the intended hostnames.
- [ ] Clients validate certificates and hostnames.
- [ ] Secrets are kept out of URLs and logs.
- [ ] Application security controls are in place in addition to TLS.

## 11. Remember

**HTTPS protects data in transit between TLS endpoints.** Developers still need secure application code, careful handling of secrets, safe session management, and properly configured infrastructure.


