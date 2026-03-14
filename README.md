# Complete Guide: HTTP Security Headers

**🛡️ What are HTTP Security Headers?** HTTP security headers are a fundamental part of website security. They instruct browsers how to behave when handling your site's content, protecting against attacks like clickjacking, XSS, and ensuring secure connections.

All Headers

X-Frame-Options

HSTS

Referrer-Policy

Permissions-Policy

CSP

## HTTP Security Headers Overview

| Header | Protection | Security Level | Configuration Difficulty |
| --- | --- | --- | --- |
| **X-Frame-Options** | Clickjacking protection | Essential | Easy |
| **Strict-Transport-Security (HSTS)** | SSL stripping attacks | Essential | Easy |
| **Referrer-Policy** | Privacy, referrer leakage | Recommended | Easy |
| **Permissions-Policy** | Browser feature control | Recommended | Medium |
| **Content-Security-Policy (CSP)** | XSS, data injection | Advanced | Complex |

### Complete Security Headers Configuration

Here's a comprehensive security headers setup for maximum protection:

\# NGINX Complete Security Headers add\_header X-Frame-Options "SAMEORIGIN" always; add\_header X-Content-Type-Options "nosniff" always; add\_header X-XSS-Protection "1; mode=block" always; add\_header Referrer-Policy "strict-origin-when-cross-origin" always; add\_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always; add\_header Permissions-Policy "geolocation=(), microphone=(), camera=()" always; add\_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline';" always;

\# Apache Complete Security Headers Header always set X-Frame-Options "SAMEORIGIN" Header always set X-Content-Type-Options "nosniff" Header always set X-XSS-Protection "1; mode=block" Header always set Referrer-Policy "strict-origin-when-cross-origin" Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()" Header always set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline';"

\# .htaccess Complete Security Headers <IfModule mod\_headers.c> Header set X-Frame-Options "SAMEORIGIN" Header set X-Content-Type-Options "nosniff" Header set X-XSS-Protection "1; mode=block" Header set Referrer-Policy "strict-origin-when-cross-origin" Header set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" Header set Permissions-Policy "geolocation=(), microphone=(), camera=()" Header set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval'; style-src 'self' 'unsafe-inline';" </IfModule>

**📌 Note:** Always test security headers in a staging environment first. CSP in particular can break site functionality if not configured correctly.

🖼️ X-Frame-Options

**What is X-Frame-Options?** The X-Frame-Options HTTP header tells the browser whether your site can be displayed in `<frame>`, `<iframe>`, or `<embed>` elements. This prevents clickjacking attacks where malicious sites load your pages in invisible frames to trick users.

### Directives

| Directive | Description |
| --- | --- |
| deny | The page cannot be displayed in any frame, regardless of the requesting site. |
| sameorigin | The page can only be displayed if all parent frames are on the same domain. |
| allow-from https://domain.com | Allows loading only from specified URL (⚠️ Deprecated in modern browsers). |

**⚠️ Deprecation Warning:** The `allow-from` directive is no longer supported in many browsers. For modern sites, use `Content-Security-Policy` with the `frame-ancestors` directive instead.

### CSP Alternative for Frame Control

\# Allow framing only from same origin and specific domains add\_header Content-Security-Policy "frame-ancestors 'self' https://domain1.com https://domain2.com";

### NGINX Configuration

\# In nginx.conf or site config add\_header X-Frame-Options SAMEORIGIN always; # Or for deny add\_header X-Frame-Options DENY always;

### Apache Configuration

\# In httpd.conf or virtual host Header always set X-Frame-Options "SAMEORIGIN" # Or for deny Header always set X-Frame-Options "DENY"

### .htaccess Configuration

<IfModule mod\_headers.c> Header set X-Frame-Options "SAMEORIGIN" </IfModule>

### PHP Configuration

<?php // Set X-Frame-Options header in PHP header("X-Frame-Options: SAMEORIGIN"); ?>

### Testing Your Configuration

\# Check headers using curl curl -I https://yourdomain.com | grep -i x-frame # Or visit your site and check Developer Tools → Network tab → Response Headers

🔒 Strict-Transport-Security (HSTS)

**What is HSTS?** Strict-Transport-Security tells the browser to always load your site via HTTPS, never HTTP. This protects against SSL stripping attacks where attackers force a downgrade to insecure HTTP.

**⚠️ Prerequisite:** HSTS will only work if your site is accessible via HTTPS and the SSL certificate is installed without errors. The site must never load via HTTP once HSTS is enabled.

### Directives

| Directive | Description |
| --- | --- |
| max-age=SECONDS | Tells the browser how long (in seconds) to remember that the site should only be accessed via HTTPS. `31536000` = 1 year. |
| includeSubDomains | Applies HSTS not only to the main domain but to all subdomains as well. |
| preload | Indicates that the site has been added to the HSTS preload list (hardcoded into browsers). |

### HSTS Preload

**🔐 Preload List:** Sites can be added to [hstspreload.org](https://hstspreload.org/) to be hardcoded into browsers. Once added, your site will **never** be accessible via HTTP, even on first visit.

### NGINX Configuration

\# Basic HSTS (1 year, main domain only) add\_header Strict-Transport-Security "max-age=31536000" always; # HSTS with subdomains add\_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always; # HSTS with preload (for submission to preload list) add\_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

### Apache Configuration

\# Basic HSTS Header always set Strict-Transport-Security "max-age=31536000" # With subdomains Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains" # With preload Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains; preload"

### .htaccess Configuration

<IfModule mod\_headers.c> Header set Strict-Transport-Security "max-age=31536000; includeSubDomains" </IfModule>

### PHP Configuration

<?php // Set HSTS header in PHP header("Strict-Transport-Security: max-age=31536000; includeSubDomains; preload"); ?>

### Testing HSTS

\# Check HSTS header curl -I https://yourdomain.com | grep -i strict # Visit chrome://net-internals/#hsts in Chrome to query HSTS status

🔗 Referrer-Policy

**What is Referrer-Policy?** The Referrer-Policy header controls how much referrer information (the previous page URL) is sent when users navigate from your site to other sites. This protects user privacy and prevents sensitive data leakage.

### Directives

| Directive | Description |
| --- | --- |
| no-referrer | Never send referrer information with requests. |
| no-referrer-when-downgrade | Send referrer when staying on same protocol (HTTPS→HTTPS or HTTP→HTTP). **This is the browser default.** |
| origin | Send only the origin (protocol + domain), not the full path. |
| origin-when-cross-origin | Send full referrer for same origin, only origin for cross-origin requests. |
| same-origin | Send referrer only for same-origin requests. |
| strict-origin | Send origin only when protocol stays the same (HTTPS→HTTPS). |
| strict-origin-when-cross-origin | Send full referrer for same-origin, origin for cross-origin HTTPS→HTTPS, nothing for HTTPS→HTTP. |
| unsafe-url | Send full referrer always (most permissive, least private). |

### Recommended Policy

**✅ Best Practice:** `strict-origin-when-cross-origin` provides the best balance of functionality and privacy.

### NGINX Configuration

\# Recommended setting add\_header Referrer-Policy 'strict-origin-when-cross-origin' always; # Other options add\_header Referrer-Policy 'no-referrer-when-downgrade' always; add\_header Referrer-Policy 'same-origin' always;

### Apache Configuration

Header set Referrer-Policy "strict-origin-when-cross-origin" Header set Referrer-Policy "no-referrer-when-downgrade"

### .htaccess Configuration

<IfModule mod\_headers.c> Header set Referrer-Policy "strict-origin-when-cross-origin" </IfModule>

### PHP Configuration

<?php header("Referrer-Policy: strict-origin-when-cross-origin"); ?>

📱 Permissions-Policy

**What is Permissions-Policy?** Formerly known as Feature-Policy, this header controls which browser features and APIs can be used by your site. This improves privacy and security by disabling unnecessary features like camera, microphone, and geolocation.

### Key Directives

| Directive | Controls access to |
| --- | --- |
| `accelerometer` | Device accelerometer (motion sensors) |
| `autoplay` | Automatic video/audio playback |
| `camera` | Device camera |
| `fullscreen` | Fullscreen mode |
| `geolocation` | GPS/location services |
| `gyroscope` | Device gyroscope |
| `magnetometer` | Device compass |
| `microphone` | Device microphone |
| `payment` | Payment Request API |
| `usb` | USB devices |

### Syntax Examples

\# Disable specific features for all origins Permissions-Policy: geolocation=(), microphone=(), camera=() # Allow only for same origin Permissions-Policy: geolocation=(self) # Allow for specific domains Permissions-Policy: geolocation=(self "https://trusted.com") # Allow all (default) Permissions-Policy: geolocation=\*

### NGINX Configuration

\# Disable camera, microphone, and geolocation completely add\_header Permissions-Policy "geolocation=(), microphone=(), camera=()" always; # Disable multiple features add\_header Permissions-Policy "accelerometer=(), autoplay=(), camera=(), document-domain=(), encrypted-media=(), fullscreen=(), geolocation=(), gyroscope=(), magnetometer=(), microphone=(), midi=(), payment=(), picture-in-picture=(), publickey-credentials-get=(), screen-wake-lock=(), sync-xhr=(), usb=(), web-share=(), xr-spatial-tracking=()" always;

### Apache Configuration

Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"

### .htaccess Configuration

<IfModule mod\_headers.c> Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()" </IfModule>

🛡️ Content-Security-Policy (CSP)

**What is CSP?** Content-Security-Policy is a powerful security header that helps prevent cross-site scripting (XSS), clickjacking, and other code injection attacks. It allows you to create a whitelist of trusted sources for content like scripts, styles, images, and fonts.

### Key Directives

| Directive | Controls |
| --- | --- |
| `default-src` | Fallback for other fetch directives |
| `script-src` | JavaScript sources |
| `style-src` | CSS stylesheet sources |
| `img-src` | Image sources |
| `font-src` | Font sources |
| `connect-src` | AJAX, WebSocket, EventSource |
| `media-src` | Audio/video sources |
| `frame-src` | Frame/iframe sources |
| `object-src` | Plugin sources (Flash, Java) |
| `frame-ancestors` | Who can embed the page (CSP alternative to X-Frame-Options) |

### Source Values

| Value | Meaning |
| --- | --- |
| `'none'` | Block everything |
| `'self'` | Allow from same origin |
| `'unsafe-inline'` | Allow inline scripts/styles (avoid if possible) |
| `'unsafe-eval'` | Allow eval() (avoid if possible) |
| `https://domain.com` | Allow from specific domain |
| `*.domain.com` | Allow from domain and subdomains |
| `https:` | Allow only over HTTPS |
| `'nonce-abc123'` | Allow specific inline scripts with matching nonce |
| `'sha256-abc123'` | Allow scripts matching hash |

### CSP Examples

#### Basic CSP (Self Only)

Content-Security-Policy: default-src 'self'; # Only allows content from your own domain

#### Typical WordPress CSP

Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline' 'unsafe-eval' https://ajax.googleapis.com; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; font-src 'self' https://fonts.gstatic.com; img-src 'self' data: https:

#### Strict CSP (Recommended)

Content-Security-Policy: default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self' data:; font-src 'self'; connect-src 'self'; frame-ancestors 'self'; form-action 'self';

#### With Nonce for Inline Scripts

\# Header Content-Security-Policy: script-src 'nonce-r4nd0m' # HTML <script nonce="r4nd0m"> console.log('This script will execute'); </script>

### NGINX CSP Configuration

\# Basic CSP add\_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';" always; # Advanced CSP add\_header Content-Security-Policy " default-src 'none'; script-src 'self' https://www.google-analytics.com; style-src 'self' 'unsafe-inline'; img-src 'self' data: https:; font-src 'self'; connect-src 'self'; frame-ancestors 'self'; form-action 'self'; base-uri 'self'; " always;

### Apache CSP Configuration

Header always set Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';"

### .htaccess CSP Configuration

<IfModule mod\_headers.c> Header set Content-Security-Policy "default-src 'self'" </IfModule>

### Testing CSP

**🧪 CSP Testing Mode:** Use `Content-Security-Policy-Report-Only` to test policies without blocking:

\# Test only (logs violations but doesn't block) add\_header Content-Security-Policy-Report-Only "default-src 'self'; report-uri /csp-report-endpoint/" always;

### CSP Report URI

\# Send violation reports to an endpoint Content-Security-Policy: default-src 'self'; report-uri https://yourdomain.com/csp-report # Or use a service like https://report-uri.com/

## Security Headers Checker Tools

| Tool | Description |
| --- | --- |
| [securityheaders.com](https://securityheaders.com/) | Analyzes your site's security headers and gives a grade (A+, A, etc.) |
| [Google CSP Evaluator](https://csp-evaluator.withgoogle.com/) | Evaluates your CSP policy for common mistakes |
| [HSTS Preload](https://hstspreload.org/) | Check HSTS status and submit to preload list |
| Browser DevTools | Network tab → Response Headers shows all headers |

## Quick Reference: All Headers

| Header | Recommended Value |
| --- | --- |
| X-Frame-Options | `SAMEORIGIN` |
| Strict-Transport-Security | `max-age=31536000; includeSubDomains; preload` |
| X-Content-Type-Options | `nosniff` |
| X-XSS-Protection | `1; mode=block` |
| Referrer-Policy | `strict-origin-when-cross-origin` |
| Permissions-Policy | `geolocation=(), microphone=(), camera=()` |
| Content-Security-Policy | `default-src 'self'; script-src 'self'; style-src 'self'; img-src 'self' data:; font-src 'self'; connect-src 'self'; frame-ancestors 'self';` |

**⚠️ Implementation Warning:**

*   Always test headers in a development environment first
*   Use CSP report-only mode initially to identify issues
*   Monitor your site after implementing headers for any functionality breaks
*   HSTS with preload is permanent - be absolutely sure before enabling
*   Document your header configuration for future reference

**📚 Additional Resources:**

*   [MDN HTTP Headers Documentation](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
*   [OWASP Secure Headers Project](https://owasp.org/www-project-secure-headers/)
*   [Google CSP Guide](https://csp.withgoogle.com/docs/index.html)
