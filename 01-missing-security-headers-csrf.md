Passive Security Findings Report

Missing Security Headers, Unprotected Search Form & Outdated jQuery

Author: Deep Karmakar
Assessment Type: Passive Security Review
Method: Browser source inspection + public scanning tools
Testing Type: Non-intrusive (No exploitation performed)
Date: March 2026
Status: Pending Responsible Disclosure

---

Finding 1 — Missing HTTP Security Headers

Description

During passive analysis of the target websites, HTTP response headers were inspected using the public scanning tool SecurityHeaders.com.

The scan returned a Grade F, indicating that several recommended security headers are missing. These headers are important for enabling browser-side security protections and reducing the risk of common web attacks.

Missing Security Headers

Header| Risk
X-Frame-Options| Allows clickjacking attacks by embedding the site inside attacker-controlled iframes
Content-Security-Policy| No browser-level protection against Cross-Site Scripting (XSS)
Strict-Transport-Security| Users may be vulnerable to HTTPS downgrade or man-in-the-middle attacks
X-Content-Type-Options| Allows MIME-type sniffing attacks
Referrer-Policy| Sensitive URL information may leak to third-party websites
Permissions-Policy| Browser features such as camera, microphone, or location may be abused

Impact

The absence of these headers weakens browser security controls.

For example, without the X-Frame-Options header, an attacker could embed the website inside a malicious iframe and trick users into clicking hidden elements. This technique, known as Clickjacking, could potentially lead to credential theft, unauthorized actions, or phishing attacks targeting students or staff.

Recommended Fix

Implement the following security headers on all HTTP responses:

Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-Content-Type-Options "nosniff"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
Header always set Content-Security-Policy "default-src 'self'; object-src 'none';"
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"

---

Finding 2 — Search Form Using GET Method

Description

During source code inspection, the website search functionality was found to use the GET method without any request validation or token protection.

Example form:

<form method="GET" action="en.php">
    <input name="linktitle" placeholder="Search...">
    <input type="submit" value="Search">
</form>

Risk

Using the GET method for search functionality exposes user input in multiple locations:

- Browser history
- Server logs
- Referrer headers
- URL sharing

This increases the risk of sensitive information exposure and may expand the attack surface for injection vulnerabilities if the parameter is improperly handled by the backend.

Impact

User-supplied search queries are directly appended to the URL. If not properly sanitized on the server side, this could potentially lead to reflected input vulnerabilities or information leakage through logs and analytics systems.

Recommended Fix

Use POST requests and implement proper input validation:

<form method="POST" action="/search">
    <input name="query" placeholder="Search...">
    <button type="submit">Search</button>
</form>

Additionally, ensure server-side validation and sanitization of all user input.

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
