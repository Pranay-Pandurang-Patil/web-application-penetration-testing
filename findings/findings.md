# Security Findings and Validation

## 1. Overview

This document consolidates observations from the authorized security assessment of the Planetorium web application.

Testing covered reconnaissance, HTTP and browser analysis, Burp Suite inspection, OWASP ZAP scanning, source-resource discovery, and initial XSS checks.

The target was running through a Vue/Vite development server. Findings must therefore be interpreted in the context of the development environment and must not automatically be treated as confirmed production vulnerabilities.

## 2. Findings Summary

| ID | Finding | Initial Severity | Status |
|---|---|---|---|
| F-01 | Content Security Policy (CSP) Header Not Set | Informational / Low pending context assessment | Observed; validation pending |
| F-02 | Anti-clickjacking Header Missing | Informational / Low pending context assessment | Observed; validation pending |
| F-03 | X-Content-Type-Options Header Missing | Informational / Low pending context assessment | Observed; validation pending |
| F-04 | Development Resource and Source Metadata Exposure | Informational / Low pending context assessment | Observed; impact assessment pending |
| F-05 | XSS Testing | Not applicable as a confirmed finding | No vulnerability confirmed during limited testing |
| F-06 | Authentication and Session Management | Not applicable to identified functionality | No authentication functionality identified |

**Severity note:** The header observations originate from automated scanning of a development server. Final severity depends on deployment context, exploitability, and security impact. No severity should be considered final until validation is complete.

---

## 3. Detailed Findings

### F-01: Content Security Policy (CSP) Header Not Set

**Initial severity:** Informational / Low pending context assessment  
**Status:** Observed; validation pending  
**Category:** Security configuration

#### Observation

OWASP ZAP reported that the HTTP response did not include a `Content-Security-Policy` header.

#### Evidence

- [ZAP CSP Alert](../owasp-zap/screenshots/02-zap-csp-header-not-set.png)
- [HTTP Response Headers](../web-testing/screenshots/04-devtools-http-headers.png)
- [Burp HTTP Response Headers](../burp-suite/screenshots/08-burp-http-response-headers.png)

#### Security Impact

A suitable Content Security Policy can restrict the sources from which a browser loads scripts, styles, images, and other resources. Depending on the application, it can reduce the impact of certain injection attacks.

The absence of this header alone does not prove that an exploitable vulnerability exists.

#### Recommendation

Define and deploy a Content Security Policy appropriate to the application. Test the policy in report-only mode before enforcing it, and account for legitimate scripts, styles, and other resources.

#### Validation Required

- Confirm the header behavior in the intended production deployment.
- Determine whether the application has other effective content restrictions.
- Assess whether the proposed policy can be deployed without breaking application functionality.

---

### F-02: Anti-clickjacking Header Missing

**Initial severity:** Informational / Low pending context assessment  
**Status:** Observed; validation pending  
**Category:** Security configuration

#### Observation

OWASP ZAP reported a missing anti-clickjacking response header.

#### Evidence

- [ZAP Anti-clickjacking Alert](../owasp-zap/screenshots/03-zap-missing-anti-clickjacking-header.png)
- [HTTP Response Headers](../web-testing/screenshots/04-devtools-http-headers.png)

#### Security Impact

Without appropriate framing restrictions, a web page may be embeddable in another page. In suitable circumstances, an attacker could use framing to deceive users into interacting with the embedded application.

The actual impact depends on the application's functionality and whether framing is required.

#### Recommendation

Use a suitable Content Security Policy `frame-ancestors` directive. Consider `X-Frame-Options` where appropriate for compatibility with older browsers.

#### Validation Required

- Verify framing behavior in the intended deployment.
- Determine whether the application contains sensitive actions or interactions that could be abused through framing.
- Confirm the chosen policy does not interfere with legitimate embedding requirements.

---

### F-03: X-Content-Type-Options Header Missing

**Initial severity:** Informational / Low pending context assessment  
**Status:** Observed; validation pending  
**Category:** Security configuration

#### Observation

OWASP ZAP reported that the `X-Content-Type-Options` response header was missing.

#### Evidence

- [ZAP X-Content-Type-Options Alert](../owasp-zap/screenshots/04-zap-x-content-type-options-missing.png)
- [HTTP Response Headers](../web-testing/screenshots/04-devtools-http-headers.png)
- [Burp HTTP Response Headers](../burp-suite/screenshots/08-burp-http-response-headers.png)

#### Security Impact

Without `X-Content-Type-Options: nosniff`, browsers may perform MIME-type sniffing in situations where the declared content type would otherwise be used. The security implications depend on the resource type and how the application serves its content.

The missing header alone does not demonstrate an exploitable vulnerability.

#### Recommendation

Configure the intended web server or application deployment to return:

`X-Content-Type-Options: nosniff`

Ensure that resources are served with appropriate content types.

#### Validation Required

- Check the header on relevant responses in the intended deployment.
- Confirm that JavaScript, stylesheets, and other resources use correct MIME types.
- Retest after the configuration is updated.

---

### F-04: Development Resource and Source Metadata Exposure

**Initial severity:** Informational / Low pending context assessment  
**Status:** Observed; impact assessment pending  
**Category:** Information disclosure / Development configuration

#### Observation

The Vue/Vite development server exposed application development resources and transformed source files. Inspected responses included:

- `/src/main.js`
- `/src/App.vue`
- `/package.json`
- `/vite.config.js`

Some transformed source responses included local filesystem path metadata and inline source-map data.

The `/src/components/` request returned the application's HTML page rather than a directory listing. A request to `/.env` also returned HTML, which did not establish that an environment file was exposed.

#### Evidence

- [Vue Source Metadata](../web-testing/screenshots/05-vue-source-metadata-disclosure.png)
- [Vue Source Path Confirmation](../web-testing/screenshots/06-vue-source-path-confirmation.png)
- [Vue Component Discovery](../web-testing/screenshots/07-vue-component-discovery.png)
- [Vue Components Directory Check](../web-testing/screenshots/08-vue-components-directory-check.png)
- [Burp Site Map](../burp-suite/screenshots/05-burp-site-map.png)

#### Security Impact

Development resources and filesystem paths can reveal implementation details, project structure, dependency information, and local environment details.

The observed behavior is consistent with a development server. Its presence does not establish that the same resources would be exposed in a production deployment.

No exposed environment secrets were confirmed by these checks.

#### Recommendation

- Do not expose the Vite development server to untrusted users or the public internet.
- Deploy the production build using an appropriately configured production web server.
- Review production source-map publication settings.
- Avoid including sensitive filesystem metadata in publicly served resources.
- Verify that environment files and secrets are not included in deployment artifacts.

#### Validation Required

- Test the production build and intended hosting configuration.
- Verify whether development-only endpoints and transformed source files remain accessible.
- Confirm that sensitive files and secrets are not exposed.

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

#### Evidence

Relevant evidence is available in the existing web-testing documentation and source-inspection screenshots.

No additional screenshot is required for this limited negative result.

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

These observations should only be reclassified if additional evidence demonstrates a security impact.

---

## 5. Validation and Remediation Tracking

| Finding | Validation | Remediation | Retest |
|---|---|---|---|
| F-01: CSP | Pending | Pending | Pending |
| F-02: Anti-clickjacking | Pending | Pending | Pending |
| F-03: X-Content-Type-Options | Pending | Pending | Pending |
| F-04: Development resource exposure | Pending production-context verification | Pending | Pending |
| F-05: XSS | Limited checks completed; no vulnerability confirmed | Not applicable unless a weakness is identified | Not applicable unless a weakness is identified |
| F-06: Authentication/session management | Not applicable to identified functionality | Not applicable | Not applicable |

## 6. Assessment Limitations

- The target was tested with authorization from its owner.
- Testing was performed against a local Vue/Vite development environment.
- Development-server behavior may differ from production behavior.
- Automated scanner alerts were not automatically treated as confirmed vulnerabilities.
- XSS checks were limited and do not prove the absence of all XSS vulnerabilities.
- No application authentication functionality was identified during initial inspection.
- Findings requiring production-context verification remain provisional.
- Evidence screenshots must be sanitized before publication.

## 7. Current Status

**Findings documentation:** Initial consolidation prepared  
**Confirmed exploitable vulnerabilities:** None established by the observations documented here  
**Configuration observations:** Three missing security headers reported by ZAP  
**Development exposure:** Observed; production impact not established  
**Next phase:** Validate applicable findings, document remediation recommendations, and retest any changes.

This document should be updated as findings are manually verified and their impact, severity, and remediation status are established.