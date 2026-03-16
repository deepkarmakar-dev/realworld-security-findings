Security Finding — Educational Institution Websites

Researcher: Deep Karmakar
Assessment Type: Passive Security Review
Method: Browser source inspection + securityheaders.com scan
Testing Type: Non-intrusive (no exploitation performed)

---

Finding 1 — Missing Security Headers

During passive testing of two college websites, several important HTTP security headers were missing.
The scan using securityheaders.com resulted in a Grade F.

Missing headers:

- X-Frame-Options → Clickjacking risk
- Content-Security-Policy → No protection against XSS
- Strict-Transport-Security → HTTPS downgrade risk
- X-Content-Type-Options → MIME sniffing risk
- Referrer-Policy → Possible information leakage
- Permissions-Policy → Browser feature misuse

Recommended Fix:

Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-Content-Type-Options "nosniff"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
Header always set Content-Security-Policy "default-src 'self'; object-src 'none';"
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"

---

Finding 2 — Missing CSRF Token on Search Form

The website search form does not contain a CSRF protection token.

Example:

<form method="GET" action="en.php">
    <input name="linktitle" placeholder="Search...">
    <input type="submit" value="Search">
</form>Risk:

Without CSRF protection, requests could potentially be triggered from external websites.

Recommended Fix (Laravel example):

<form method="POST" action="/search">
    @csrf
    <input name="query" placeholder="Search...">
    <button type="submit">Search</button>
</form>---

Tools Used

- Browser Developer Tools
- securityheaders.com

---

Note

This was a passive security observation only.
No exploitation attempts were made and no data was accessed.
