# Security Remediation Recommendations

## 1. Overview

This document provides remediation recommendations based on the authorized security assessment of the Planetorium web application.

The recommendations address observed security configuration weaknesses and development-environment exposure.

These are recommendations, not confirmation that changes have been implemented.

## 2. Remediation Summary

| ID | Finding | Recommended Action | Status |
|---|---|---|---|
| F-01 | Content Security Policy Missing | Configure an appropriate CSP | Recommended |
| F-02 | Anti-clickjacking Protection Missing | Configure CSP frame-ancestors or X-Frame-Options | Recommended |
| F-03 | X-Content-Type-Options Missing | Configure nosniff response header | Recommended |
| F-04 | Development Resource Exposure | Use production deployment configuration | Production preview checked; no exposure confirmed |
| F-05 | XSS | No vulnerability confirmed during limited testing | No remediation identified |
| F-06 | Authentication/Session Management | Not applicable to identified functionality | Not applicable |

## 3. Recommended Security Improvements

### F-01: Content Security Policy

**Observation:** The CSP header was absent from the inspected production-preview response.

**Recommendation:**

Configure a Content Security Policy appropriate to the application's resource requirements.

Example starting policy:

`Content-Security-Policy: default-src 'self'; object-src 'none'; base-uri 'self'; frame-ancestors 'none'`

This is an example baseline, not a verified drop-in policy. Test it in report-only mode and adjust it for legitimate application resources before enforcement.

### F-02: Anti-clickjacking Protection

**Observation:** No anti-clickjacking header was present in the inspected production-preview response.

**Recommendation:**

Configure:

`Content-Security-Policy: frame-ancestors 'none'`

Alternatively, where compatibility requires it:

`X-Frame-Options: DENY`

Choose the policy according to legitimate application embedding requirements.

### F-03: X-Content-Type-Options

**Observation:** The response did not include the X-Content-Type-Options header.

**Recommendation:**

Configure:

`X-Content-Type-Options: nosniff`

Ensure that application resources are served using their correct MIME types.

### F-04: Development Resource Exposure

**Observation:** Development resources and transformed Vue source files were accessible through the Vite development server.

The inspected production preview returned the application HTML fallback for selected development-resource paths. No standalone source-map files or matching local filesystem paths were identified in the production bundle checks.

**Recommendation:**

- Use the production build for deployment.
- Do not expose the Vite development server to untrusted users.
- Review source-map publication settings.
- Keep environment files and secrets outside public deployment artifacts.
- Verify the final hosting configuration independently.

## 4. Additional Security Recommendations

- Use HTTPS in the actual production deployment.
- Evaluate HSTS after HTTPS is configured.
- Consider an appropriate Referrer-Policy.
- Configure Permissions-Policy according to required browser features.
- Review security headers on all relevant application responses.
- Reassess authentication, authorization, CSRF, and API security if corresponding functionality is introduced.

## 5. Implementation Status

No application-side remediation has been implemented or verified as part of this assessment.

The recommendations remain pending implementation by the application owner.

## 6. Retesting Plan

After authorized remediation:

- Verify the CSP response header and browser behavior.
- Verify anti-clickjacking protection.
- Verify X-Content-Type-Options.
- Recheck development-resource accessibility.
- Confirm that legitimate application functionality still works.
- Record before-and-after evidence.

## 7. Status

**Remediation documentation:** Completed

**Application remediation:** Pending application-owner implementation

**Next phase:** Retesting and final penetration testing report.