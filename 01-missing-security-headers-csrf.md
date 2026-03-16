# Missing Security Headers, Unprotected Search Form & Outdated jQuery
### Passive Security Finding — Educational Institution Websites (India)

---

| Field | Details |
|---|---|
| **Author** | Deep Karmakar |
| **Target** | Two Educational Institution Websites (India) |
| **Assessment Type** | Passive Security Review |
| **Method** | Browser source inspection + public scanning tools |
| **Testing Type** | Non-intrusive — no exploitation performed |
| **Date** | March 2026 |
| **Status** | Pending Responsible Disclosure |

---

## Finding 1 — Missing HTTP Security Headers

During passive review, both websites were scanned using [securityheaders.com](https://securityheaders.com) and received a **Grade F** — indicating all six recommended security headers are absent.

### Missing Headers

| Header | Risk |
|---|---|
| `X-Frame-Options` | Clickjacking — site can be embedded in attacker iframe |
| `Content-Security-Policy` | No browser-level XSS mitigation |
| `Strict-Transport-Security` | HTTPS downgrade / MITM possible |
| `X-Content-Type-Options` | MIME sniffing attacks |
| `Referrer-Policy` | Sensitive URL data leaks to third parties |
| `Permissions-Policy` | Unauthorized camera / mic / location access |

### Impact

The missing `X-Frame-Options` header is a standalone exploitable misconfiguration — no other vulnerability required. An attacker can embed the site in a hidden iframe and overlay fake UI elements to trick students into submitting credentials or fee payments to a malicious page.

### Recommended Fix

```apache
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-Content-Type-Options "nosniff"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
Header always set Content-Security-Policy "default-src 'self'; object-src 'none';"
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"
```

---

## Finding 2 — Search Form Using GET Without CSRF Protection

The website search form uses the `GET` method with no CSRF token present.

```html
<form method="GET" action="en.php">
    <input name="linktitle" placeholder="Search...">
    <input type="submit" value="Search">
</form>
```

### Risk

- Search queries exposed in browser history, server logs, and referrer headers
- No token validation — requests can be triggered from external pages
- Parameter directly reflected in URL, increasing attack surface

### Recommended Fix

```html
<form method="POST" action="/search">
    @csrf
    <input name="query" placeholder="Search...">
    <button type="submit">Search</button>
</form>
```

---

## Finding 3 — Outdated jQuery (v1.11.0) with Known CVEs

Confirmed via [Wappalyzer](https://wappalyzer.com) — a passive technology fingerprinting tool.

```
Detected: jQuery 1.11.0
Latest stable: jQuery 3.7.1
```

### Applicable CVEs

| CVE | CVSS | Type | Description |
|---|---|---|---|
| CVE-2020-11023 | 6.9 | XSS | Passing HTML to DOM manipulation methods can execute untrusted code |
| CVE-2020-11022 | 6.9 | XSS | XSS via HTML string parsing in `.html()` and related methods |
| CVE-2019-11358 | 6.1 | Prototype Pollution | `jQuery.extend()` allows `__proto__` pollution |
| CVE-2015-9251 | 6.1 | XSS | Cross-domain AJAX requests with `text/javascript` auto-execute |

### Impact

jQuery 1.11.0 is affected by 4 publicly documented CVEs. While exploitation depends on how jQuery is used in the application code, the library itself is a known vulnerable dependency — any code using `.html()`, `$.extend()`, or cross-domain AJAX with user-controlled input is at direct risk.

### Recommended Fix

```html
<!-- Replace outdated version -->
<script src="https://code.jquery.com/jquery-3.7.1.min.js"
        integrity="sha384-1H217gwSVyLSIfaLxHbE7dRb3v4mYCKbpQvzx0cegeju1MVsGrX5xXxAvs/HgeFs"
        crossorigin="anonymous"></script>
```

---

## Tools Used

| Tool | Purpose |
|---|---|
| Browser Developer Tools | HTML source review |
| [securityheaders.com](https://securityheaders.com) | HTTP header analysis |
| [Wappalyzer](https://wappalyzer.com) | Technology fingerprinting |

---

## Summary

| # | Finding | Severity | Exploitable Without Auth |
|---|---|---|---|
| 1 | Missing HTTP Security Headers | High | Yes (Clickjacking) |
| 2 | No CSRF Token on Search Form | Medium | Yes |
| 3 | Outdated jQuery 1.11.0 (4 CVEs) | Medium | Conditional |

---

## Note

This was a passive security observation only.  
No exploitation was attempted and no data was accessed.  
Findings will be reported to the respective institutions via responsible disclosure.

---

*Deep Karmakar — AppSec Learning Journey 🔐*

---

Finding 3 — Outdated jQuery Version (v1.11.0)

Description

Technology fingerprinting using the browser extension Wappalyzer revealed that the application is running jQuery version 1.11.0, which is outdated and affected by several publicly disclosed vulnerabilities.

The latest stable version at the time of testing is jQuery 3.7.1.

Known Vulnerabilities

CVE| CVSS| Type| Description
CVE-2020-11023| 6.9| XSS| HTML passed to DOM manipulation functions may execute malicious code
CVE-2020-11022| 6.9| XSS| XSS vulnerability in HTML parsing within ".html()" and similar methods
CVE-2019-11358| 6.1| Prototype Pollution| jQuery.extend() may allow prototype pollution
CVE-2015-9251| 6.1| XSS| Cross-domain AJAX requests may automatically execute JavaScript responses

Impact

Using outdated third-party libraries increases the attack surface of the application. If the application processes user-controlled data through vulnerable jQuery methods such as ".html()", ".append()", or "$.extend()", it may be susceptible to Cross-Site Scripting or prototype pollution attacks.

Recommended Fix

Upgrade jQuery to the latest stable version:

<script src="https://code.jquery.com/jquery-3.7.1.min.js"
integrity="sha384-1H217gwSVyLSIfaLxHbE7dRb3v4mYCKbpQvzx0cegeju1MVsGrX5xXxAvs/HgeFs"
crossorigin="anonymous"></script>

Regularly update third-party libraries to ensure known vulnerabilities are patched.

---

Tools Used

Tool| Purpose
Browser Developer Tools| HTML source code inspection
securityheaders.com| HTTP header analysis
Wappalyzer| Technology fingerprinting

---

Summary

#| Finding| Severity| Exploitable Without Authentication
1| Missing HTTP Security Headers| High| Yes
2| Search Form Using GET Method| Medium| Yes
3| Outdated jQuery 1.11.0| Medium| Conditional

---

Note

This assessment was conducted using passive testing techniques only.
No exploitation attempts were made and no sensitive data was accessed.

The findings will be responsibly disclosed to the respective institutions.

---

Deep Karmakar — AppSec Learning Journey 🔐
