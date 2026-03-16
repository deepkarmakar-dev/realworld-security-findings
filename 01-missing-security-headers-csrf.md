Missing Security Headers & Unprotected Search Form
Passive Security Finding — Educational Institution Websites (India)

Field| Details
Author| Deep Karmakar
Target| Two Educational Institution Websites (India)
Assessment Type| Passive Security Review
Method| Browser source inspection + securityheaders.com
Testing Type| Non-intrusive — no exploitation performed
Date| March 2026
Status| Pending Responsible Disclosure

---

Finding 1 — Missing HTTP Security Headers

During passive review, both websites were scanned using securityheaders.com and received a Grade F, indicating that several recommended HTTP security headers are missing.

Missing Headers

Header| Risk
X-Frame-Options| Clickjacking — site can be embedded in attacker iframe
Content-Security-Policy| No browser-level XSS mitigation
Strict-Transport-Security| HTTPS downgrade / MITM risk
X-Content-Type-Options| MIME sniffing attacks
Referrer-Policy| Sensitive URL data leakage
Permissions-Policy| Potential misuse of browser features (camera, mic, etc.)

Impact

The missing X-Frame-Options header may allow attackers to embed the website in an iframe and perform clickjacking attacks, potentially tricking users into interacting with malicious overlays.

Recommended Fix

Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-Content-Type-Options "nosniff"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
Header always set Content-Security-Policy "default-src 'self'; object-src 'none';"
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"

---

Finding 2 — Search Form Using GET Without CSRF Protection

The website search form uses the GET method and does not contain a CSRF token.

Example

<form method="GET" action="en.php">
    <input name="linktitle" placeholder="Search...">
    <input type="submit" value="Search">
</form>Risk

- Search queries may appear in browser history, logs, and referrer headers
- Requests could potentially be triggered from external websites
- Parameters reflected in URLs may increase attack surface

Recommended Fix

<form method="POST" action="/search">
    @csrf
    <input name="query" placeholder="Search...">
    <button type="submit">Search</button>
</form>---

Tools Used

Tool| Purpose
Browser Developer Tools| HTML source review
securityheaders.com| HTTP header analysis

---

Note

This assessment was conducted using passive analysis only.
No exploitation attempts were made and no restricted data was accessed.

Deep Karmakar — AppSec Learning Journey 🔐
