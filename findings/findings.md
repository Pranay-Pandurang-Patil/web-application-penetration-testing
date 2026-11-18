# Security Findings and Validation

## 1. Overview

This document consolidates observations from the authorized security assessment of the Planetorium web application.

Testing covered reconnaissance, HTTP and browser analysis, Burp Suite inspection, OWASP ZAP scanning, source-resource discovery, initial XSS checks, and production-build assessment.

The application was assessed using a Vue/Vite development server and a locally hosted production preview. Findings must be interpreted within these tested environments. The local production preview does not represent a full assessment of the final public hosting environment.

## 2. Findings Summary

| ID | Finding | Initial Severity | Status |
|---|---|---|---|
| F-01 | Content Security Policy (CSP) Header Not Set | Informational / Low pending context assessment | Observed in development and production preview |
| F-02 | Anti-clickjacking Header Missing | Informational / Low pending context assessment | Observed in development and production preview |
| F-03 | X-Content-Type-Options Header Missing | Informational / Low pending context assessment | Observed in development and production preview |
| F-04 | Development Resource and Source Metadata Exposure | Informational / Low pending context assessment | Observed in development; not reproduced in selected production-preview checks |
| F-05 | XSS Testing | Not applicable as a confirmed finding | No vulnerability confirmed during limited testing |
| F-06 | Authentication and Session Management | Not applicable to identified functionality | No authentication functionality identified |

**Severity note:** Missing security headers are configuration observations. Their actual severity depends on deployment context, application functionality, exploitability, and security impact. No confirmed exploitable vulnerability was established by the documented testing.

---

## 3. Detailed Findings

### F-01: Content Security Policy (CSP) Header Not Set

**Initial severity:** Informational / Low pending context assessment  
**Status:** Observed in development and local production-preview responses  
**Category:** Security configuration

#### Observation

OWASP ZAP reported that the HTTP response did not include a `Content-Security-Policy` header.

The missing header was also observed during the local production-preview HTTP response-header assessment.

#### Evidence

- [ZAP CSP Alert](../owasp-zap/screenshots/02-zap-csp-header-not-set.png)
- [Development HTTP Response Headers](../web-testing/screenshots/04-devtools-http-headers.png)
- [Burp HTTP Response Headers](../burp-suite/screenshots/08-burp-http-response-headers.png)
- [Production Preview Security Headers](../web-testing/screenshots/12-production-security-headers.png)

#### Security Impact

A suitable Content Security Policy can restrict the sources from which a browser loads scripts, styles, images, and other resources. Depending on the application, it can reduce the impact of certain injection attacks.

The absence of this header alone does not prove that an exploitable vulnerability exists.

#### Recommendation

Define and deploy a Content Security Policy appropriate to the application. Test the policy in report-only mode before enforcing it, and account for legitimate scripts, styles, and other resources.

#### Validation

- Missing CSP header observed in the development environment.
- Missing CSP header also observed in the local production preview.
- The final public hosting environment was not independently assessed.

---

### F-02: Anti-clickjacking Header Missing

**Initial severity:** Informational / Low pending context assessment  
**Status:** Observed in development and local production-preview responses  
**Category:** Security configuration

#### Observation

OWASP ZAP reported a missing anti-clickjacking response header.

The corresponding framing-protection header was also absent in the inspected local production-preview response.

#### Evidence

- [ZAP Anti-clickjacking Alert](../owasp-zap/screenshots/03-zap-missing-anti-clickjacking-header.png)
- [Development HTTP Response Headers](../web-testing/screenshots/04-devtools-http-headers.png)
- [Production Preview Security Headers](../web-testing/screenshots/12-production-security-headers.png)

#### Security Impact

Without appropriate framing restrictions, a web page may be embeddable in another page. In suitable circumstances, an attacker could use framing to deceive users into interacting with the embedded application.

The actual impact depends on the application's functionality and whether framing is required.

#### Recommendation

Use a suitable Content Security Policy `frame-ancestors` directive. Consider `X-Frame-Options` where appropriate for compatibility with older browsers.

#### Validation

- Missing anti-clickjacking protection observed in the development environment.
- Missing protection also observed in the local production preview.
- The final public hosting environment was not independently assessed.

---

### F-03: X-Content-Type-Options Header Missing

**Initial severity:** Informational / Low pending context assessment  
**Status:** Observed in development and local production-preview responses  
**Category:** Security configuration

#### Observation

OWASP ZAP reported that the `X-Content-Type-Options` response header was missing.

The header was also absent in the inspected local production-preview response.

#### Evidence

- [ZAP X-Content-Type-Options Alert](../owasp-zap/screenshots/04-zap-x-content-type-options-missing.png)
- [Development HTTP Response Headers](../web-testing/screenshots/04-devtools-http-headers.png)
- [Burp HTTP Response Headers](../burp-suite/screenshots/08-burp-http-response-headers.png)
- [Production Preview Security Headers](../web-testing/screenshots/12-production-security-headers.png)

#### Security Impact

Without `X-Content-Type-Options: nosniff`, browsers may perform MIME-type sniffing in situations where the declared content type would otherwise be used.

The security implications depend on the resource type and how the application serves its content. The missing header alone does not demonstrate an exploitable vulnerability.

#### Recommendation

Configure the intended web server or application deployment to return:

`X-Content-Type-Options: nosniff`

Ensure that resources are served with appropriate content types.

#### Validation

- Missing header observed in the development environment.
- Missing header also observed in the local production preview.
- The final public hosting environment was not independently assessed.

---

### F-04: Development Resource and Source Metadata Exposure

**Initial severity:** Informational / Low pending context assessment  
**Status:** Observed in development; not reproduced in selected production-preview checks  
**Category:** Information disclosure / Development configuration

#### Observation

The Vue/Vite development server exposed application development resources and transformed source files. Inspected responses included:

- `/src/main.js`
- `/src/App.vue`
- `/package.json`
- `/vite.config.js`

Some transformed source responses included local filesystem path metadata and inline source-map data.

The `/src/components/` request returned the application's HTML page rather than a directory listing. A request to `/.env` also returned HTML, which did not establish that an environment file was exposed.

A production build was generated successfully. The generated build did not contain the inspected `.map` source-map files or the Vite development-client reference.

Selected production-preview requests for development-related paths returned the normal HTML application page rather than the requested development resources.

#### Evidence

- [Vue Source Metadata](../web-testing/screenshots/05-vue-source-metadata-disclosure.png)
- [Vue Source Path Confirmation](../web-testing/screenshots/06-vue-source-path-confirmation.png)
- [Vue Component Discovery](../web-testing/screenshots/07-vue-component-discovery.png)
- [Vue Components Directory Check](../web-testing/screenshots/08-vue-components-directory-check.png)
- [Burp Site Map](../burp-suite/screenshots/05-burp-site-map.png)
- [Production Preview Security Headers](../web-testing/screenshots/12-production-security-headers.png)

#### Security Impact

Development resources and filesystem paths can reveal implementation details, project structure, dependency information, and local environment details.

The observed behavior was consistent with a development server. The selected production-preview checks did not reproduce the same development-resource exposure.

No exposed environment secrets were confirmed by these checks.

#### Recommendation

- Do not expose the Vite development server to untrusted users or the public internet.
- Deploy the production build using an appropriately configured production web server.
- Review production source-map publication settings.
- Avoid including sensitive filesystem metadata in publicly served resources.
- Verify that environment files and secrets are not included in deployment artifacts.

#### Validation

- Development-resource exposure confirmed in the development environment.
- Production build generated successfully.
- No `.map` files were identified in the inspected build output.
- Selected development-resource paths did not reproduce the exposure in the local production preview.
- The final public hosting environment was not independently assessed.

---

### F-05: Cross-Site Scripting (XSS) Checks

**Status:** No vulnerability confirmed during limited testing  
**Category:** Input handling

#### Tests Performed

- Checked whether a query parameter was reflected in the initial HTML response.
- Read a test query parameter through the browser's URL API.
- Searched loaded source files for `innerHTML`, excluding identified dependency paths from the search.

#### Results

- Reflection of the test parameter in the initial HTML response was not observed.
- Reading the query parameter through the browser's URL API worked normally.
- The filtered search did not visibly identify `innerHTML` usage in application-owned source files.
- Remaining visible matches were in Vue DevTools and installed dependencies.

#### Assessment

No XSS vulnerability was confirmed during these checks. These tests were limited and do not establish that the application is free from all XSS vulnerabilities.

#### Recommendation

Continue testing only if further review identifies application-controlled input that reaches a potentially unsafe rendering operation.

---

### F-06: Authentication and Session Management

**Status:** Not applicable to the functionality identified during initial inspection  
**Category:** Authentication / Session management

#### Observation

No login, registration, account-management, or other application authentication functionality was identified during the initial assessment.

The tested main-page response returned `HTTP 200 OK`. No `Set-Cookie` or `Location` header was observed in the inspected response.

Browser storage inspection found Vue DevTools interface preferences in Local Storage. No entries were observed in Cookies, Session Storage, or IndexedDB during that inspection.

#### Assessment

No application authentication or session-management vulnerability was established. The absence of identified authentication functionality limits the scope of these checks.

#### Recommendation

Reassess authentication and session security if account functionality or authenticated endpoints are introduced or discovered.

---

## 4. Informational Alerts Not Classified as Confirmed Vulnerabilities

The following observations are not treated as confirmed vulnerabilities based on the available evidence:

- A `token` parameter in the Vite development WebSocket URL. The observed behavior was consistent with Vite Hot Module Replacement (HMR); no application authentication-token exposure was established.
- Vue DevTools Local Storage entries, which appeared to contain development-tool preferences.
- Suspicious-comment alerts containing comments that were not demonstrated to expose sensitive information.
- Supported HTTP methods reported by an OPTIONS response. The method list alone does not demonstrate unauthorized access or a vulnerability.
- A request to `/.env` returning HTML. This did not confirm exposure of an environment file.
- A ZAP email alert caused by a CSS `@keyframes` match, which was assessed as a false positive.
- Informational alerts related to the Vite development environment.

These observations should only be reclassified if additional evidence demonstrates a security impact.

---

## 5. Validation and Remediation Tracking

| Finding | Validation | Remediation | Retest |
|---|---|---|---|
| F-01: CSP | Missing in development and local production preview | Recommendation documented; not implemented | Not performed |
| F-02: Anti-clickjacking | Missing in development and local production preview | Recommendation documented; not implemented | Not performed |
| F-03: X-Content-Type-Options | Missing in development and local production preview | Recommendation documented; not implemented | Not performed |
| F-04: Development resource exposure | Observed in development; not reproduced in selected production-preview checks | Recommendation documented; not implemented | Not performed |
| F-05: XSS | Limited checks completed; no vulnerability confirmed | Not applicable unless a weakness is identified | Not applicable |
| F-06: Authentication/session management | Not applicable to identified functionality | Not applicable | Not applicable |

**Important:** No application-side remediation was implemented as part of this assessment. Post-remediation retesting was therefore not performed.

---

## 6. Assessment Limitations

- The target was tested with authorization from its owner.
- Testing was performed against a local Vue/Vite development environment and a local production preview.
- The final public hosting environment was not independently assessed.
- Development-server behavior may differ from production behavior.
- Automated scanner alerts were not automatically treated as confirmed vulnerabilities.
- XSS checks were limited and do not prove the absence of all XSS vulnerabilities.
- No application authentication functionality was identified during initial inspection.
- No application-side remediation was implemented.
- No post-remediation retesting was performed.
- Evidence screenshots must be sanitized before publication.

---

## 7. Current Status

**Findings documentation:** Updated with development and production-preview validation results  
**Confirmed exploitable vulnerabilities:** None established by the documented observations  
**Configuration observations:** Three missing security headers observed in development and local production-preview responses  
**Development exposure:** Observed in development; not reproduced in selected production-preview checks  
**Remediation recommendations:** Documented  
**Application-side remediation:** Not implemented  
**Post-remediation retesting:** Not performed  
**Assessment status:** Completed within the documented scope